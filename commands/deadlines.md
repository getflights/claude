---
description: Show your deadlines — everything due in the next two weeks, or one booking's
argument-hint: [booking reference, e.g. ABC123]
---

Follow the `deadline-lookup` skill.

If `$1` is given, show every deadline and cancellation moment on that booking, done or
not, as in *Deadlines on one booking*. Otherwise show *Upcoming deadlines*: everything due
within 14 days, overdue ones included.

Lead with what is urgent. For each item: the date written out, "in N days" or "N days
overdue", what has to happen in plain words, and which booking. Never ask for a
deadline's `contact`.

If nothing is due, say so simply. An empty list means nothing is due, not that something
went wrong.
