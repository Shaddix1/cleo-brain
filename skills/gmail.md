---
name: gmail
description: Gmail integration for Cleo - read inbox, send emails, search, reply, and manage emails via Gmail REST API using OAuth2 tokens stored in memory.
version: 1.0.0
author: Claude Code for Cleo
triggers:
  - check email
  - read email
  - send email
  - inbox
  - gmail
  - email
  - unread messages
  - draft email
  - reply to email
---

# Gmail Skill

Access Gmail via the REST API. Credentials stored in your persistent memory. Always refresh the access token before operations.

---

## Stored Credential Keys

| Key | Description |
|---|---|
| `GMAIL_CLIENT_ID` | OAuth2 client ID |
| `GMAIL_CLIENT_SECRET` | OAuth2 client secret |
| `GMAIL_REFRESH_TOKEN` | Long-lived refresh token |
| `GMAIL_ACCESS_TOKEN` | Short-lived access token (auto-refreshed) |
| `GMAIL_TOKEN_EXPIRES` | Unix timestamp when access token expires |

**First-time setup**: Jan will provide values for the first 3 keys. Store them in memory. The last 2 are managed automatically.

---

## Step 0: Refresh Access Token (run before every operation)

Check if `GMAIL_TOKEN_EXPIRES` is in the past (or missing). If so, refresh:

```bash
curl -s -X POST "https://oauth2.googleapis.com/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "client_id=GMAIL_CLIENT_ID&client_secret=GMAIL_CLIENT_SECRET&refresh_token=GMAIL_REFRESH_TOKEN&grant_type=refresh_token"
```

Response contains `access_token` and `expires_in` (seconds, usually 3600). Store new `GMAIL_ACCESS_TOKEN` in memory. Set `GMAIL_TOKEN_EXPIRES` to current unix time + `expires_in`.

If you get 401 on any call below, refresh and retry once.

---

## Operations

### List Inbox

```bash
curl -s "https://gmail.googleapis.com/gmail/v1/users/me/messages?maxResults=10&labelIds=INBOX" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN"
```

Returns a list of message IDs. Then fetch metadata for each:

```bash
curl -s "https://gmail.googleapis.com/gmail/v1/users/me/messages/MESSAGE_ID?format=metadata&metadataHeaders=From&metadataHeaders=Subject&metadataHeaders=Date" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN"
```

Display as a numbered list: `[N] From | Subject | Date`. Mark unread if `UNREAD` label present in `labelIds`.

---

### Read Email (full content)

```bash
curl -s "https://gmail.googleapis.com/gmail/v1/users/me/messages/MESSAGE_ID?format=full" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN"
```

Body is in `payload.body.data` (or in `payload.parts[].body.data` for multipart). Decode base64url:

```bash
echo "BASE64URL_STRING" | tr -- '-_' '+/' | base64 -d
```

Show: sender, subject, date, decoded body. Strip quoted reply chains unless Jan asks for the full thread.

---

### Search Emails

```bash
curl -s "https://gmail.googleapis.com/gmail/v1/users/me/messages?q=QUERY&maxResults=10" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN"
```

Query syntax examples:
- `from:person@example.com` — by sender
- `subject:meeting notes` — by subject
- `is:unread` — unread only
- `after:2026/01/01` — date range
- `has:attachment` — with attachments
- Combine: `from:jan is:unread`

---

### Send Email [REQUIRES JAN'S APPROVAL]

**Before sending**: Show Jan the full email (To, Subject, Body) and wait for explicit confirmation.

Build and encode the message:

```bash
printf "To: RECIPIENT\r\nSubject: SUBJECT\r\nContent-Type: text/plain; charset=utf-8\r\nMIME-Version: 1.0\r\n\r\nBODY" | base64 | tr '+/' '-_' | tr -d '='
```

Then send:

```bash
curl -s -X POST "https://gmail.googleapis.com/gmail/v1/users/me/messages/send" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"raw": "BASE64URL_ENCODED_MESSAGE"}'
```

---

### Reply to Thread [REQUIRES JAN'S APPROVAL]

Same as send, but include threading headers and `threadId`:

Email headers to add:
- `In-Reply-To: ORIGINAL_MESSAGE_ID`
- `References: ORIGINAL_MESSAGE_ID`

```bash
curl -s -X POST "https://gmail.googleapis.com/gmail/v1/users/me/messages/send" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"raw": "BASE64URL_ENCODED_MESSAGE", "threadId": "THREAD_ID"}'
```

---

### Create Draft [No approval needed]

```bash
curl -s -X POST "https://gmail.googleapis.com/gmail/v1/users/me/drafts" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"message": {"raw": "BASE64URL_ENCODED_MESSAGE"}}'
```

---

### Mark as Read

```bash
curl -s -X POST "https://gmail.googleapis.com/gmail/v1/users/me/messages/MESSAGE_ID/modify" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"removeLabelIds": ["UNREAD"]}'
```

---

### Trash Email [REQUIRES JAN'S APPROVAL]

```bash
curl -s -X POST "https://gmail.googleapis.com/gmail/v1/users/me/messages/MESSAGE_ID/trash" \
  -H "Authorization: Bearer GMAIL_ACCESS_TOKEN"
```

---

## Approval Policy

| Operation | Approval Required |
|---|---|
| Read inbox / search / read email | None |
| Create draft | None |
| Mark as read / star | None |
| Send email / reply | Jan must confirm |
| Trash / delete | Jan must confirm |

For send/trash: Say "Ready to send this email to [recipient] with subject [subject]. Should I proceed?" and wait.

---

## Error Handling

- **401**: Refresh access token and retry once
- **403**: Scope issue — tell Jan "Gmail needs additional permissions"
- **429**: Rate limit — wait 2 seconds and retry once
- **400 invalid base64**: Recheck encoding, ensure padding removed and chars swapped

---

## Display Tips

- Inbox: numbered list, bold unread, truncate subjects at 60 chars
- Full email: show headers cleanly, skip raw MIME noise
- Search results: show count + list
- After sending: confirm "Sent to [recipient] ✓"
