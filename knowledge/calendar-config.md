# Calendar Configuration

The `pm-support` agent uses calendar data for daily briefs, weekly plans, and priority scoring.

## ICS Calendar URL

<!-- CUSTOMIZE: Add your Outlook or Google Calendar ICS URL below.
     To find your ICS URL:
     - Outlook 365: Settings > Calendar > Shared calendars > Publish a calendar > ICS link
     - Google Calendar: Settings > Calendar settings > Secret address in iCal format
-->

```
[Paste your ICS URL here]
```

## How it works

1. The `pm-support` agent reads this file for the ICS URL
2. If a valid URL is found, it fetches events via WebFetch
3. Events are parsed (VEVENT entries) and matched to epics by title/Jira key
4. If no URL is configured, the agent falls back to `knowledge/calendar-events.md`

## Privacy note

The ICS URL grants read access to your calendar. Keep this file in `.gitignore` if your calendar contains sensitive meetings.
