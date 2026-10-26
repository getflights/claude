---
name: deadline-lookup
description: See your Getflights deadlines — everything due in the next two weeks, or every deadline on one booking — what has to be signed, paid, sent or issued by when, and the moments after which cancelling costs money. Use when someone asks what is due, what their deadlines are, when they have to pay or send the passenger names, whether a contract still needs signing, or what cancelling a booking would cost and from when.
---

# Your deadlines

A **deadline** is something you have to do on a booking by a given day: sign the
contract, pay the deposit or the rest, send the passenger names, or have the tickets
issued. A **cancellation moment** is the day from which cancelling a booking costs a
given amount. Getflights Pro shows both together on its Deadlines page, and so does
this skill.

Tools: `task_search` for the deadlines, `cancellation-rule_search` for the cancellation
moments, and `pnr_search` to find a booking by its reference.

If `task_search` is not offered, say deadlines are not available on this connection yet
and that the Deadlines page in Getflights Pro has them. Then stop. If only
`cancellation-rule_search` is missing, show the deadlines and say the cancellation
moments could not be checked.

## You see your own deadlines

The deadlines come from your company's bookings only. An empty list is good news: say
"nothing due in the next two weeks" rather than treating it as an error.

## Upcoming deadlines

This is the default, and it matches the Deadlines page: everything not yet done that is
due within 14 days, including anything overdue.

**The deadlines:**

```
task_search
  filters: [{"field": "task-custom-type", "operator": "IN",
             "value": "Signed Contract Deadline,Passenger List Deadline,Ticket Issuance Deadline,Deposit Payment Deadline,Full Payment Deadline,Quote Expiration Deadline"},
            {"field": "task-status", "operator": "<>", "value": "Completed"},
            {"field": "due-date", "operator": "<", "value": "<today + 14 days>"}]
  sort:    [{"field": "due-date"}]
  fields:  {"deadline": ["name", "due-date", "task-status", "task-custom-type", "related-to-object"],
            "booking":  ["name", "departure-date", "external-reference"],
            "quote":    ["name"]}
  include: ["related_to_object"]
  page:    {"size": 100}
```

Keep the `task-custom-type` filter: it limits the list to the deadlines Getflights Pro
shows. Never ask for `contact`: the list is about what is due, not who.

A **Quote Expiration Deadline** belongs to an offer, not a booking: its
`related-to-object` is the quote. Name it by the quote's `name`.

**The cancellation moments:**

```
cancellation-rule_search
  filters: [{"field": "sales-order", "operator": "<>", "value": "null"},
            {"field": "date", "operator": ">=", "value": "<today>"},
            {"field": "date", "operator": "<=", "value": "<today + 14 days>"}]
  sort:    [{"field": "date"}]
  fields:  {"cancellation-rule": ["date", "days", "amount", "cancellation-amount-type",
                                  "cancellation-rule-type", "cancellation-partial-type",
                                  "free-amount", "sales-order"],
            "booking": ["name", "departure-date", "sales-order-status", "currency",
                        "external-reference"]}
  include: ["sales_order"]
```

Then leave out, as the Deadlines page does:
- moments on a booking whose `sales-order-status` is `Cancelled` (it is often empty,
  which is not cancelled);
- moments that are only "100 % of the fare": `amount` 100 or more, a `Percent` amount
  type, no `free-amount` and no `cancellation-partial-type`.

**Together:** merge both lists, sort by date, and split them:
- **Urgent**: overdue, or due today or within 5 days;
- **Upcoming**: due in 6 to 14 days.

Read every page before counting. The response says how many there are in total.

## Deadlines on one booking

When someone names a booking, show everything on it, done or not, at any date.

1. Find it with `pnr_search` on `pnr-code`, with `sales-order` and `return-date` among the
   `fields`. If the reference comes back twice, follow *One reference, two bookings* in
   `my-bookings`. Take its `sales-order` id. A booking without one has no deadlines yet:
   say so.
2. `task_search` with the six-type filter above and `due-date` `<=` the booking's
   `return-date`, every status, `related-to-object` among the `fields`, and read every
   page. Keep the deadlines whose `related-to-object` id is the booking's. **Filtering on
   `related-to-object` itself returns nothing**, so match the id yourself.
3. `cancellation-rule_search` with only `{"field": "sales-order", "id": "<sales-order id>"}`.
   Leave out the "100 % only" moments as above.

Show a completed deadline as **Completed** and a cancellation moment in the past as
**Passed**, so the list still reads as the booking's history.

## Reading a deadline

`name` says which deadline it is:

| Deadline | What you have to do |
|---|---|
| Signed Contract Deadline | Sign the contract for the booking |
| Deposit Payment Deadline | Pay the deposit agreed for the booking |
| Full Payment Deadline | Pay the rest of the booking |
| Passenger List Deadline | Send the names (and details) of everyone travelling |
| Ticket Issuance Deadline | Have the tickets issued; after this the seats can be lost |
| Quote Expiration Deadline | Accept the offer before it runs out |

- The contract and payment deadlines mark themselves done once the contract is signed or
  the payment is booked. A payment can take a few working days to be booked; until then
  its deadline stays open. If someone says they already paid or signed, report what the
  deadline shows, mention that, and point them to their Getflights agent. Do not guess
  further why it is open.
- `task-status` is Open or In Progress. Both mean not done.
- **`due-date` is a UTC timestamp.** Convert it to local time before you take the date, as
  Getflights Pro does; use Brussels time when you do not know the reader's. Most are
  midnight UTC (`2026-10-22T00:00:00+00:00`), which stays the same day in Europe, but one
  at `2025-07-20T22:30:00+00:00` is due on 21 July in Brussels.
- Overdue deadlines can go back years, on trips that have already departed. List them as
  the records show them; the booking's departure date makes the age plain.
- Say "in 3 days" or "2 days overdue", and give the date in full: "Friday 23 October".

## Reading a cancellation moment

From `date` on, cancelling the booking costs what the moment says, until the next moment
takes over. `date` is a plain date, `days` how many days before departure it falls. Word
the cost the way Getflights Pro does:

| On the moment | Say |
|---|---|
| `amount` 0 | free of charge |
| `Fixed` | €X per passenger |
| `Fixed per Group` | €X for the group |
| `Percent BF` | X % of the base fare |
| `Percent BF + YQ` | X % of the base fare and the fuel surcharge |
| `cancellation-partial-type` *Reduced to min group size* | seats can be reduced to the minimum group size free of charge |
| `free-amount` set | N % of the booked seats can be cancelled free of charge; for the rest, the cost above |

- `cancellation-rule-type` **Full** is cancelling the whole booking ("Cancellation
  moment"); **Partial** is cancelling part of the group ("Partial cancellation moment").
- The currency is the booking's.
- Say what the moment means: "Up to 22 October cancelling is free; from 23 October it
  costs 50 % of the base fare." Do not advise whether to cancel.

## Which booking

Name each booking by its `name` (for example `261015 ACME SN AGP 41 PAX`) and departure
date, and add your own reference (`external-reference`) when it is set. Never name it by a
number or id. In a sentence, put the name in double quotes:

> The full payment for "261015 ACME SN AGP 41 PAX" (departs 15 October) is due Friday
> 2 October, in 3 days.

A name that stands alone, such as in a table cell, goes without quotes. If someone wants
the booking reference, look it up with `pnr_search` filtered on `sales-order` by the id. A
group can be split over several references (`ABC123` and `DEF456`); give them all.

## What you cannot do here

You cannot mark a deadline done, move it, or upload a contract or a passenger list
through Claude. Those happen in Getflights Pro, or through your Getflights agent. Do not
suggest a deadline can be moved; that is for the agent to say.

## Output

Lead with what is urgent. Then, per item: the date, "in N days" or "N days overdue", what
is due in plain words, and which booking. Group the items by booking only when someone
asks about one booking.
