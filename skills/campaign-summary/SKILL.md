---
name: campaign-summary
description: "Summarise one of your company's campaigns — an event whose attendees book through Getflights, such as a congress booking tool or a pre-registration — in the same layout every time: how many attendees are on the list, how far they got with registering, how many booked, and what the bookings add up to. Use when someone asks how their event is going, how many people registered or booked, what an event costs so far, or when a scheduled task asks to run a campaign summary."
---

# Summarising a campaign

A **campaign** is an event your company runs through Getflights: a congress whose
attendees book their flights through a booking tool, or a pre-registration. Each attendee
on its list is a **member**.

Tools: `campaign_search` to find the campaign, `campaign-member_search` for its attendees.

If `campaign_search` is not offered, say campaign details are not available on this
connection yet, and that the Getflights Pro site has them. Then stop. Do not try another
route to the data. If only `campaign-member_search` is missing, summarise what the
campaign shows and say the attendee counts could not be checked.

This skill runs with nobody watching as often as with someone asking, so it never stops to
ask a question. See *Running unattended* at the end.

## You see your own campaigns

Searching returns only your company's campaigns. If nothing comes back, the name is
usually spelled differently or the event belongs to another account. Say that plainly
rather than treating it as an error.

## 1. Find the campaign

Search `campaign_search` on `name` with `CONTAINS` and the part of the name people say
("Acme", not "261105-07 Acme LIS"). Names often start with the event's date; take the
dates from `start-date` and `end-date` instead.

- **One match:** use it.
- **Several:** keep the ones that have not ended (`end-date` today or later). If exactly one
  is left, use it and name the others under *Couldn't be checked*. Otherwise produce no
  summary: list every match with its dates there.
- **None:** say what you searched on.

Never ask which one was meant.

What the campaign itself shows: its `name`, `start-date`, `end-date`, `currency` and
`gross-price`. The gross price is the total of the bookings made under it so far.

## 2. The attendees

```
campaign-member_search
  filters: [{"field": "campaign", "id": "<campaign id>"}]
  fields:  {"campaign-member": ["campaign-member-status", "sales-orders", "opportunities"]}
  page:    {"size": 100}
```

**Always pass `fields` as above.** Ask for `name` only when someone asks who registered.

Read every page before counting. The response says how many there are in total, so a
count over one page is a count over part of the list.

Count the members per `campaign-member-status`:

| Status | Means |
|---|---|
| `open` | on the list, no invitation sent yet |
| `sent` | invitation sent |
| `responded` | opened their personal link |
| `completed` | asked for a quote or booked |

A status not in this table is still counted, under its own name.

Then, of all members:
- **booked**: at least one sales order in `sales-orders`;
- **asked for a quote, not booked**: opportunities in `opportunities` but no sales order.

No members is normal for an event without an attendee list. Say "no attendees on the list"
and carry on.

## What you cannot show here

- **Names only when asked.** The summary gives counts. List attendees by name only when
  someone asks who registered, or who has not.
- **No per-booking amounts, invoices or payments.** This connection carries the campaign's
  total, not the individual bookings or what has been paid. For those, point to the
  Getflights Pro site.

## Output

Keep this layout every time, so a weekly reader can compare one run with the next.

1. **Headline**, in three bullets:
   - the name, the dates, and "starts in N days", "running now" or "ended N days ago";
   - attendees: how many are on the list, and how many booked;
   - the total booked so far, in the campaign's currency.
2. **Registration**: one row per status with its count, in the order of the table above,
   then the total.
3. **Bookings**: booked, asked for a quote but not booked, and neither.
4. **Couldn't be checked**: anything skipped or ambiguous, with why. Leave it out if empty.
5. **How this was produced**: the campaign found and what was searched on, and that the
   total is the campaign's own sum of the bookings made under it.

Report what the records show. Do not guess why someone has not registered, and do not
suggest actions. For changes to the event or its list, contact your Getflights agent.

## Running unattended

When this runs from a scheduled task:

- **Never ask a question.** Where a choice is ambiguous, follow step 1 and list the rest
  under *Couldn't be checked*.
- **Take the campaign from the task's prompt.** If the prompt names none, produce no
  summary and say the task should name one.
- **A tool that is missing or refuses a call is a finding.** Say so under its own heading
  and carry on with the parts that still work. Do not stop silently or produce an empty
  report.
