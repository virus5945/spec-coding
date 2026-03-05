# Feature Spec: CRM Contact Merge

**Author:** Jamie Kowalski
**Date:** 2025-11-12
**Status:** Approved
**Reviewers:** Priya Shankar, Marco Ellis, Dana Yoon

---

## Goal

Let sales reps merge two duplicate contacts into one, preserving all activity history and deal associations, so they stop wasting time managing the same person across multiple records.

## Non-goals

- Automated duplicate detection or merge suggestions. That's a separate project (see PROJ-412).
- Bulk merge (selecting 10+ contacts and merging at once). We'll handle one pair at a time for now.
- Merging contacts across different organizations. If two contacts belong to different orgs, the rep needs to reassign first.
- Merging companies or deals. This spec is contacts only.

## Background

Sales reps have been filing support tickets about duplicate contacts since Q2. We have roughly 23,000 duplicate pairs in production based on matching email addresses alone -- the real number is higher when you account for name variations and typos.

Right now, the workaround is to manually copy notes from one contact to another and then delete the duplicate. This loses activity history (emails, calls, meetings) tied to the deleted contact. Three reps reported losing deal context this way in October, and one lost a renewal because the activity timeline was incomplete.

We looked at third-party merge tools, but they all require API access patterns we don't support yet (batch activity reassignment). Building this in-house is straightforward since we control the data model.

## Detailed design

### Data model changes

New table to track merge history:

```sql
CREATE TABLE contact_merges (
  id            BIGINT PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  kept_id       BIGINT NOT NULL REFERENCES contacts(id),
  removed_id    BIGINT NOT NULL,
  removed_snapshot JSONB NOT NULL,
  merged_by     BIGINT NOT NULL REFERENCES users(id),
  merged_at     TIMESTAMPTZ NOT NULL DEFAULT now()
);

CREATE INDEX idx_contact_merges_kept ON contact_merges(kept_id);
CREATE INDEX idx_contact_merges_removed ON contact_merges(removed_id);
```

The `removed_snapshot` column stores a full JSON copy of the removed contact at merge time. This is the rollback mechanism.

### API changes

| Method | Path | Description |
|---|---|---|
| POST | /api/v1/contacts/merge | Merge two contacts into one |
| GET | /api/v1/contacts/:id/merge-history | List past merges for a contact |
| POST | /api/v1/contacts/merge/:merge_id/undo | Undo a merge within 30 days |

**POST /api/v1/contacts/merge**

```json
{
  "keep_contact_id": 4501,
  "remove_contact_id": 4523,
  "field_overrides": {
    "phone": "+1-555-0142",
    "title": "VP of Engineering"
  }
}
```

The `field_overrides` object lets the rep pick which value wins when the two contacts have conflicting fields. Any field not in `field_overrides` keeps the value from the `keep_contact_id` record.

**Response (200)**

```json
{
  "merged_contact_id": 4501,
  "merge_id": 881,
  "reassigned": {
    "deals": 2,
    "activities": 14,
    "notes": 7
  }
}
```

### UI changes

**Merge button:** Add a "Merge with another contact" action to the contact detail page's overflow menu (...).

**Merge dialog:** A two-panel view showing both contacts side by side. For each conflicting field, the rep clicks which value to keep. Non-conflicting fields are pre-selected.

**Confirmation step:** After field selection, show a summary: "This will merge Contact B into Contact A. 14 activities and 2 deals will be reassigned. This can be undone within 30 days."

**Post-merge banner:** On the kept contact's page, show a dismissible banner: "This contact was merged with [removed contact name] on [date]. Undo merge."

### Key implementation details

The merge operation runs in a single transaction:

1. Snapshot the removed contact to `contact_merges.removed_snapshot`.
2. Apply `field_overrides` to the kept contact.
3. Reassign all activities, notes, and deal associations from removed to kept.
4. Soft-delete the removed contact (set `deleted_at`).
5. Insert the merge record.

We're using soft delete so the undo operation can restore the contact without regenerating an ID.

## Acceptance criteria

```gherkin
Given two contacts exist with the same email address
When a sales rep selects "Merge with another contact" on Contact A
  and picks Contact B as the duplicate
  and chooses field values for any conflicts
  and confirms the merge
Then Contact B is removed and all its deals, activities, and notes appear under Contact A

Given a merge was completed 15 days ago
When the rep clicks "Undo merge" on the kept contact's page
Then both contacts are restored to their pre-merge state
  and all reassigned activities move back to their original contact

Given a merge was completed 45 days ago
When the rep views the kept contact's page
Then the "Undo merge" option is no longer available

Given a sales rep is viewing the merge dialog
When Contact A has phone "+1-555-0100" and Contact B has phone "+1-555-0142"
Then both values are shown side by side and the rep must pick one before confirming
```

## Edge cases

| Scenario | Expected behavior |
|---|---|
| Both contacts are owners on the same deal | The kept contact remains as deal owner; no duplicate association is created |
| The removed contact is the primary contact on a deal | The kept contact becomes the primary contact on that deal |
| Contacts have different owners (sales reps) | The merge proceeds, but the removed contact's owner gets an email notification that their contact was merged |
| A contact has already been merged once before | Allowed. The merge history shows the full chain. Undo only reverses the most recent merge |
| Rep tries to merge a contact with itself | API returns 422. UI disables the confirm button if both sides are the same contact |
| Both contacts have upcoming scheduled activities | All activities transfer. No activities are deleted or duplicated |

## Error handling

| Error condition | Response | User impact |
|---|---|---|
| One of the contacts was deleted between page load and merge | 404 with message "Contact not found" | Dialog closes, rep sees an error toast: "One of these contacts no longer exists. Refresh and try again." |
| Rep doesn't have edit permission on both contacts | 403 | Merge button is hidden in the UI. API returns "You need edit access to both contacts to merge them." |
| Merge transaction fails mid-way (e.g., DB timeout) | 500, transaction rolls back | Rep sees "Something went wrong. No changes were made. Try again." |
| Undo attempted after 30-day window | 422 with message "Undo window has expired" | Undo button is hidden, but if they hit the API directly they get a clear message |

## Rollback plan

If we need to pull this feature entirely after release:

1. Disable the merge UI behind the existing feature flag (`crm.contact_merge`).
2. Any merges already completed stay in place -- undoing them all automatically is too risky.
3. If specific merges need reversal, use the undo endpoint or a support script that reads from `contact_merges.removed_snapshot`.
4. The `contact_merges` table stays in the schema. We can drop it later in a cleanup migration.

## Open questions

- [x] Do we need to merge custom fields too? **Yes -- confirmed with Priya. Custom fields follow the same override logic.**
- [x] Should the 30-day undo window be configurable per account? **No, keep it fixed for v1.**
- [ ] How do we handle contacts with linked Salesforce records? Need input from the integrations team (Marco to follow up).
