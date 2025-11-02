# Implementation Plan

- [x] 1. Enhance Event Selection Page (events-list.html)


  - Add price badges to event cards showing ₹100 per event
  - Create CSS styling for event-price-badge class
  - Update checkout section HTML to include event count and total amount displays
  - Create CSS styling for summary-totals and summary-row classes
  - _Requirements: 1.1, 1.2, 1.3, 2.1, 2.2, 2.3, 2.4, 2.5, 3.1, 3.2, 3.3_



- [ ] 1.1 Add price badge HTML to event card rendering
  - Modify the event card HTML structure in the renderEvents() function
  - Add `<div class="event-price-badge">₹100</div>` to each event card

  - _Requirements: 1.1, 1.2_

- [ ] 1.2 Create CSS styles for price badges
  - Add .event-price-badge styles with absolute positioning


  - Style with primary color background and appropriate sizing
  - _Requirements: 1.2, 1.3_


- [ ] 1.3 Update checkout section HTML structure
  - Add summary-totals div with event count and total amount rows
  - Update the selectionSummary div structure
  - _Requirements: 2.3, 3.1_



- [ ] 1.4 Create CSS styles for checkout summary
  - Add .summary-totals, .summary-row, and .summary-row.total styles
  - Ensure proper visual hierarchy and spacing
  - _Requirements: 2.3_



- [ ] 1.5 Implement dynamic calculation in updateSelectionSummary()
  - Calculate event count from selectedEvents object


  - Calculate total amount as eventCount × 100
  - Update DOM elements for event count and total amount
  - Store values in sessionStorage
  - _Requirements: 2.1, 2.2, 2.4, 2.5, 3.2, 3.3, 8.1, 8.2, 8.5_

- [ ] 1.6 Update proceedToPayment() to save pricing data
  - Include totalAmount in Firestore eventSelections document


  - Include eventCount in Firestore document
  - Ensure sessionStorage values are set before redirect
  - _Requirements: 8.3, 8.4_

- [x] 2. Update Payment Page (payment.html)


  - Modify loadEventSelections() to retrieve and display dynamic amount
  - Update page title with dynamic amount
  - Update UPI configuration with dynamic amount


  - Update transaction note with event count and amount
  - Enhance user info box to display event count and amount
  - _Requirements: 4.1, 4.2, 4.3, 4.4_

- [ ] 2.1 Modify loadEventSelections() function
  - Retrieve totalAmount and eventCount from sessionStorage
  - Update page title element with dynamic amount


  - Update UPI_CONFIG.amount with retrieved value
  - Update UPI_CONFIG.transactionNote with event count and amount
  - _Requirements: 4.1, 4.2, 4.3, 4.4_



- [ ] 2.2 Enhance user info box HTML
  - Add event count display field
  - Add amount display field
  - Update displayUserInfoPayment() function to populate new fields


  - _Requirements: 4.1_

- [x] 3. Update QR Code Ticket Generation (success.html)


  - Modify generateTicket() function signature to accept eventCount and totalAmount
  - Add event count text to canvas
  - Add total amount text to canvas
  - Adjust positioning of existing elements
  - Retrieve pricing data from sessionStorage when calling generateTicket()


  - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [ ] 3.1 Update generateTicket() function signature
  - Change from (tokenNo, userName, mode) to (tokenNo, userName, mode, eventCount, totalAmount)


  - Update all calls to generateTicket() to pass new parameters
  - _Requirements: 5.1_



- [ ] 3.2 Add pricing information to ticket canvas
  - Add "Events Selected: [count]" text at y=430
  - Add "Total Amount: ₹[amount]" text at y=460
  - Adjust event date position to y=490
  - Style with appropriate fonts and colors
  - _Requirements: 5.1, 5.2, 5.3, 5.4_



- [ ] 3.3 Retrieve pricing data from sessionStorage
  - Get eventCount from sessionStorage in reveal() function
  - Get totalAmount from sessionStorage in reveal() function
  - Pass values to generateTicket() calls


  - _Requirements: 5.1_

- [x] 4. Update QR Code Page (qr-code.html)


  - Add event count and amount to user info display
  - Retrieve pricing data from sessionStorage or URL parameters
  - Update generateQRCode() to include pricing in payload
  - _Requirements: 5.1, 5.2_

- [x] 4.1 Enhance user info display


  - Add event count row to userInfoContainer
  - Add amount row to userInfoContainer
  - Retrieve values from sessionStorage


  - _Requirements: 5.1, 5.2_

- [ ] 4.2 Update QR code payload
  - Include eventCount in JSON payload
  - Include totalAmount in JSON payload


  - _Requirements: 5.1_

- [ ] 5. Enhance Admin RSVP Management (admin/rsvp-management.html)
  - Add "Number of Events" column to table header
  - Add "Payment Amount" column to table header


  - Update loadRecentDownloads() to fetch and display event count
  - Update loadRecentDownloads() to fetch and display payment amount
  - Add event count and amount to user details card


  - Create CSS styles for new columns
  - _Requirements: 6.1, 6.2, 6.3, 6.4, 7.1, 7.2, 7.3, 7.4_

- [ ] 5.1 Update table HTML structure
  - Add <th>Events</th> column header
  - Add <th>Amount</th> column header


  - Position columns appropriately in table
  - _Requirements: 6.1, 7.1_

- [ ] 5.2 Create CSS styles for new columns
  - Style Events column with center alignment and 80px width

  - Style Amount column with right alignment, 100px width, and primary color
  - _Requirements: 6.1, 7.1_

- [ ] 5.3 Update loadRecentDownloads() function
  - Query registrations collection for each download record


  - Retrieve eventCount from registration document
  - Retrieve totalAmount from registration document
  - Display values in table cells with proper formatting
  - Handle missing data with default values (0 and ₹0)
  - _Requirements: 6.2, 6.3, 6.4, 7.2, 7.3, 7.4_

- [ ] 5.4 Enhance user details card
  - Add event count field to user-info-grid
  - Add total amount field to user-info-grid
  - Update displayUserDetails() to populate new fields

  - _Requirements: 6.1, 7.1_

- [ ] 6. Update Admin Ticket Generation
  - Modify generateAdminTicket() to accept eventCount and totalAmount
  - Add pricing information to admin-generated tickets
  - Retrieve pricing data when generating tickets

  - _Requirements: 5.1, 5.2, 5.3_

- [ ] 6.1 Update generateAdminTicket() function
  - Add eventCount and totalAmount parameters
  - Add event count text to canvas
  - Add total amount text to canvas

  - Adjust positioning of other elements
  - _Requirements: 5.1, 5.2, 5.3_

- [ ] 6.2 Update downloadAdminRsvpTicket() function
  - Retrieve eventCount from currentUserData
  - Retrieve totalAmount from currentUserData

  - Pass values to generateAdminTicket()
  - _Requirements: 5.1_

- [ ] 7. Add Error Handling and Validation
  - Add validation for missing totalAmount in payment page
  - Add validation for invalid amount values

  - Add default values for missing data in admin panel
  - Add console logging for debugging
  - _Requirements: All requirements (defensive programming)_

- [ ] 7.1 Implement payment page validation
  - Check if totalAmount exists in sessionStorage
  - Validate amount is a positive number
  - Default to ₹100 if missing or invalid
  - Log warnings to console
  - _Requirements: 4.1, 4.4_

- [ ] 7.2 Implement admin panel error handling
  - Handle missing registration documents gracefully
  - Display default values (0, ₹0) when data is missing
  - Log warnings for missing data
  - Don't block table rendering on errors
  - _Requirements: 6.4, 7.4_

- [ ] 8. Test and Verify Implementation
  - Test event selection with multiple events
  - Verify checkout section updates correctly
  - Test payment page amount display
  - Verify ticket generation includes pricing
  - Test admin panel displays correct data
  - Test edge cases (0 events, missing data)
  - Test mobile responsive design
  - _Requirements: All requirements_

- [ ] 8.1 Test event selection flow
  - Select 0, 1, 3, and 5 events
  - Verify event count updates correctly
  - Verify total amount calculates correctly (count × 100)
  - Verify sessionStorage contains correct values
  - _Requirements: 2.1, 2.2, 2.4, 2.5, 3.2, 3.3, 8.1, 8.2, 8.5_

- [ ] 8.2 Test payment page
  - Navigate from events page with different amounts
  - Verify page title shows correct amount
  - Verify UPI amount is correct
  - Verify transaction note includes correct details
  - _Requirements: 4.1, 4.2, 4.3, 4.4_

- [ ] 8.3 Test ticket generation
  - Generate tickets with different event counts
  - Verify event count displays on ticket
  - Verify amount displays on ticket
  - Verify QR code includes pricing data
  - _Requirements: 5.1, 5.2, 5.3, 5.4_

- [ ] 8.4 Test admin panel
  - View registrations with different event counts
  - Verify Events column shows correct numbers
  - Verify Amount column shows correct values
  - Test with missing data scenarios
  - _Requirements: 6.1, 6.2, 6.3, 6.4, 7.1, 7.2, 7.3, 7.4_

- [ ] 8.5 Test edge cases and error scenarios
  - Test with 0 events selected
  - Test with missing sessionStorage data
  - Test with invalid amount values
  - Test mobile responsive layouts
  - _Requirements: All requirements_
