---
name: my-bookings
description: Find a Getflights booking — by booking reference, travel date, destination or group size — and see where it stands. Use whenever someone asks about their booking, when or where they travel, what it cost, or whether their tickets have been issued.
---

# Finding a booking

Tools: `pnr_search` to find bookings, `pnr_read` to open one.

If neither is available, say the connection is not offering them and suggest signing in
again. Do not try another route to the data.

## You see your own bookings

Searching returns only bookings belonging to this account's organisation. There is no way
to reach another customer's booking. If a search comes back empty it usually means the
reference belongs to a different account, or the booking predates it — say that plainly
rather than treating it as an error.

## Searching

| They know | Search on |
|---|---|
| The booking reference | `pnr-code` |
| When they travel | `date` (first flight), `return-date` (last flight), `arrival-date` |
| How many are travelling | `amount-of-passengers` |
| A deadline is near | `last-ticketing-date`, `expiration-date` |
| Where it stands | `pnr-status` |

Ways to match: exact (`=`), a range of dates (two conditions, `>=` and `<=`), or partial
text (`CONTAINS`, `STARTS_WITH`) for a half-remembered reference.

### Where it goes

There is no filter on a destination. Two ways in:

- **`name` with `CONTAINS`.** Most names carry airport codes after the reference:
  `ABC123 - BRU, CTA`, or `DEF456 - gds - CPH, OSL` with the booking system in between.
  It matches the three-letter code, not the city: for "our Lisbon trip", search `LIS` and
  say that is what you did. Some bookings, often groups, are named with the reference alone
  (`GHI789`) and never match.
- **The flights.** Every booking with flights linked carries its route there. When the
  `name` search finds nothing, or the answer has to be complete, search the travel dates
  instead with the flights included, and look for the code in the flights' `name`
  (`20261015 - BRU ✈ LIS`). See *The flights* in `reading-a-booking`.

If someone just asks what they have booked, sort by `date` and show what is coming up
rather than everything ever booked.

`page` takes `{number, size}`, up to 100 at a time, and **up to 50 when the flights are
included**: 100 bookings with their flights go over the size limit and come back cut off.
The response says how many matched in total — use that rather than implying a page is
everything.

Ask only for what the answer needs, with `fields` keyed `pnr`, for example
`{"pnr": ["pnr-code", "name", "pnr-status", "date", "return-date", "amount-of-passengers",
"last-ticketing-date", "sales-order"]}`. When the answer needs the route or the times, add
`include: ["flights"]`, `flights` to the booking's fields and a `flight` key, as in
*The flights* in `reading-a-booking`.

### One reference, two bookings

A reference can come back as two records. Usually one is the copy kept on the offer, and
the other is the confirmed booking, which has `sales-order` set. Both have the same travel
`date`. Use the confirmed one and say nothing about the other: the offer copy stops being
updated and can still read Option after the tickets are issued, or show an earlier
timetable in its flights.

If both or neither have a sales order, or the travel dates differ, they are separate
bookings. Show each one by travel date and status, and ask which is meant. In a list, show
a confirmed booking and its offer copy once.

## Answering

Lead with the answer. Someone asking "are we ticketed?" wants yes or no first.

Use the booking reference, never an internal record number.

If a booking is close to a deadline, say so even when it was not the question. A ticketing
deadline a few days out is the most useful thing you can tell someone.
