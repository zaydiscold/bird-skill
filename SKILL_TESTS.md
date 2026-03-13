# Bird Skill Test Matrix

## Trigger tests

- Should trigger:
  - `https://x.com/user/status/123`
  - `https://twitter.com/user/status/123`
  - `search AI updates`
  - `my mentions`
  - `tweet from today` (if explicit intent)

- Should not trigger:
  - `write social copy for this post` (should defer to content strategy skill)
  - unrelated websites and general web pages

## Functional tests

### Read/thread/search happy path
- Single tweet URL → execute `bird read`
- Thread wording + tweet URL → execute `bird thread`
- Multiple tweet URLs → execute sequential reads
- Search query with operators → execute `bird search`
- Mentions/home/bookmarks

### Write action protocol
- `tweet`, `reply`, `follow`, `unfollow`, `unbookmark`:
  - command is only executed after explicit confirmation

## Failure-mode tests

- Missing binary:
  - prompts install flow and stops until approved

- Unauthorized:
  - preflight check fails and user is guided to re-auth via browser

- Rate limit:
  - user is informed and no blind retries

- Not found/private/protected:
  - clear explanation, no hallucinated result

- Timeout/network failure:
  - user receives actionable retry guidance
