---
name: instagram
description: Instagram integration for Cleo — post photos/videos/reels/carousels, comment on posts, read recent media, and check insights via the Instagram Graph API. Requires a Creator or Business account.
version: 1.0.0
author: Claude Code for Cleo
triggers:
  - instagram
  - post on instagram
  - instagram post
  - share on instagram
  - instagram photo
  - instagram reel
  - instagram story
  - comment on instagram
  - instagram comment
  - instagram insights
  - instagram analytics
---

# Instagram Skill

Post content, comment, and read data from Jan's Instagram account via the official Graph API. Requires a Creator or Business account linked to a Facebook Page (personal accounts are not supported by the API).

---

## Stored Credential Keys

| Key | Description |
|---|---|
| `INSTAGRAM_ACCESS_TOKEN` | Long-lived access token (~60 days, auto-refreshed) |
| `INSTAGRAM_TOKEN_EXPIRES` | Unix timestamp when token expires |
| `INSTAGRAM_ACCOUNT_ID` | Instagram Business/Creator account ID |

**First-time setup**: Jan provides the initial `INSTAGRAM_ACCESS_TOKEN` and `INSTAGRAM_ACCOUNT_ID`. Store both in memory. Token expires after 60 days — refresh before expiry (see Step 0).

---

## Step 0: Refresh Token If Needed (run before every operation)

Check if `INSTAGRAM_TOKEN_EXPIRES` is missing or in the past. If so, refresh:

```bash
curl -s "https://graph.instagram.com/refresh_access_token?grant_type=ig_refresh_token&access_token=INSTAGRAM_ACCESS_TOKEN"
```

Response contains `access_token` and `expires_in` (seconds, usually 5183944 ≈ 60 days). Store new `INSTAGRAM_ACCESS_TOKEN` and set `INSTAGRAM_TOKEN_EXPIRES` to current unix time + `expires_in`.

If you get 401 on any call below, refresh and retry once.

---

## Operations

### Get Recent Posts (read)

```bash
curl -s "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/media?fields=id,caption,media_type,media_url,permalink,timestamp&limit=10" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN"
```

Display as a numbered list: `[N] Type | Timestamp | Caption (first 80 chars) | Link`

---

### Get Post Details (read)

```bash
curl -s "https://graph.facebook.com/v21.0/MEDIA_ID?fields=id,caption,media_type,media_url,permalink,thumbnail_url,timestamp,username,like_count,comments_count" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN"
```

---

### Post Image [REQUIRES JAN'S APPROVAL]

**Important**: The image must be at a **publicly accessible URL** (not a local file). If Jan sends a local image, ask them to upload it somewhere public first (e.g. Google Drive public link, Dropbox, or any image host). Accepted format: JPEG only for single images.

**Step 1 — Create media container:**

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/media" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"image_url": "PUBLIC_IMAGE_URL", "caption": "CAPTION_TEXT"}'
```

Response contains `id` (the creation ID).

**Step 2 — Publish:**

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/media_publish" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"creation_id": "CREATION_ID"}'
```

Confirm: "Posted to Instagram ✓ — [permalink]"

---

### Post Reel [REQUIRES JAN'S APPROVAL]

Video must be at a publicly accessible URL. MP4, H.264, AAC audio, max 15 min, min 3 sec.

**Step 1 — Create container:**

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/media" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"media_type": "REELS", "video_url": "PUBLIC_VIDEO_URL", "caption": "CAPTION_TEXT", "share_to_feed": true}'
```

**Step 2 — Wait for processing.** Poll status until `status_code` = `FINISHED` (check every 10 seconds, max 5 tries):

```bash
curl -s "https://graph.facebook.com/v21.0/CREATION_ID?fields=status_code" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN"
```

**Step 3 — Publish** (same as image publish step 2 above).

---

### Post Carousel (multiple images) [REQUIRES JAN'S APPROVAL]

All images must be at publicly accessible URLs. Up to 10 items.

**Step 1 — Create a container for each image** (repeat for each):

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/media" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"image_url": "PUBLIC_IMAGE_URL_N", "is_carousel_item": true}'
```

Collect all returned `id` values.

**Step 2 — Create carousel container** (comma-separated list of IDs):

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/media" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"media_type": "CAROUSEL", "children": ["ID1", "ID2", "ID3"], "caption": "CAPTION_TEXT"}'
```

**Step 3 — Publish** (same as image publish step 2 above).

---

### Comment on a Post [REQUIRES JAN'S APPROVAL]

Works on any public post (own or others') if you have the media ID.

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/MEDIA_ID/comments" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"message": "COMMENT_TEXT"}'
```

---

### Reply to a Comment [REQUIRES JAN'S APPROVAL]

```bash
curl -s -X POST "https://graph.facebook.com/v21.0/COMMENT_ID/replies" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"message": "REPLY_TEXT"}'
```

---

### Get Comments on a Post (read)

```bash
curl -s "https://graph.facebook.com/v21.0/MEDIA_ID/comments?fields=id,text,username,timestamp" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN"
```

---

### Get Post Insights (read)

```bash
curl -s "https://graph.facebook.com/v21.0/MEDIA_ID/insights?metric=impressions,reach,likes,comments,shares,saved" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN"
```

---

### Check Publishing Limit (read)

Useful to know how many posts remain in the 100/day quota:

```bash
curl -s "https://graph.facebook.com/v21.0/INSTAGRAM_ACCOUNT_ID/content_publishing_limit?fields=config,quota_usage" \
  -H "Authorization: Bearer INSTAGRAM_ACCESS_TOKEN"
```

---

## Approval Policy

| Operation | Approval Required |
|---|---|
| Read posts, comments, insights | None |
| Check publishing limit | None |
| Post image, reel, carousel | Jan must confirm |
| Comment or reply | Jan must confirm |

For posts: Show Jan the full caption + image URL and ask "Ready to post this to Instagram. Should I proceed?"

For comments: Show the comment text and target post, ask "Ready to post this comment. Should I proceed?"

---

## Error Handling

- **190 / OAuthException**: Token expired — refresh and retry once
- **100 invalid parameter**: Check media URL is publicly accessible and image is JPEG
- **4 / API call limit**: Rate limit hit — wait 60 seconds and retry once
- **368**: Account action blocked by Meta — tell Jan to check the Instagram app
- **Unsupported account type**: Account must be Creator or Business — personal accounts block API access

---

## Notes

- Rate limit: 100 posts per 24 hours (rolling window)
- Hashtag search requires `instagram_manage_insights` scope — if not available, skip that feature
- Media URLs must stay publicly accessible until after publishing completes
- Stories are supported by the API (`media_type: STORIES`) but expire after 24 hours — add to a future version if needed
