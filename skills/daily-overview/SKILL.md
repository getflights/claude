---
name: daily-overview
description: Your daily Getflights briefing, in the same layout every day — which flights depart today and tomorrow, what is in your wallet, which options and offers are still open and when they expire, the deadlines coming up, and what is new since yesterday. Use when someone asks what is on today, for a daily summary, overview or briefing of their Getflights account, or when a scheduled task asks to run the daily overview.
---

# The daily overview

One read of the account, every morning, in a fixed layout so one day can be compared with
the next. It runs with nobody watching as often as with someone asking, so it never stops
to ask a question. See *Running unattended* at the end.

Every section stands on its own. **A tool that is not offered, or refuses a call, is a
finding**: note it under *Couldn't be checked* and carry on with the other sections.

Work out **today** and **yesterday** in local time before the first call. If you do not
know the reader's time zone, use Brussels time and say so at the end. **Moments you send
are in UTC with a `Z`**: the server ignores an offset, so the start of yesterday in
Brussels is sent as `<yesterday - 1 day>T22:00:00Z` in summer and `T23:00:00Z` in winter.

## 1. Departing today and tomorrow

Three searches, each with these `include`, `fields` and `page`:

```
pnr_search
  sort:    [{"field": "date"}]
  include: ["flights"]
  fields:  {"pnr": ["pnr-code", "pnr-status", "amount-of-passengers", "sales-order",
                    "flights"],
            "flight": ["name", "date", "departure-time", "arrival-date", "arrival-time",
                       "flight-number"]}
  page:    {"size": 50}
```

| Search | `filters` |
|---|---|
| Leaving today | `date` `=` today, `pnr-status` `<>` `Canceled` |
| Coming back today | `return-date` `=` today, `date` `<` today, `pnr-status` `<>` `Canceled` |
| Leaving tomorrow | `date` `=` tomorrow, `pnr-status` `<>` `Canceled` |

A booking's `date` is its first flight and `return-date` its last. Use only the flights
dated today (or tomorrow, for the third search).

**Never more than 50 a page.** With flights included, 100 bookings are over the size limit
and come back cut off.

Count bookings after merging the two copies of a reference (see below): on a busy day
there can be nearly twice as many records as bookings.

**Up to five bookings: list them.** Per booking: the reference, the route, the departure
and arrival time of each flight that day, the flight number, and the number of travellers.

**More than five: one line per destination.** Read every page, then group the bookings by
the destination of their trip:

- **Leaving:** where the booking's last flight that day lands. A connection is not a
  destination: BSL → FRA → CPH goes to CPH. If the last flight lands where the first one
  left (a day trip), use where the first one lands.
- **Coming back:** where the booking's first flight that day leaves from.

> **LIS** — 120 bookings, 126 travellers, from 23 airports, first departure 06:10, last
> 21:40, 18 with a connection
>
> **From LIS** — 98 bookings, 102 travellers, to 21 airports, first departure 08:35,
> last 22:15

- Travellers is the sum of `amount-of-passengers`, each booking counted once.
- Name the airports they come from (or go home to) when there are three or fewer;
  otherwise give how many.
- First and last departure are the times of the first flight of each booking.
- Add "N not yet ticketed" for bookings in **Option** or **Pending**, and "N with a
  connection" when there are any.
- Sort the lines by first departure. Show at most eight; put the rest in one line ("and 12
  more destinations, 31 bookings").
- List single bookings only when someone asks for them.

Large events put hundreds of bookings on one day. Reading them all is the price of a
correct count; do not group a first page and present it as the whole day.

- A flight's `date`, `departure-time` and `arrival-time` are local to its airports. Show
  them as they are; a flight that lands the next day has a later `arrival-date`.
- A flight's `name` reads like `20261015 - BRU ✈ AGP`: the route, with the destination
  after the ✈. `flight-number` comes without the airline's code (`3733`). Do not guess
  the code.
- If a reference comes back twice, follow *One reference, two bookings* in `my-bookings`
  and count it once. The two copies can list different flights; the confirmed one is
  right.
- `pnr-status` is often empty; then say nothing about it.
- Never ask for the passengers. The overview gives counts, not names.

If the `flights` include is refused, search again without it and say that routes and
times could not be shown.

## 2. Wallet

```
deposit_search
  fields: {"deposit": ["name", "currency", "available-amount"]}
```

One line per wallet, each in its own currency: its name and its available amount. An
empty amount is 0.00. A negative amount is shown as it is, below zero; whether that
matters is for the reader and their agent.

Then what moved since the start of yesterday:

```
transaction_search
  filters: [{"field": "created", "operator": ">=", "value": "<start of yesterday, UTC>Z"}]
  sort:    [{"field": "created", "direction": "desc"}]
  fields:  {"transaction": ["created", "currency", "transaction-amount", "deposit"]}
```

A positive amount is a top-up, a negative one money taken for a booking. Give the number
of movements and the net change per wallet, and list them when there are five or fewer.

Topping up is not possible through Claude. It is done from the Wallet page in Getflights
Pro.

## 3. Options and offers

```
quote_search
  filters: [{"field": "quote-status", "operator": "=", "value": "Presented"},
            {"field": "expiration-datetime", "operator": ">", "value": "<now, UTC>Z"}]
  sort:    [{"field": "expiration-datetime"}]
  include: ["pnrs"]
  fields:  {"quote": ["quote-number", "name", "opportunity-type", "departure-date",
                      "expiration-datetime", "amount-of-passengers", "gross-price",
                      "currency", "quote-date", "pnrs"],
            "pnr": ["pnr-code", "name", "date"]}
  page:    {"size": 50}
```

Split the result on `opportunity-type`:

- **Options** (`booking_tool`): bookings made through the booking tool, held until they
  expire. Unless the tickets are issued before then, the booking is released.
- **Offers awaiting your answer** (every other type: group, charter and so on): offers
  Getflights made that can still be accepted.

For an option, name it by its booking: the `pnr-code` and route from the included booking
(`ABC123 - gds - VIE, LIS`) and its travel `date`. The option's own `name` is only
"Booking Tool Quote <date> <company>" and its `departure-date` is empty. For an offer, use
its `name` and `departure-date`. Then the number of passengers, the price with its
currency, and when it expires. `expiration-datetime` is in UTC: convert it to local time,
and say "expires in 5 hours" or "expires Friday 23 October at 16:30".

Accepting an offer, issuing an option or letting one go happens in Getflights Pro.

## 4. Deadlines

Follow *Upcoming deadlines* in `deadline-lookup`: the deadlines and cancellation moments
due within 14 days, overdue ones included, split into **Urgent** and **Upcoming**.

An option's expiry is already in section 3. Do not repeat it here as a Quote Expiration
Deadline.

Overdue deadlines on trips that have already departed stay open in the records, sometimes
for years. Do not list them every day: give one line, "N older deadlines on past trips are
still marked open", and list them only when someone asks.

## 5. New since yesterday

No extra calls. Mark with **new**:
- options and offers whose `quote-date` falls on or after the start of yesterday;
- the wallet movements from section 2.

## Output

Keep this layout every day.

1. **Headline**, one bullet each:
   - flying today: how many bookings leave and how many travellers, how many come back,
     and the first departure;
   - wallet: the available amount per wallet;
   - options and offers: how many of each, and the soonest to expire;
   - deadlines: how many urgent, and the most pressing one;
   - since yesterday: N new offers or options, N wallet movements.
2. **Flying today**: leaving, then coming back. Up to five bookings each listed, more
   than five one line per destination (see section 1).
3. **Tomorrow**: the same rule, for the bookings leaving tomorrow.
4. **Wallet**.
5. **Options and offers**: options first, then offers, soonest expiry first.
6. **Deadlines**: urgent, then upcoming.
7. **Couldn't be checked**: each section that could not be read, with why. Leave it out
   when empty.

A section with nothing in it gets one line: "No departures today", "No open options or
offers", "Nothing due in the next two weeks".

Report what the records show. Do not guess why something is late, and do not suggest what
to do beyond where it can be done. For changes to a booking, contact your Getflights agent.

## Running unattended

When this runs from a scheduled task:

- **Never ask a question.** Where something is ambiguous, show what the records say and
  note the doubt under *Couldn't be checked*.
- **A tool that is missing or refuses a call is a finding.** Say so under its own heading
  and carry on. Do not stop silently or produce an empty report.
- **Today is the day the task runs**, in local time.
