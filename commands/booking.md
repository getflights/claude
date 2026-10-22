---
description: Look up one booking and explain where it stands
argument-hint: <booking reference, e.g. ABC123>
---

Look up booking `$1` with `pnr_search` filtered on `pnr-code`, with `sales-order` among
the `fields`. If it comes back twice, follow *One reference, two bookings* in `my-bookings`:
usually that means using the record with a sales order. Then `pnr_read` it with
`include: ["flights"]`, as in *The flights* in `reading-a-booking`. Pass `fields` with only
what the points below need, and `flights` among them.

Explain, in plain language:

1. **Where it stands** — and what that means in practice, not just the status word
2. **When and how they travel** — each flight in order: the date written out in full, the
   route, departure and arrival time, and the flight number
3. **How many are travelling** — the total, and the breakdown if there are children or
   infants
4. **What happens next** — the next deadline and what has to happen before it
5. **What it costs** — the total with its currency, and what the fees are for

Mention if passport details are still needed, or if an e-ticket has been issued.

Don't list who is travelling. If that is what they wanted, say so and point them at the
Getflights Pro site. If a booking has no flights linked, say the
flights are not recorded on it — do not report that as though nothing were booked.

If nothing matches, say the reference was not found on this account and suggest checking
it; bookings belonging to another account are not visible here.
