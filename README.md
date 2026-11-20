# Getflights Pro for Claude

Ask about your Getflights bookings in plain language, straight from Claude.

For Getflights customers with a Getflights Pro login. Claude answers from your own
account only, in the language you ask in.

## Getting started

You need:

- your **Getflights Pro login**, the one you use on the Getflights Pro site;
- a **paid Claude plan**: Pro, Max, Team or Enterprise.

### In Claude (web and desktop app)

1. Open **Customize › Plugins**, choose **+**, then **Add marketplace**.
2. Paste the address of this repository: `https://github.com/getflights/claude`.
3. Install **Getflights Pro**.
4. Start a new chat and ask something, for example *"What's on today?"*. The first time,
   Claude asks you to connect Getflights Pro: sign in with your Getflights Pro login.

### In Claude Code

```
/plugin install getflights-pro --marketplace https://github.com/getflights/claude.git
```

On Claude Code versions before 2.1.275, add the marketplace first:

```
/plugin marketplace add getflights/claude
/plugin install getflights-pro
```

Then run `/mcp` and sign in with your Getflights Pro login.

### For administrators: Claude Code for your whole organization

To install the plugin for everyone in your organization who uses Claude Code, add this to
your organization's
[managed settings](https://code.claude.com/docs/en/plugins/org) — on claude.ai under
**Organization settings › Claude Code › Managed settings**, through your device management,
or in a `managed-settings.json` file:

```json
{
  "extraKnownMarketplaces": {
    "getflights-pro": {
      "source": { "source": "github", "repo": "getflights/claude" }
    }
  },
  "enabledPlugins": {
    "getflights-pro@getflights-pro": true
  }
}
```

The plugin installs at the start of each person's next Claude Code session. Everyone still
signs in with their own Getflights Pro login, so Claude only ever sees their own account.

### In ChatGPT

Getflights Pro is also available for the ChatGPT desktop app, with the same skills. Until
it is listed in ChatGPT's plugin directory, add it under **Plugins › Add marketplace** with
`https://github.com/getflights/chatgpt`; the README there has the steps.

## What you can do

- **Find a booking** — by reference, travel date or group size
- **See where it stands** — held, ticketed, or needing something from you
- **Check the flights** — route, flight numbers, and departure and arrival times
- **Check what happens next** — ticketing and payment deadlines, passport details
- **Understand what it costs** — the total, the split by traveller type, and the fees
- **Follow your events** — for a campaign your company runs through us: how many
  attendees registered, how far they got, how many booked, and what it adds up to
- **Keep track of deadlines** — what has to be signed, paid, sent or issued in the next two
  weeks, or on one booking, and from when cancelling costs money
- **Start the day with an overview** — flights departing today and tomorrow, your wallet,
  the options and offers still open, the deadlines coming up, and what is new since
  yesterday
- **Ask for a group offer** — send Getflights a request for seats for a group of 10 or
  more, a charter or a private jet: from where to where, when, how many and in which
  class, as on the Groups, Charters and Private Jets tabs. Claude checks it, shows it
  back, and sends it once you confirm; our group team replies with an offer. You can also
  ask what became of a request you sent earlier
- **Find and hold a flight** — for up to 9 travellers, as with the "Up to 9 pax" search:
  Claude searches the flights at your own prices, shows the flights and fares side by
  side, prices the one you choose, fills in the travellers with you and, once you
  confirm, holds it as an option. You buy the tickets in Getflights Pro

Try: *"What's the status of ABC123?"*, *"When do we travel in March?"*,
*"Do we still need passport details for that booking?"*, *"How many people booked for our
spring congress?"*, *"What's due this week?"*, *"What does cancelling ABC123 cost?"*,
*"What's on today?"*,
*"We need an offer for 25 people from Brussels to Lisbon on 12 March, back on the 15th"*,
*"Find flights for 2 adults from Brussels to Barcelona on 17 November, back on the 20th"*

The event summary and the daily overview can also arrive by themselves: create a
scheduled task in Claude with the prompt *Run campaign summary for <your event>*, e.g.
every Monday, or *Run the daily overview* every weekday morning. Neither stops to ask
anything; whatever couldn't be settled is listed at the end.

## What you cannot do

**You cannot buy tickets, or change or cancel a booking, through Claude.** Claude can hold
a flight for up to 9 travellers as an option; buying the tickets is done in Getflights
Pro. Existing bookings are read-only here: to make a change, contact your Getflights agent
or use the Getflights Pro site.

**You see only your own bookings.** Access matches your Getflights Pro account exactly.

**Claude doesn't list who is travelling.** It answers about the booking's own details —
status, dates, group size, cost and deadlines — and its flights, with route, flight number
and times, but it doesn't list the travellers or their e-tickets. For those, use the
Getflights Pro site. When you hold a flight through Claude, it asks for
the travellers' details only to fill in that booking.

## Commands

| Command | What it does |
|---|---|
| `/my-bookings` | Your upcoming bookings, soonest deadlines first |
| `/booking <reference>` | One booking explained in full |
| `/deadlines [reference]` | What is due in the next two weeks, or on one booking |
| `/today` | The daily overview |

## Help

Questions, or something not working? Contact your Getflights agent.
