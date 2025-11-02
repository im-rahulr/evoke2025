# Requirements Document

## Introduction

This feature implements a dynamic pricing system for the Evoke 2k25 event registration platform. Currently, the system has a fixed payment structure, but this enhancement will introduce per-event pricing (₹100 per event), dynamic total calculation based on selected events, and comprehensive display of pricing information across the user journey and admin panel.

## Glossary

- **Event Selection System**: The interface where users select which events they want to participate in from the events-list.html page
- **Checkout Section**: The fixed bottom section on events-list.html that displays selected events and total amount
- **Payment Page**: The page (payment.html) where users complete their payment via UPI
- **QR Code Ticket**: The downloadable image that users receive containing their registration details and QR code
- **Admin RSVP Management**: The admin interface (admin/rsvp-management.html) for managing registrations
- **Registration Record**: The Firestore document containing user registration data
- **Event Selection Record**: The Firestore document containing the user's selected events and pricing

## Requirements

### Requirement 1: Per-Event Pricing Display

**User Story:** As a user browsing the events list, I want to see the price for each event clearly displayed, so that I understand the cost before selecting events.

#### Acceptance Criteria

1. WHEN a user views the events-list.html page, THE Event Selection System SHALL display "₹100" as the price for each individual event card
2. THE Event Selection System SHALL display the per-event price in a visually prominent location on each event card
3. THE Event Selection System SHALL maintain consistent price formatting across all event cards using the Indian Rupee symbol (₹)

### Requirement 2: Dynamic Total Calculation in Checkout

**User Story:** As a user selecting multiple events, I want to see the total cost update automatically as I add or remove events, so that I know exactly how much I will pay.

#### Acceptance Criteria

1. WHEN a user selects an event from the events list, THE Checkout Section SHALL add ₹100 to the displayed total amount
2. WHEN a user deselects an event from the events list, THE Checkout Section SHALL subtract ₹100 from the displayed total amount
3. THE Checkout Section SHALL display the total amount in the format "Total: ₹[amount]" where [amount] is the sum of all selected events
4. THE Checkout Section SHALL update the total amount within 100 milliseconds of event selection or deselection
5. THE Checkout Section SHALL display "₹0" WHEN no events are selected

### Requirement 3: Event Count Display in Checkout

**User Story:** As a user selecting events, I want to see how many events I have selected, so that I can quickly verify my selections.

#### Acceptance Criteria

1. THE Checkout Section SHALL display the count of selected events in the format "[count] event(s) selected"
2. WHEN a user selects or deselects an event, THE Checkout Section SHALL update the event count within 100 milliseconds
3. THE Checkout Section SHALL display "0 events selected" WHEN no events are selected

### Requirement 4: Payment Page Amount Display

**User Story:** As a user proceeding to payment, I want to see the correct total amount on the payment page, so that I can verify the charge before completing payment.

#### Acceptance Criteria

1. WHEN a user navigates to the payment page, THE Payment Page SHALL display the total amount calculated from selected events
2. THE Payment Page SHALL display the amount in the page title as "Pay ₹[amount]"
3. THE Payment Page SHALL display the amount in the UPI transaction note as "Token [token] Evoke 2k25 Registration - ₹[amount]"
4. THE Payment Page SHALL retrieve the total amount from sessionStorage key 'totalAmount'

### Requirement 5: QR Code Ticket Amount Display

**User Story:** As a user downloading my registration ticket, I want to see the total amount I paid or will pay on the ticket, so that I have a record of the transaction.

#### Acceptance Criteria

1. WHEN a user downloads their QR code ticket, THE QR Code Ticket SHALL display the total payment amount
2. THE QR Code Ticket SHALL display the amount in the format "Amount: ₹[amount]"
3. THE QR Code Ticket SHALL display the payment mode (Online Payment or Pay on Spot)
4. THE QR Code Ticket SHALL position the amount information in a clearly visible location on the ticket image

### Requirement 6: Admin Panel Event Count Column

**User Story:** As an admin viewing registrations, I want to see how many events each user selected, so that I can quickly assess registration details.

#### Acceptance Criteria

1. THE Admin RSVP Management SHALL display a "Number of Events" column in the registrations table
2. THE Admin RSVP Management SHALL display only the numeric count of events for each registration
3. THE Admin RSVP Management SHALL retrieve the event count from the Registration Record or Event Selection Record
4. THE Admin RSVP Management SHALL display "0" WHEN a user has not selected any events

### Requirement 7: Admin Panel Payment Amount Column

**User Story:** As an admin viewing registrations, I want to see the payment amount for each user, so that I can verify payment details and manage finances.

#### Acceptance Criteria

1. THE Admin RSVP Management SHALL display a "Payment Amount" column in the registrations table
2. THE Admin RSVP Management SHALL display the amount in the format "₹[amount]"
3. THE Admin RSVP Management SHALL retrieve the payment amount from the Registration Record or Event Selection Record
4. THE Admin RSVP Management SHALL display "₹0" WHEN a user has not selected any events

### Requirement 8: Data Persistence

**User Story:** As a system, I need to store event selection and pricing data reliably, so that the information is available across all pages and for admin review.

#### Acceptance Criteria

1. WHEN a user selects events, THE Event Selection System SHALL store the total amount in sessionStorage with key 'totalAmount'
2. WHEN a user selects events, THE Event Selection System SHALL store the event count in sessionStorage with key 'eventCount'
3. WHEN a user submits their event selections, THE Event Selection System SHALL save the total amount to the Event Selection Record in Firestore
4. WHEN a user submits their event selections, THE Event Selection System SHALL save the event count to the Registration Record in Firestore
5. THE Event Selection System SHALL calculate the total amount as: (number of selected events) × 100
