---
name: bird
version: "1.0.1"
description: "Twitter/X CLI skill. Triggers automatically when user shares an x.com or twitter.com URL. Also use when user mentions a tweet/thread, asks about Twitter posts, wants to search, check mentions, see timeline, post/reply on X. Runs bird CLI directly — no browser, no WebFetch."
argument-hint: "[tweet-url-or-id] [action]"
allowed-tools: Bash
user-invocable: true
license: MIT
homepage: https://github.com/zaydiscold/bird-skill
metadata:
  clawdbot:
    emoji: "🐦"
    requires:
      bins:
        - bird
---

# Bird — Twitter/X CLI

**Auth:** Safari cookies auto-detected (logged in as @ColdCooks)
**Fallback:** `bird --chrome-profile "Default" <command>`
**Binary:** `/opt/homebrew/bin/bird`

## When invoked with a URL or ID

Run immediately — do not ask:
```bash
bird read $ARGUMENTS
```

For a thread URL:
```bash
bird thread $ARGUMENTS
```

## Read

```bash
bird read <url-or-id>               # single tweet
bird thread <url-or-id>             # full conversation
bird thread <url-or-id> --all       # all pages
bird replies <url-or-id>            # replies to a tweet
bird user-tweets @handle -n 20      # user's profile timeline
```

## Search

```bash
bird search "query" -n 20
bird search "from:@handle keyword" -n 10
bird search "term" --all            # all results, paginated
```

## Timeline & Discovery

```bash
bird home -n 20                     # For You feed
bird home --following -n 20         # Following (chronological)
bird mentions -n 20                 # @ColdCooks mentions
bird mentions -u @handle -n 20      # another user's mentions
bird bookmarks -n 20
bird likes -n 20
bird news --ai-only                 # trending / AI-curated
bird news --with-tweets --tweets-per-item 3
bird lists                          # your lists
bird list-timeline <list-id-or-url>
bird about @handle                  # account origin & location
bird following -n 50
bird followers -n 50
```

## Post & Engage

> Confirm before posting unless the user provided explicit text.

```bash
bird tweet "text"
bird reply <url-or-id> "reply text"
bird follow @handle
bird unfollow @handle
bird unbookmark <url-or-id>

# Media (up to 4 images or 1 video)
bird tweet "caption" --media /path/to/file.jpg --alt "description"
```

## Output Formats

```bash
bird read <id> --json               # structured JSON
bird read <id> --json-full          # JSON + raw API response
bird search "query" --plain         # no color/emoji (for piping)
```

## Auth Check

```bash
bird whoami      # confirm logged-in account
bird check       # check credential availability
```

## Behavior Rules

- If user pastes an x.com/twitter.com URL → `bird read <url>` immediately
- If URL is clearly a thread → `bird thread <url>`
- For search/mentions/timeline → run the appropriate command and present results
- Do NOT open browser tabs or use WebFetch for Twitter content
- Do NOT make up tweet content — only report what bird returns
