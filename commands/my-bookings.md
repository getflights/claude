---
description: Show your upcoming Getflights bookings
---

Find this account's bookings with `pnr_search`, sorted by travel date, and show the ones
coming up rather than everything ever booked.

For each: the booking reference, where and when the group is travelling, how many people,
and where it stands in plain words ("seats held until 12 March", "tickets issued"). Take
the route and times from the flights (`include: ["flights"]`, at most 50 bookings a page),
as in *The flights* in `reading-a-booking`. Pass `fields` with only those.

Put anything with a deadline in the next couple of weeks at the top, and say what the
deadline is.

If there are none, say so simply. An empty result means this account has no bookings — not
that something went wrong.
