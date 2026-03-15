---
name: calendar
description: Google Calendar integration for Cleo — view upcoming events, check schedule, create events, update or cancel events, and search calendar. Uses the same Google OAuth credentials as Gmail.
version: 1.0.0
author: Claude Code for Cleo
triggers:
  - calendar
  - schedule
  - upcoming events
  - what's on
  - add event
  - create event
  - book meeting
  - check my schedule
  - cancel event
  - reschedule
  - free time
  - busy
---

# Calendar Skill

Access Google Calendar via the REST API. Uses the same client credentials as Gmail — only the token is different.

---

## Stored Credential Keys

| Key | Description |
|---|---|
| `GMAIL_CLIENT_ID` | OAuth2 client ID (shared with Gmail) |
| `GMAIL_CLIENT_SECRET` | OAuth2 client secret (shared with Gmail) |
| `CALENDAR_REFRESH_TOKEN` | Long-lived refresh token for Calendar |
| `CALENDAR_ACCESS_TOKEN` | Short-lived access token (auto-refreshed) |
| `CALENDAR_TOKEN_EXPIRES` | Unix timestamp when access token expires |

**First-time setup**: Jan will provide `CALENDAR_REFRESH_TOKEN`. Store it in memory. `GMAIL_CLIENT_ID` and `GMAIL_CLIENT_SECRET` are already stored from Gmail setup.

---

## Step 0: Refresh Access Token (run before every operation)

Check if `CALENDAR_TOKEN_EXPIRES` is in the past (or missing). If so:

```bash
curl -s -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=GMAIL_CLIENT_ID&client_secret=GMAIL_CLIENT_SECRET&refresh_token=CALENDAR_REFRESH_TOKEN&grant_type=refresh_token"
```

Extract `access_token` and `expires_in`. Store as `CALENDAR_ACCESS_TOKEN` and set `CALENDAR_TOKEN_EXPIRES` to current unix time + `expires_in`.

If you get 401 on any call, refresh and retry once.

---

## Operations

### List Upcoming Events

```bash
curl -s "https://www.googleapis.com/calendar/v3/calendars/primary/events?maxResults=10&singleEvents=true&orderBy=startTime&timeMin=$(date -u +%Y-%m-%dT%H:%M:%SZ)" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

Display as a numbered list: `[N] Date/Time | Title | Location (if any)`. Group by day if multiple days shown.

---

### Get Today's Events

```bash
curl -s "https://www.googleapis.com/calendar/v3/calendars/primary/events?maxResults=20&singleEvents=true&orderBy=startTime&timeMin=$(date -u +%Y-%m-%dT00:00:00Z)&timeMax=$(date -u +%Y-%m-%dT23:59:59Z)" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

---

### Get Events for a Date Range

Replace `TIME_MIN` and `TIME_MAX` with RFC3339 timestamps (e.g. `2026-03-15T00:00:00Z`):

```bash
curl -s "https://www.googleapis.com/calendar/v3/calendars/primary/events?maxResults=50&singleEvents=true&orderBy=startTime&timeMin=TIME_MIN&timeMax=TIME_MAX" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

---

### Search Events

```bash
curl -s "https://www.googleapis.com/calendar/v3/calendars/primary/events?q=SEARCH_TERM&maxResults=10&singleEvents=true&orderBy=startTime" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

---

### Get Event Details

```bash
curl -s "https://www.googleapis.com/calendar/v3/calendars/primary/events/EVENT_ID" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

---

### Create Event [REQUIRES JAN'S APPROVAL]

**Before creating**: Show Jan the full event details (title, date/time, duration, location, description) and wait for confirmation.

```bash
curl -s -X POST "https://www.googleapis.com/calendar/v3/calendars/primary/events" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "summary": "EVENT_TITLE",
    "location": "LOCATION",
    "description": "DESCRIPTION",
    "start": {"dateTime": "2026-03-15T10:00:00+01:00", "timeZone": "Europe/Vienna"},
    "end": {"dateTime": "2026-03-15T11:00:00+01:00", "timeZone": "Europe/Vienna"}
  }'
```

For all-day events, use `"date": "2026-03-15"` instead of `dateTime`.

Confirm: "Event created: [title] on [date] ✓"

---

### Update Event [REQUIRES JAN'S APPROVAL]

```bash
curl -s -X PATCH "https://www.googleapis.com/calendar/v3/calendars/primary/events/EVENT_ID" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"summary": "NEW_TITLE", "start": {...}, "end": {...}}'
```

Only include fields you want to change. PATCH updates partially; PUT replaces entirely.

---

### Delete Event [REQUIRES JAN'S APPROVAL]

```bash
curl -s -X DELETE "https://www.googleapis.com/calendar/v3/calendars/primary/events/EVENT_ID" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

Confirm: "Event deleted ✓"

---

### List All Calendars

```bash
curl -s "https://www.googleapis.com/calendar/v3/users/me/calendarList" \
  -H "Authorization: Bearer CALENDAR_ACCESS_TOKEN"
```

Use the calendar's `id` field in place of `primary` above to query non-primary calendars.

---

## Approval Policy

| Operation | Approval Required |
|---|---|
| View events / search / check schedule | None |
| Create event | Jan must confirm |
| Update / reschedule event | Jan must confirm |
| Delete / cancel event | Jan must confirm |

For write operations: "Ready to [create/update/delete] [event title] on [date]. Should I proceed?"

---

## Timezone

Jan is in **Europe/Vienna** (CET/CEST, UTC+1/+2). Always display times in local time. When creating events, use `"timeZone": "Europe/Vienna"` and offset `+01:00` (winter) or `+02:00` (summer).

---

## Error Handling

- **401**: Refresh access token and retry once
- **403**: Calendar scope issue — tell Jan "Calendar needs re-authorization"
- **404**: Event not found — confirm event ID is correct
- **429**: Rate limit — wait 2 seconds and retry once

---

## Display Tips

- Upcoming events: show next 5–10 in a clean list, grouped by day
- All-day events: label as `[All day]` instead of a time
- Empty calendar: "No events found for that period"
- After creating/updating: confirm with event title and date
