# تيكيت سوق — Ticket-Souq

## Product Requirements Document

**A unified ticket booking platform for concerts, sports, rail travel, museums, and beyond — one experience for customers, one platform for organizers.**

| | |
|---|---|
| **Version** | 1.2 — Business Requirements (reorganized, merged with role/rules whiteboard) |
| **Status** | 🟡 Draft |

---

## Table of Contents

1. [Product Vision](#01-product-vision)
2. [Problem Statement](#02-problem-statement)
3. [Product Objectives](#03-product-objectives)
4. [Product Scope](#04-product-scope)
5. [Booking Models](#05-booking-models)
7. [Users & Roles](#07-users--roles)
8. [Roles & Permissions Matrix](#08-roles--permissions-matrix)
9. [User Stories](#09-user-stories)
10. [Event Lifecycle](#10-event-lifecycle)
11. [Customer Journey](#11-customer-journey)
12. [Organizer Journey](#12-organizer-journey)
13. [Event Templates](#13-event-templates)
14. [Inventory Management](#14-inventory-management)
15. [Payments](#15-payments)
16. [QR Tickets](#16-qr-tickets)
17. [Seat Locking](#17-seat-locking)
18. [Event Updates](#18-event-updates)
19. [Cancellation & Refund](#19-cancellation--refund)
20. [Notifications](#20-notifications)
21. [Categories](#21-categories)
22. [Assumptions](#22-assumptions)
23. [Out of Scope (MVP)](#23-out-of-scope-mvp)

---

## 01. Product Vision

Ticket-Souq is a unified ticket booking platform that enables customers to discover, book, and manage tickets for multiple event types from a single application — while giving organizers a complete platform to publish events, manage seats, receive payments, and validate attendees using QR codes.

---

## 02. Problem Statement

Customers currently use multiple platforms to book tickets. Each platform provides a different user experience, payment flow, and booking process. Organizers, meanwhile, need to build their own booking solution or depend on multiple third-party services.

### The Opportunity

Ticket-Souq aims to centralize this fragmented experience into one platform — a single interface for customers, and a single backend for organizers.

---

## 03. Product Objectives

**Customer**
- Discover events
- Book tickets easily
- Pay securely
- Receive QR tickets
- Manage reservations from one place

**Organizations**
- Publish events
- Manage available seats or zones
- Track reservations
- Notify attendees
- Validate tickets

**Platform**
- Support multiple booking domains
- Offer a unified booking experience
- Allow future expansion without redesigning the platform

---

## 04. Product Scope

Ticket-Souq focuses on ticket-based physical attendance experiences.

**In Scope**
- Cinemas
- Concerts
- Stadiums
- Museums

**Future plans**
- Hotels
- Transportation
- Restaurants
- Doctor Appointments
- General Reservations

The architecture must allow adding new domains in future releases.

---

## 05. Booking Models


### 5.1 Zone-Based Booking 🏟️
The customer reserves a place inside a zone — no seat selection exists. Examples: Museums, Stadiums.

### 5.2 Section-Based/Seat-Based Booking 
the organizer can divide the venue to multiple section each section treated as zone but it contain Seats
using a library such as [react-seatmap-creator](https://github.com/cenksari/react-seatmap-creator) to let organizers draw the venue template

and The customer reserves a specific seat. Examples: Cinema, Concerts.

---

## 06. Users & Roles

**Customer**
- Registration
- Booking single or multiple tickets and selecting specific seats/zones
- Payment
- Viewing reservations
- Receiving email notifications
- Browsing events
- Fuzzy search over events (Elasticsearch), with results even on imprecise queries
- Receiving a QR code as a ticket
- Viewing reserved events in a timeline list
- Reserving more than one ticket (max 10), each of which must be named for a specific attendee
- Viewing event/ticket details (where, when, description, valid-until)

**Organization** — a company that manages events, with three internal roles:

1. **Consumer**
   - Validate / consume QR codes at entry

2. **Agent** *(Consumer authorities, plus:)*
   - Create, update, cancel (soft delete) an event
   - Set up a venue template and save it, linked to the organization's venues
   - Pre-reserve a number of tickets before the event is published
   - Create unique QR codes for non-public tickets
   - View an analysis dashboard showing attendees

3. **Organization Head** *(Agent authorities, plus:)*
   - Add agents / consumers to the organization

**Admin**
- Mange platform (Observation)
- Approve an organization
- Ban an organization (cascades to its consumers/agents)

---

## 07. Roles & Permissions Matrix

This matrix reconciles the high-level "Rules" summary with the detailed per-role user stories, so permissions aren't under-specified for engineering.

| Capability | Consumer | Agent | Org Head | Admin |
|---|:---:|:---:|:---:|:---:|
| Validate / consume QR | ✅ | ✅ | ✅ | — |
| Create event | — | ✅ | ✅ | — |
| Update event | — | ✅ | ✅ | — |
| Cancel / soft-delete event | — | ✅ | ✅ | — |
| Create & save venue template | — | ✅ | ✅ | — |
| Pre-reserve tickets before publish | — | ✅ | ✅ | — |
| Create unique QR for non-public tickets | — | ✅ | ✅ | — |
| View analysis dashboard | — | ✅ | ✅ | ✅ |
| Add agents / consumers | — | — | ✅ | — |
| Mange platform (Observation) | — | — | — | ✅ |
| Approve organization | — | — | — | ✅ |
| Ban organization (cascade) | — | — | — | ✅ |

---

## 08. User Stories

[User Stories](https://trello.com/b/TNxbpUGr/backlog)

---

## 09. Event Lifecycle

1. Organization Head / Agent creates event
2. Configure seats/zones
3. Submit
4. Published *(visible to customers)*
5. Reservations open
6. Consumer Validate ticket
7. Event completed

---

## 10. Customer Journey

Nine steps carry a customer from discovery through to venue entry.

**Discover**
1. Browse
2. Search / Categories
3. Open event

**Book & Pay**
4. Select seat or zone
5. Reserve *(temporary lock for 10 min starts)*
6. Payment
7. QR generation

**Attend**
8. Attend event
9. QR validation *(scanned at entry)*

---

## 11. Organization Journey

1. Organization registers → awaits Admin approval
2. Agent/Org Head sets up venue template (or reuses a saved one)
3. Agent/Org Head creates event and configures seats/zones
4. Agent/Org Head optionally pre-reserves a block of tickets
5. Event submitted → published
6. Agent/Org Head monitors reservations via analysis dashboard
7. Agent/Org Head issues updates as needed (bounded by Event Updates rules)
8. On event day, Consumer/Agent/Org Head validates tickets via QR scan
9. when event start payout released to organizer

---

## 12. Event Templates

- Venue templates can be created and saved by an Agent or Organization Head.
- Saved templates are linked to the organization's venues for reuse across events.
- For seat-based venues, template creation is expected to use a seat-map drawing tool.

---

## 13. Payments

Support payment via Stripe in production and a Simulated gateway in local testing.

**Simulated** *(MVP / Demo)*
- Internal `PaymentService` mimics gateway behavior
- Configurable delay + success/fail outcome for demoing edge cases
- Same interface as a real provider — swappable later
- Removes gateway sandbox setup from the production

> **Recommendation:** Build one `PaymentGateway` interface with a single method set — `charge()`, `confirm()`, `refund()` — and implement it as Simulated first. This unblocks the seat-locking and booking flow immediately. Swap in Stripe once the core flow is stable, without touching booking logic.

### Payment Flow (Provider-Agnostic)
1. Reservation created *(seat/zone locked)*
2. Checkout initiated *(redirect or simulated call)*
3. Gateway processes payment
4. Webhook / callback received (Confirm the payment)
5. Reservation confirmed
6. QR ticket issued

> Confirmation is always driven by the gateway's callback/webhook, never by the client redirect alone — this avoids trusting an unconfirmed client-side "success" state.

### Payout Timing
- Ticket money is held until the event starts.
- If the event proceeds normally, funds are released to the organizer after the event start.
- Organizer receives event money **after** event start specifically to allow room for organizer-initiated cancellation refunds.

---

## 14. QR Tickets

Every successful reservation generates one QR code, used for venue entry. During scanning, the organization's Consumer/Agent/Org Head can view:
- Customer name
- Seat or zone
- Ticket status

The QR must indicate whether the ticket is:
- Valid
- Used
- Cancelled (NON functional requierment)

---

## 15. Seat Locking

A short-lived lock protects a seat while the customer pays, then resolves one of two ways.

1. Customer selects a seat
2. Seat temporarily locked *(TTL countdown starts)*
3. Customer completes payment

✅ **Payment Succeeds** — Seat transitions to Booked. Reservation is confirmed and a QR ticket is issued.

❌ **Payment Abandoned or Times Out** — Lock expires automatically. Seat returns to Available for other customers.

---

## 16. Event Updates

Organizers may update events under specific rules.

**Allowed**
- Increase prices for unbooked inventory

---

## 17. Cancellation & Refund

Ticket money is held until the event starts. If the event goes ahead, funds are released to the organizer after event start. Refunds are **not** generally supported — **except** when the organizer cancels the event, in which case the ticket price is returned to the customer (requires maintaining a user → ticket map).

### Cancellation Flow
1. Organizer cancels the event
2. All reservations flagged for refund
3. Customers receive refunds
4. Notification sent

### Money Flow Summary
- Normal case: hold funds → release to organizer after event start.
- Cancellation case: hold funds → refund to customer in cash/original payment method.

> Detailed refund rules — timing, partial refunds, processing fees — will be defined in a later version.

---

## 18. Notifications

User receive notifications when:
for org: 
- Approve Organization
- ban Organization

for Customer
- Payment succeeds (confirm reservation)
- Event is cancelled
- A link to the ticket's QR code is sent

**Channels**
- Email
- push Notification

---

## 19. Categories

Customers browse events through categories:
- Concerts
- Sports
- Movies
- Museums

Search functionality is supported (fuzzy, via Elasticsearch).

---

## 20. Assumptions

- Guest checkout is not supported.
- Every reservation belongs to exactly one event.
- Every event belongs to one organizer.
- Electronic payment is mandatory.
- An organizer can add one or more type of business event.
- Organizer receives event money after event start, to allow handling of organizer-initiated cancellations.

---

## 21. Out of Scope (MVP)

- Funding organizations
- Promo Codes
- Loyalty Programs
- Multi-language
- Multi-currency
- Calendar Integration
- Waitlists
- Recommedation