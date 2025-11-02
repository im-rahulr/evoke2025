# Design Document

## Overview

This design implements a dynamic pricing system for the Evoke 2k25 event registration platform. The system calculates costs based on the number of events selected (₹100 per event) and displays pricing information consistently across the user journey from event selection through payment to ticket generation. The admin panel is enhanced to display event counts and payment amounts for each registration.

## Architecture

### System Components

The pricing system integrates with existing components:

1. **Events List Page** (`pages/events-list.html`)
   - Event card display with pricing
   - Checkout section with dynamic totals
   - Session storage management

2. **Payment Page** (`pages/payment.html`)
   - Amount display and UPI integration
   - Transaction note generation

3. **Success Page** (`pages/success.html`)
   - Ticket generation with pricing

4. **QR Code Page** (`pages/qr-code.html`)
   - QR code ticket with amount display

5. **Admin RSVP Management** (`admin/rsvp-management.html`)
   - Enhanced table with event count and amount columns

### Data Flow

```
User selects events → Calculate total (count × ₹100) → Store in sessionStorage
                                                      ↓
                                                Store in Firestore
                                                      ↓
Display on payment page → Generate ticket → Display in admin panel
```

## Components and Interfaces

### 1. Event Selection Component (events-list.html)

#### Event Card Enhancement

**Location:** Within each `.event-card` element

**Visual Design:**
- Add price badge in the top-right corner of each event card
- Style: Small pill-shaped badge with primary color background
- Text: "₹100"
- Position: Absolute positioning, top-right with 8px margin

**HTML Structure:**
```html
<div class="event-card">
  <div class="event-price-badge">₹100</div>
  <!-- existing card content -->
</div>
```

**CSS Styling:**
```css
.event-price-badge {
  position: absolute;
  top: 8px;
  right: 8px;
  background: var(--primary);
  color: #10210a;
  padding: 4px 10px;
  border-radius: 12px;
  font-size: 12px;
  font-weight: 700;
}
```

#### Checkout Section Enhancement

**Location:** `#selectionSummary` div

**Current Structure:** Displays list of selected events with checkmarks

**Enhanced Structure:**
- Add summary row showing event count
- Add summary row showing total amount
- Style with clear visual hierarchy

**HTML Structure:**
```html
<div class="selection-summary" id="selectionSummary">
  <h3>Selected Events</h3>
  <div id="summaryItems">
    <!-- existing event items -->
  </div>
  <div class="summary-totals">
    <div class="summary-row">
      <span>Events Selected:</span>
      <span id="eventCount">0</span>
    </div>
    <div class="summary-row total">
      <span>Total Amount:</span>
      <span id="totalAmount">₹0</span>
    </div>
  </div>
  <button class="btn btn-primary btn-block" id="proceedToPayment">
    Continue to Payment
  </button>
</div>
```

**CSS Styling:**
```css
.summary-totals {
  border-top: 1px solid var(--line);
  margin-top: 12px;
  padding-top: 12px;
}

.summary-row {
  display: flex;
  justify-content: space-between;
  padding: 6px 0;
  font-size: 14px;
}

.summary-row.total {
  font-size: 16px;
  font-weight: 700;
  color: var(--primary);
  border-top: 1px solid var(--line);
  margin-top: 8px;
  padding-top: 8px;
}
```

#### JavaScript Logic

**Function: `updateSelectionSummary()`**

Current behavior: Updates the list of selected events

Enhanced behavior:
1. Calculate event count: `Object.keys(selectedEvents).length`
2. Calculate total amount: `eventCount × 100`
3. Update DOM elements:
   - `#eventCount` with count
   - `#totalAmount` with formatted amount (₹[amount])
4. Store in sessionStorage:
   - `sessionStorage.setItem('eventCount', eventCount)`
   - `sessionStorage.setItem('totalAmount', totalAmount)`

**Function: `proceedToPayment()`**

Current behavior: Saves selections to Firestore and redirects

Enhanced behavior:
1. Include `totalAmount` in Firestore document
2. Include `eventCount` in Firestore document (or derive from selections array length)
3. Ensure sessionStorage values are set before redirect

### 2. Payment Page Component (payment.html)

#### Amount Display Enhancement

**Location:** Multiple locations on the page

**Page Title Update:**
- Current: "Pay ₹100"
- Enhanced: "Pay ₹[totalAmount]" (dynamic based on selections)

**Implementation:**
```javascript
function loadEventSelections() {
  const totalAmount = sessionStorage.getItem('totalAmount') || '100';
  const eventCount = sessionStorage.getItem('eventCount') || '1';
  
  // Update page title
  const titleEl = document.getElementById('paymentTitle');
  if (titleEl) {
    titleEl.textContent = `Pay ₹${totalAmount}`;
  }
  
  // Update UPI config
  UPI_CONFIG.amount = totalAmount;
  
  // Update transaction note
  const userContext = getUserContext();
  UPI_CONFIG.transactionNote = `Token ${userContext?.token || 'N/A'} Evoke 2k25 - ${eventCount} event(s) - ₹${totalAmount}`;
}
```

**User Info Box Enhancement:**

Add event count and amount to the user info display:

```html
<div id="userInfo">
  <h3>Registration Details</h3>
  <div>
    <p><strong>Name:</strong> <span id="userName"></span></p>
    <p><strong>Token:</strong> <span id="userToken"></span></p>
    <p><strong>Events:</strong> <span id="userEventCount"></span></p>
    <p><strong>Amount:</strong> <span id="userAmount"></span></p>
  </div>
</div>
```

### 3. QR Code Ticket Component (success.html & qr-code.html)

#### Ticket Generation Enhancement

**Function: `generateTicket()`**

Current parameters: `(tokenNo, userName, mode)`

Enhanced parameters: `(tokenNo, userName, mode, eventCount, totalAmount)`

**Canvas Layout Updates:**

Add two new text elements to the ticket:
1. Event count: "Events: [count]"
2. Total amount: "Amount: ₹[amount]"

**Position:** Below the payment mode line, above the event date

**Implementation:**
```javascript
async function generateTicket(tokenNo, userName, mode, eventCount, totalAmount) {
  // ... existing canvas setup ...
  
  // Payment mode (existing)
  ctx.font = '18px Arial';
  ctx.fillStyle = '#B3B3B3';
  ctx.fillText(`Payment Mode: ${mode === 'onspot' ? 'Pay on Spot' : 'Online Payment'}`, canvas.width / 2, 400);
  
  // NEW: Event count
  ctx.font = '18px Arial';
  ctx.fillStyle = '#ffffff';
  ctx.fillText(`Events Selected: ${eventCount}`, canvas.width / 2, 430);
  
  // NEW: Total amount
  ctx.font = 'bold 20px Arial';
  ctx.fillStyle = '#2C74B3';
  ctx.fillText(`Total Amount: ₹${totalAmount}`, canvas.width / 2, 460);
  
  // Event date (moved down)
  ctx.font = 'bold 18px Arial';
  ctx.fillStyle = '#B3B3B3';
  ctx.fillText('Event Date: November 27, 2025', canvas.width / 2, 490);
  
  // ... rest of ticket generation ...
}
```

**Data Retrieval:**

Retrieve from sessionStorage when generating ticket:
```javascript
const eventCount = sessionStorage.getItem('eventCount') || '0';
const totalAmount = sessionStorage.getItem('totalAmount') || '0';
```

### 4. Admin RSVP Management Component (admin/rsvp-management.html)

#### Table Structure Enhancement

**Current Columns:**
- Token
- Name
- Payment Mode
- Type
- Downloaded At

**Enhanced Columns:**
- Token
- Name
- **Number of Events** (NEW)
- **Payment Amount** (NEW)
- Payment Mode
- Type
- Downloaded At

**HTML Structure:**
```html
<table class="downloads-table">
  <thead>
    <tr>
      <th>Token</th>
      <th>Name</th>
      <th>Events</th>
      <th>Amount</th>
      <th>Payment Mode</th>
      <th>Type</th>
      <th>Downloaded At</th>
    </tr>
  </thead>
  <tbody id="downloadsTableBody">
    <!-- rows populated by JavaScript -->
  </tbody>
</table>
```

**CSS Styling:**
```css
.downloads-table th:nth-child(3),
.downloads-table td:nth-child(3) {
  text-align: center;
  width: 80px;
}

.downloads-table th:nth-child(4),
.downloads-table td:nth-child(4) {
  text-align: right;
  width: 100px;
  font-weight: 600;
  color: var(--primary);
}
```

#### Data Retrieval Logic

**Function: `loadRecentDownloads()`**

Enhanced to fetch event count and amount from Firestore:

```javascript
async function loadRecentDownloads() {
  // ... existing query setup ...
  
  const rows = [];
  for (const doc of snapshot.docs) {
    const data = doc.data();
    
    // Fetch registration data to get event count and amount
    const regQuery = query(
      collection(db, 'registrations'),
      where('shortId', '==', data.tokenNo),
      limit(1)
    );
    const regSnap = await getDocs(regQuery);
    
    let eventCount = 0;
    let totalAmount = 0;
    
    if (!regSnap.empty) {
      const regData = regSnap.docs[0].data();
      eventCount = regData.eventCount || 0;
      totalAmount = regData.totalAmount || 0;
    }
    
    rows.push(`
      <tr>
        <td><strong>${data.tokenNo || 'N/A'}</strong></td>
        <td>${data.username || 'N/A'}</td>
        <td>${eventCount}</td>
        <td>₹${totalAmount}</td>
        <td>${data.paymentMode || 'N/A'}</td>
        <td><span class="download-type ${downloadType}">${downloadType}</span></td>
        <td>${downloadTime}</td>
      </tr>
    `);
  }
  
  downloadsTableBody.innerHTML = rows.join('');
}
```

**User Details Card Enhancement:**

Add event count and amount to the user details display:

```html
<div class="user-info-grid">
  <!-- existing fields -->
  <div class="user-info-item">
    <div class="user-info-label">Events Selected</div>
    <div class="user-info-value" id="userEventCount">0</div>
  </div>
  <div class="user-info-item">
    <div class="user-info-label">Total Amount</div>
    <div class="user-info-value" id="userTotalAmount">₹0</div>
  </div>
</div>
```

## Data Models

### Session Storage Schema

```javascript
{
  "eventCount": "3",           // String: number of selected events
  "totalAmount": "300",        // String: total amount in rupees
  "eventSelections": "[...]",  // JSON string: array of event objects (existing)
  "lastToken": "058",          // String: user token (existing)
  "lastName": "John Doe",      // String: user name (existing)
  // ... other existing fields
}
```

### Firestore Schema Updates

#### Collection: `eventSelections`

**Enhanced Document Structure:**
```javascript
{
  userId: "doc_id",
  userToken: "058",
  selections: [
    {
      eventId: "solo-dance",
      eventTitle: "Solo Dance",
      quantity: 1,
      maxParticipants: 1
    }
    // ... more events
  ],
  eventCount: 3,              // NEW: number of events
  totalAmount: 300,           // ENHANCED: calculated total
  createdAt: Timestamp,
  updatedAt: Timestamp,
  paymentStatus: "pending"
}
```

#### Collection: `registrations`

**Enhanced Document Structure:**
```javascript
{
  // ... existing fields ...
  eventCount: 3,              // NEW: number of events selected
  totalAmount: 300,           // NEW: total payment amount
  hasSelectedEvents: true,
  eventSelectionId: "doc_id",
  eventSelectionTimestamp: Timestamp
}
```

## Error Handling

### Missing Amount Data

**Scenario:** User navigates to payment page without selecting events

**Handling:**
1. Check if `totalAmount` exists in sessionStorage
2. If missing, default to ₹100 (minimum one event)
3. Log warning to console
4. Display message: "Amount not found, defaulting to ₹100"

### Invalid Amount Data

**Scenario:** Amount in sessionStorage is not a valid number

**Handling:**
1. Validate amount using `parseInt()` or `parseFloat()`
2. If invalid, default to ₹100
3. Log error to console
4. Display error message to user

### Firestore Write Failures

**Scenario:** Failed to save event count or amount to Firestore

**Handling:**
1. Catch error in try-catch block
2. Log error details to console
3. Continue with user flow (don't block)
4. Display warning: "Some data may not have been saved"

### Admin Panel Data Loading

**Scenario:** Failed to fetch event count or amount for admin table

**Handling:**
1. Display "0" for event count
2. Display "₹0" for amount
3. Log warning to console
4. Don't block table rendering

## Testing Strategy

### Unit Testing

**Test Cases:**

1. **Amount Calculation**
   - Input: 0 events selected
   - Expected: totalAmount = 0
   
2. **Amount Calculation**
   - Input: 1 event selected
   - Expected: totalAmount = 100
   
3. **Amount Calculation**
   - Input: 5 events selected
   - Expected: totalAmount = 500

4. **Session Storage**
   - Action: Select 3 events
   - Expected: sessionStorage contains eventCount="3" and totalAmount="300"

5. **Firestore Save**
   - Action: Submit event selections
   - Expected: Firestore document contains eventCount and totalAmount fields

### Integration Testing

**Test Scenarios:**

1. **End-to-End Flow**
   - Select 3 events on events-list.html
   - Verify checkout shows "3 events" and "₹300"
   - Navigate to payment.html
   - Verify page title shows "Pay ₹300"
   - Verify UPI amount is ₹300
   - Complete payment flow
   - Download ticket
   - Verify ticket shows "3 events" and "₹300"

2. **Admin Panel Verification**
   - Complete registration with 2 events
   - Open admin panel
   - Verify table shows "2" in Events column
   - Verify table shows "₹200" in Amount column

3. **Edge Case: No Events**
   - Navigate to events-list.html
   - Don't select any events
   - Attempt to proceed
   - Verify error message or disabled button

### Manual Testing

**Test Checklist:**

- [ ] Event cards display ₹100 badge
- [ ] Checkout section updates dynamically
- [ ] Event count displays correctly
- [ ] Total amount displays correctly
- [ ] Payment page shows correct amount
- [ ] UPI transaction includes correct amount
- [ ] Ticket displays event count and amount
- [ ] Admin table shows event count column
- [ ] Admin table shows amount column
- [ ] Data persists across page navigation
- [ ] Mobile responsive design works correctly

## Performance Considerations

### Calculation Efficiency

- Amount calculation is O(1): `count × 100`
- No complex loops or API calls required
- Updates happen synchronously in memory

### DOM Updates

- Use `textContent` instead of `innerHTML` for simple text updates
- Batch DOM updates where possible
- Debounce rapid selection changes (if needed)

### Firestore Optimization

- Minimize writes by batching updates
- Use existing document update operations
- Index fields used in queries (eventCount, totalAmount)

## Security Considerations

### Input Validation

- Validate event count is a positive integer
- Validate amount matches expected calculation (count × 100)
- Sanitize all user inputs before display

### Data Integrity

- Verify amount on server-side (if backend validation exists)
- Cross-check event count with selections array length
- Prevent manipulation of sessionStorage values

### Admin Access

- Ensure admin panel is properly authenticated
- Validate admin permissions before displaying sensitive data
- Log all admin actions for audit trail
