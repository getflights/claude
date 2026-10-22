---
name: reading-a-booking
description: Explain what is in a Getflights booking — where it stands, which flights the group takes and when, how many are going, what it costs and what is due next — in language someone outside the travel trade can act on. Use after finding a booking, when someone asks which flights they are on or when they leave and land, or what a status, a deadline or a charge means.
---

# Making sense of a booking

Most fields on a booking have trade names. Translate them.

## The flights

Read them with the booking:

```
pnr_read
  id:      <booking id>
  include: ["flights"]
  fields:  {"pnr": [<what the answer needs>, "flights"],
            "flight": ["name", "date", "departure-time", "arrival-date", "arrival-time",
                       "flight-number"]}
```

Name `flights` in the booking's own `fields` too. Without it the flights still come back,
but on a search they can no longer be matched to their booking.

- A flight's `name` reads like `20261015 - BRU ✈ AGP`: the date, then where it leaves from
  and where it lands, as airport codes. Say the route that way, or with the city when you
  know it for certain.
- `date`, `departure-time` and `arrival-time` are local to each airport. An overnight
  flight lands on a later `arrival-date`; say so.
- `flight-number` comes without the airline's code (`3733`). The airline is often in the
  booking's `name` (`261015 ACME SN AGP 41 PAX`). Do not guess the code otherwise.
- Put the flights in order of date and departure time. A return trip usually has two; a
  group split over several connections has more.
- **No flights linked** happens on some older bookings. Say the flights are not recorded
  on this booking and point to Getflights Pro. Do not say "no flights booked": that would
  alarm someone for no reason.
- If a reference exists twice, use the flights of the confirmed copy (see *One reference,
  two bookings* in `my-bookings`); the other copy can show an earlier timetable.

## What is not there

**Don't list who is travelling or their e-tickets.** If someone asks who is on the booking,
or for a traveller's ticket, say you don't show that here and point them at the Getflights
Pro site or their agent. Do not guess.

## Where a booking stands

`pnr-status` is one of four things. Say what each means rather than repeating the word:

- **Pending** — being put together. Nothing held yet.
- **Option** — seats held but not yet paid for, until a deadline, then released.
- **Ticketed** — tickets issued. Travel is secure.
- **Canceled** — no longer live.

On some bookings this is blank. Say it is not set rather than inventing a status.

## The two dates that matter

**`last-ticketing-date`** — the airline needs tickets issued by then, or it can release the
seats. This is behind most urgent situations.

**`expiration-date`** — when a held option lapses.

When either is close, lead with it. "Your seats are held until 12 March, and tickets need
issuing by 5 March" beats a status word.

`date` is the date of the first flight, `return-date` of the last, `arrival-date` when
they land. The times are on the flights.

## Who is travelling

`amount-of-passengers` is the total, broken down by `amount-of-children`,
`amount-of-infants` and `amount-of-young-adults`. Airlines price these differently, which
is why they are counted apart. Names are not shown here — see *What is not there*.

`passport-info-required` means the airline needs passport details before travel. Mention it
unprompted; it catches groups out.

`baggage` reads like `1PC x 23KG` — one piece, 23 kilos, per traveller.

## What it costs

`currency` applies to every amount.

- `total` — the full price, and `sub-total` before fees
- `total-adults`, `total-children`, `total-infants`, `total-young-adults` — the same split
  as the passenger counts, with `subtotal-*` alongside
- `booking-fee`, `transaction-fee` — Getflights charges, separate from the fare

Give a total with its currency, and say what a fee is for. If someone questions a charge,
explain what it covers; do not speculate about whether it can be waived.

## Things to get right

- **Say what happens next.** "Paid, and your tickets are issued" beats "Ticketed".
- **Dates in full.** "5 March", not "05/03".
- **Do not guess at a change.** Bookings cannot be changed here. To add a traveller, move a
  date or cancel, they contact their Getflights agent — do not speculate about what the
  airline might allow.
- **`eticket-document`** indicates an e-ticket has been generated. If they cannot find it,
  that is worth saying.
