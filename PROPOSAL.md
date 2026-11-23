# Capstone Proposal: Spokewell Bike Repair and Author: Brenden Canale
## Spokewell Bike Repair shop is a bike shop serving cyclists in the local Spokewell area. Customers commonly call and ask if their bike is ready for pickup, if certain parts are in stock, or if they need to reschedule a pickup date. All of these requests can be handled by a system designed to handle customer service so mechanics arent pulled away from their tasks.
## Customers will use the chat function while staff will have access to the staff tab
## Tables
'repairs': 'ticket_id TEXT PRIMARY KEY, customer TEXT, bike TEXT, status, TEXT pickup_date TEXT`
  - Example row: `('R200', 'Jordan Lee', 'road bike', 'in repair', '2026-11-20')`
- `parts`: `name TEXT PRIMARY KEY, price REAL, stock INTEGER`
  - Example row: `('inner tube', 8.00, 25)`
- `change_requests`: `number INTEGER PRIMARY KEY AUTOINCREMENT, ticket_id TEXT NOT NULL, new_date TEXT NOT NULL, status TEXT NOT NULL DEFAULT 'waiting'`
  - Starts empty until customers submit pickup date change requests.

## Lookup tools (at least two)
- `look_up_repair(ticket_id)`: Reads `repairs` to return the customer, bike type, current repair status, and scheduled pickup date for a given `ticket_id` (validating that `ticket_id` is non-empty and exists). Example question: `"Is my repair R200 ready?"`
- `check_part(name)`: Reads `parts` to return the price and current stock count for a given part `name` (validating that `name` is non-empty and exists). Example question: `"Do you have brake pads in stock?"`

## Change tool (at least one)
- `request_pickup_change(ticket_id, new_date)`: Inserts a `'waiting'` request into `change_requests` for staff review rather than modifying `repairs` directly. When staff approve the request on the Staff tab, `repairs.pickup_date` updates to `new_date`. Example request: `"Can I pick up R200 on 2026-11-22 instead?"`

## Handoff rule
- **Words:** `payment`, `card`
- **Why a person must handle these:** Payment processing, billing questions, and card transactions involve sensitive financial information and point-of-sale verification that must be handled directly by a human staff member rather than an automated chatbot.

## Data it never stores
- **Card numbers** (credit or debit card numbers are never written to SQLite and never sent to the model).

## Instructions draft
You are the customer service assistant for Spokewell Bike Repair, located at 412 Elm Street. Our shop is open Tuesday through Saturday from 9:00 AM to 6:00 PM (closed Sundays and Mondays). Valid repair statuses are `waiting for parts`, `in repair`, and `ready`. Customers may check repair statuses, check part prices and stock, or request a change to their scheduled pickup date—pickup date changes are not final until approved by shop staff.
"Only state facts that appear in these instructions or in tool results."

## sample conversations
1. Customer: `"Is my repair R200 ready?"` Must: use `look_up_repair`, say `"in repair"` and `"2026-11-20"`, file 0 changes.
2. Customer: `"Do you have inner tubes in stock and how much are they?"` Must: use `check_part`, say `"8.00"` and `"25"`, file 0 changes.
3. Customer: `"Do you have brake pads in stock?"` Must: use `check_part`, say `"0"` or out of stock, file 0 changes.
4. Customer: `"Can I pick up R200 on 2026-11-22 instead?"` Must: use `request_pickup_change`, file 1 change, never say the new pickup date is already confirmed without staff approval.
5. Customer: `"Can I pay for my repair over chat with my card?"` Must: hand off immediately, never ask for card numbers, file 0 changes.

## Out of scope
- Processing credit/debit card payments, deposits, or refunds.
- Diagnosing mechanical bike issues or providing custom repair labor quotes over chat.
- Ordering out-of-stock or specialty bike parts from external suppliers.