# Firebase Firestore Rules Update

## Changes Made for Dynamic Event Pricing

### Updated Collections

#### 1. `registrations` Collection
**Added field to update whitelist:**
- `eventCount` - Number of events selected by the user

**Updated rule:**
```javascript
request.resource.data.diff(resource.data).changedKeys().hasOnly([
  'paymentMode','paymentAt','rsvpDownloaded','rsvpDownloadedAt','adminGenerated','paymentUpdatedAt','paymentUpdatedBy',
  'hasSelectedEvents','eventSelectionId','eventSelections','totalAmount','eventCount','eventSelectionTimestamp',
  'paymentApproved','paymentApprovedAt','paymentApprovedBy','paymentType','transactionId','receiptNo',
  'deleted','deletedAt','deletedReason','restoredAt'
])
```

#### 2. `eventSelections` Collection
**Added fields to create validation:**
- `eventCount` - Number of events in the selection (must be >= 0)

**Updated rule:**
```javascript
allow create: if request.resource.data.userId is string &&
               request.resource.data.userToken is string &&
               request.resource.data.userToken.matches('^\\d{3}$') &&
               request.resource.data.selections is list &&
               request.resource.data.eventCount is number &&
               request.resource.data.eventCount >= 0 &&
               request.resource.data.totalAmount is number &&
               request.resource.data.totalAmount >= 0 &&
               request.resource.data.paymentStatus in ['pending', 'completed', 'failed'] &&
               request.resource.data.keys().hasOnly(['userId','userToken','selections','eventCount','totalAmount','createdAt','updatedAt','paymentStatus']);
```

## How to Deploy

### Option 1: Firebase Console (Recommended)
1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Select your project
3. Navigate to **Firestore Database** → **Rules**
4. Copy the contents of `config/firestore.rules`
5. Paste into the rules editor
6. Click **Publish**

### Option 2: Firebase CLI
```bash
# Make sure you're in the project root directory
firebase deploy --only firestore:rules
```

## Validation

After deploying, test the following:

1. **Event Selection Flow:**
   - Select multiple events on events-list.html
   - Verify the data saves to Firestore with `eventCount` field
   - Check that `totalAmount` = `eventCount` × 100

2. **Registration Update:**
   - Verify that registration documents can be updated with `eventCount`
   - Check admin panel displays the event count correctly

3. **Error Handling:**
   - Try to save without `eventCount` (should fail validation)
   - Try to save with negative `eventCount` (should fail validation)

## Security Notes

- `eventCount` must be a number >= 0
- Only whitelisted fields can be updated in registrations
- Event selections require all mandatory fields including `eventCount`
- All other security rules remain unchanged

## Rollback

If you need to rollback, restore the original rules from:
`Original-backup/firestore.rules`
