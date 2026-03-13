<p align="center">
  <img src="./assets/banner.svg" alt="banner" />
</p>

<h1 align="center">bird-skill</h1>

<p align="center">claude code skill for bird, the twitter/x cli. originally by <a href="https://x.com/steipete">@steipete</a>.</p>

<p align="center">
  <img src="https://img.shields.io/badge/skill-v1.1.0-B4A7D6?style=flat-square&labelColor=1a1a2e" alt="skill version" />
  <img src="https://img.shields.io/badge/bird-v0.8.0-D4AF37?style=flat-square&labelColor=1a1a2e" alt="bird version" />
  <img src="https://img.shields.io/badge/zayd.wtf-D4AF37?style=flat-square&labelColor=1a1a2e" alt="site" />
</p>

<p align="center">
  <a href="#what-it-does">what it does</a> · <a href="#install">install</a> · <a href="#usage">usage</a> · <a href="#changelog">changelog</a>
</p>

<br>
<br>

<p align="center">
  <img src="./assets/stars1.svg" alt="·" />
</p>

<br>
<br>

## what it does

[bird](https://github.com/steipete/bird) is a fast cli for twitter/x, built by [@steipete](https://x.com/steipete). reads tweets, searches, posts, follows, checks your timeline. all from the terminal using your browser's saved cookies — no api keys, no oauth dance.

the original repo was removed from github. we keep it accessible at [zaydiscold/bird](https://github.com/zaydiscold/bird).

this is a claude code skill that wraps it. paste an x.com link into any conversation and your agent reads it directly. no browser tab, no webfetch, no auth setup. the skill is bash-only by design (`allowed-tools: Bash`) — that's the whole point: agents read twitter without opening a browser.

open tools should stay open.

works in claude code, codex, cursor, openclaw, gemini. one install, all agents.

<br>
<br>

<p align="center">
  <img src="./assets/stars2.svg" alt="·" />
</p>

<br>
<br>

## install

**step 1:** install the bird cli

```bash
# from zaydiscold/bird — universal arm64/x86_64 binary
curl -L https://github.com/zaydiscold/bird/releases/download/v0.8.0/bird -o bird
chmod +x bird
sudo mv bird /usr/local/bin/bird
```

> <sub>steipete's original tap (`brew install steipete/tap/bird`) may no longer be maintained — use the curl install above.</sub>

verify it's working:

```bash
bird whoami  # should return your twitter handle
```

bird uses safari or chrome cookies automatically. no api keys, no oauth dance.

for full cli docs and archive: [zaydiscold/bird](https://github.com/zaydiscold/bird)

**step 2:** install the skill

```bash
npx skills add zaydiscold/bird-skill@bird -g -y  # global, all agents
```

or install to a single agent:

```bash
npx skills add zaydiscold/bird-skill@bird -y  # current project only
```

<br>
<br>

<p align="center">
  <img src="./assets/stars3.svg" alt="·" />
</p>

<br>
<br>

## usage

once installed, the skill activates automatically. paste any x.com or twitter.com link and your agent reads it. no slash command needed.

```bash
# what the agent runs behind the scenes
bird read https://x.com/user/status/123456789   # single tweet
bird thread https://x.com/user/status/123456789  # full thread
bird search "query" -n 20                         # search tweets
bird mentions -n 20                               # your mentions
bird home -n 20                                   # for you feed
bird home --following -n 20                       # chronological
bird news --ai-only                               # trending topics
bird user-tweets @handle -n 20                    # someone's profile
```

posting requires a confirm from you first. reading is automatic.

```bash
bird tweet "text here"              # post a tweet
bird reply <url-or-id> "reply"      # reply to a tweet
bird follow @handle
bird bookmarks -n 20
bird likes -n 20
```

output options:

```bash
bird read <id> --json        # structured json
bird search "q" --plain      # no color, pipeable
```

<sub>all bird commands: read, thread, replies, search, mentions, home, bookmarks, likes, user-tweets, news, lists, list-timeline, following, followers, about, tweet, reply, follow, unfollow, unbookmark, whoami, check</sub>

<br>
<br>

<p align="center">
  <img src="./assets/stars4.svg" alt="·" />
</p>

<br>
<br>

## compatibility & limitations

- Works on macOS with a browser profile (Safari or Chrome) that has an authenticated bird session.
- Requires `bird` v0.8.0+ and a functioning cookie-backed auth state.
- The skill currently gates write actions behind explicit confirmation (`tweet`, `reply`, `follow`, `unfollow`, `unbookmark`).
- Inputs are validated before execution; only x.com / twitter.com content links, status IDs, and explicit action commands are supported.
- List-style outputs default to `N=12` for compact responses, then offer `show more` for additional chunks.
- This skill intentionally uses only `Bash` tooling and avoids browser automation/WebFetch for reads and writes.

## release verification checklist

Before publishing a new skill version, verify:

- `metadata.version` in `bird/SKILL.md` and the README skill badge both match the release tag.
- `SKILL_TESTS.md` core scenarios pass (trigger, functional, and failure-mode checks).
- `SKILL.md` preflight/auth flow works for: missing binary, unauthorized, and private/protected responses.
- `README.md` has matching changelog entry, installation steps, and compatibility notes.
- `npx skills publish` checks (or equivalent distribution check) include this directory with updated files.

Verification command examples:

```bash
# quick local consistency checks
grep -n "metadata:\n  version" bird/SKILL.md
grep -n "skill-v1.1.0" README.md
python -m markdown
# run your normal smoke tests using SKILL_TESTS.md
```


## changelog

### v1.1.0
- added explicit preflight command sequencing and auth gating
- added explicit write-command confirmation protocol for side-effect actions
- added URL normalization and malformed input rejection
- added default N=12 list output cap with show-more policy
- expanded troubleshooting cases (private/protected, suspended/deleted, malformed input, network/DNS, timeout)
- moved detailed operators/write/error guidance into `references/` docs and added `SKILL_TESTS.md` test matrix
- added compatibility + release verification checklist and version-sync rule

### v1.0.4
- smarter install detection (`command -v` + `~/.local/bin` fallback instead of `which`)
- search operators quick reference (from:, to:, filter:, date ranges, engagement filters)
- output presentation rules — agent knows when to show raw vs summarize vs curate
- batch URL handling — multiple tweet links processed sequentially
- cleaned up stale HTML comment in readme

### v1.0.3
- aligned skill with [anthropic's official skill guide](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)
- added `license`, `compatibility`, and standard `metadata` fields to frontmatter
- added negative trigger in description to prevent over-triggering on content strategy tasks
- added overview line at top of skill body
- added concrete examples section (URL read, search, post) per guide's recommended template
- renamed error handling → troubleshooting with structured error/cause/solution format

### v1.0.2
- skill now detects missing bird binary on invocation and offers to install from [zaydiscold/bird](https://github.com/zaydiscold/bird/releases)
- install section updated with curl fallback for when steipete's brew tap is unavailable

### v1.0.1
- updated description: x.com/twitter.com URL trigger is now first (stronger auto-invoke)
- added `bird v0.8.0` badge
- credits @steipete as original bird author
- auth line now shows `@ColdCooks` for clarity
- bash-only design note (no browser, no webfetch — that's the point)
- links to [zaydiscold/bird](https://github.com/zaydiscold/bird) for cli docs and archive

### v1.0.0
- initial release: read, search, thread, post, timeline
- cross-agent: claude code, codex, cursor, openclaw, gemini
- follows agentskills open standard

<br>
<br>

<p align="center">
  <a href="https://star-history.com/#zaydiscold/bird-skill&Date">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/svg?repos=zaydiscold/bird-skill&type=Date&theme=dark" />
      <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/svg?repos=zaydiscold/bird-skill&type=Date" />
      <img src="https://api.star-history.com/svg?repos=zaydiscold/bird-skill&type=Date&theme=dark" width="320" alt="star history chart" />
    </picture>
  </a>
</p>

<p align="center">mit. <a href="./LICENSE">license</a></p>

<br>
<br>

<p align="center">
  <img src="./assets/stars5.svg" alt="·" />
</p>

<br>
<br>

<p align="left"><strong>zayd / cold</strong></p>

<p align="center">
  <a href="https://zayd.wtf">zayd.wtf</a> · <a href="https://x.com/coldcooks">twitter</a> · <a href="https://github.com/zaydiscold">github</a>
  <br>
  <em>icarus only fell because he flew</em>
</p>

<p align="right">
  <strong>to do</strong><br>
  <sub>
  ☑ core skill: read, search, thread, post, timeline<br>
  ☑ cross-agent: claude code, codex, cursor, openclaw, gemini<br>
  ☑ follows agentskills open standard<br>
  ☑ auto-install bird if missing<br>
  ☑ aligned with anthropic's official skill guide<br>
  ☐ skills.sh listing<br>
  ☐ firefox profile support in skill<br>
  ☐ multi-account switching
  </sub>
</p>

<br>
<br>
<br>
<br>

<p align="center">
  <img src="./assets/wisps.svg" alt="" />
</p>
