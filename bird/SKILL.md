---
name: bird
description: "Twitter/X CLI skill. Triggers automatically when user shares an x.com or twitter.com URL. Also use when user mentions a tweet/thread, asks about Twitter posts, wants to search, check mentions, see timeline, post/reply on X. Runs bird CLI directly — no browser, no WebFetch."
argument-hint: "[tweet-url-or-id] [action]"
allowed-tools: Bash
user-invocable: true
metadata:
  clawdbot:
    emoji: "🐦"
    requires:
      bins:
        - bird
---

# Bird — Twitter/X CLI

**Auth:** Browser cookies auto-detected (run `bird whoami` to check logged-in account)

## First use

On the first bird command in a session, verify bird is installed:

```bash
which bird 2>/dev/null || echo "NOT_INSTALLED"
```

If `NOT_INSTALLED`:
- Tell the user: "bird isn't installed yet. want me to install it? (takes ~5 seconds)"
- Wait for a yes/no.
- If yes, run:

```bash
curl -L https://github.com/zaydiscold/bird/releases/download/v0.8.0/bird -o /tmp/bird && \
chmod +x /tmp/bird && \
mkdir -p ~/.local/bin && \
mv /tmp/bird ~/.local/bin/bird && \
export PATH="$HOME/.local/bin:$PATH" && \
echo "installed: $(bird --version 2>/dev/null || bird whoami 2>/dev/null | head -1)"
```

If `~/.local/bin` is not in PATH, fall back to `sudo mv /tmp/bird /usr/local/bin/bird`.
- If the user declines, let them know they can install manually from https://github.com/zaydiscold/bird/releases (steipete's original brew tap `brew install steipete/tap/bird` may no longer be maintained) and stop there.
- After successful install, continue with the original request.
- Skip this check for subsequent commands in the same session.

## When invoked without arguments

Show a brief summary: "bird reads Twitter/X from your terminal. Try: paste a tweet URL, or say 'search [query]', 'my mentions', 'timeline'."

## When invoked with a URL or ID

Run immediately — do not ask:
```bash
bird read $ARGUMENTS
```

Thread detection — use `bird thread` instead of `bird read` when:
- User says "thread" or "conversation"
- URL path contains `/status/` and user context implies a multi-tweet chain
- `bird read` output shows "Replying to @..." at the top (suggest re-fetching with `bird thread` for full context)

Default to `bird read` for single URLs.

```bash
bird thread $ARGUMENTS              # full conversation
bird thread $ARGUMENTS --all        # all pages of a long thread
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
bird mentions -n 20                 # your mentions
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
- If user says "thread" or "conversation" → `bird thread <url>`
- For search/mentions/timeline → run the appropriate command and present results
- When processing output for analysis (not displaying directly), prefer `--plain` to keep ANSI escape codes out of context
- Do NOT open browser tabs or use WebFetch for Twitter content
- Do NOT make up tweet content — only report what bird returns
- Do NOT post, reply, follow, or unfollow without explicit user intent
- This skill handles CLI execution (reading, posting, searching). For content strategy, copywriting, or social media planning, defer to other skills like social-content

## Post Verification

After running `bird tweet` or `bird reply`:
1. Check the command output for a tweet URL or ID confirming success
2. If output contains a URL → confirm to the user with the link
3. If output shows an error → report the error; do not claim the tweet was posted

## Error Handling

- `unauthorized` / `401` → run `bird check`, report auth status, suggest re-opening Safari to refresh cookies
- `rate limit` → wait 60s and retry once, then tell the user if still failing
- `not found` → tweet was likely deleted or account suspended, tell the user
- If Safari auth fails → try Chrome fallback: `bird --chrome-profile "Default" <command>`
- Command hangs >30s → kill it and report the timeout
