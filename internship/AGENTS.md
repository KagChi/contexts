Always call ltm-mcp_search_memories and search based on scope bmw-internship or by collection bmw-internship-rules and DO NOT CLEAN UP THIS CONTEXT.

## Active Plans

Per-phase action files live in the ai-meeting-agent repo at
`.opencode/plans/`. **One file per phase**; each describes the work for that
phase only, and is deleted from that directory when the phase is complete.

## Extern Repo Commit & Push Rule (mandatory)

EEdits to the extern `bmw-ntust-internship/internship` repo — the source todo
(`docs/daily-logs/08_MeetingAgent.md`), study notes, `Daily-Logs.md`, and this
repo's `AGENTS.md` rules — must not be left dangling locally. Whenever a Daily-Logs
Sync change or any rule edit touches that repo, commit and push in the **same
turn**, before posting the #812 comment:

1. **Commit — REQUIRES explicit permission.** (unchanged from current)
2. **Push — still requires explicit permission.** (unchanged from current)
3. **#812 links reference the pushed HEAD.** (unchanged from current)
4. **`Daily-Logs.md` must stay in sync with #812.** `Daily-Logs.md` in the
   internship repo is the aggregated daily log and **must mirror the #812 comment
   thread**. After posting or PATCHing a #812 comment for a date, ensure
   `Daily-Logs.md` has the matching entry (date header, short-term goals,
   daily-log bullets). Apply the same correction to `Daily-Logs.md` whenever a
   #812 comment is PATCHed. Treat #812 as the live record and reconcile
   `Daily-Logs.md` to it; never let the two drift.

Rationale: the #812 comment thread and its links are the lab mentors' view of
progress; a link to unpushed content is a dead link. Keeping the extern remote
current — and `Daily-Logs.md` reconciled to #812 — also prevents drift between the
two workspace mirrors.

## Issues Sync Rule (mandatory)

The intern's daily log lives as a comment thread on
[bmw-ntust-internship/internship issue #812](https://github.com/bmw-ntust-internship/internship/issues/812).
Whenever you complete a meaningful chunk of work on ai-meeting-agent, you
**must** post a continuation comment to that thread describing what was
done. The comment is public, so do not post secrets, internal paths beyond
the repo root, or anything you would not want the lab mentors to read.

Comment format (matches the existing daily-log pattern in the thread):

```
# YYYY/MM/DD

## Short-term Goals
#### <one-line summary of this session's slice>

**Daily Logs:**
- HH:MM-HH:MM <what you did, with concrete file/function pointers>

**Links:**
- <relevant URLs — todo, study notes, plan files>
- Commits: <repo>/compare/<first_commit>...<last_commit>
```

**Comment content guidelines:**
- Use commit-based timestamps from `git log` for accuracy
- Include only code changes and technical work (no planning/discussion unless it resulted in code)
- Reference the BMW upstream repo (`bmw-ece-ntust/ai-meeting-agent`) in all workspace links, not personal forks
- Keep format minimal: date, goal, time ranges with brief descriptions, links
- Use commit range instead of single "latest commit" link to show all commits in the session
- Get first commit: `git log --oneline --reverse | head -1`
- Get last commit: `git log --oneline -1`
- **One bullet per continuous work stretch.** A "work stretch" = any
  continuous period of work (no significant break). Each stretch gets
  exactly one bullet.
- **Time range per bullet = first commit → last commit of that stretch.**
  Continuous, no gaps within a stretch.
- **New stretch = new bullet.** After a break (lunch, end of day, stopping
  and resuming later), start a fresh bullet. The gap between stretches is
  expected and allowed (not an error).
- **When PATCHing an existing same-day comment within the same stretch:**
  rewrite the current bullet to extend its time range (first commit →
  latest commit). Do NOT append a new bullet for the same stretch.
- **One bullet per phase, with continuous time ranges — ALWAYS derived
  from commit timestamps, NEVER from the current clock.** Each phase gets
  its own bullet. Time ranges must be continuous — the next phase's bullet
  starts where the previous one ended (e.g., `10:05-11:18` then
  `11:18-11:48`), no gaps.
  - ❌ **Known failure mode:** using the current time for both ends,
    producing a zero-length range like `14:32-14:32`. This happens when
    the time is taken from `date` / "now" instead of `git log`. This is
    ALWAYS wrong — a real work stretch has duration.
  - ✅ Start of range = timestamp of the phase's **first commit**
    (`git log --format='%ad' --date=format:'%H:%M' <first-commit> -1`).
  - ✅ End of range = timestamp of the phase's **last commit**, or the
    start of the next phase's bullet.
  - **Sanity check before posting:** if any bullet's start time equals its
    end time, or any time matches the current wall-clock time, stop and
    re-derive from `git log`.

Sync in lockstep with the Daily-Logs Sync Rule:

1. **Code complete → comment**: when you flip items in the todo, update
   the #812 thread in the same turn. **One comment per day** — **ALWAYS
   check first if a comment for today exists** by querying:
   `gh api repos/bmw-ntust-internship/internship/issues/812/comments --jq '.[] | select(.created_at | startswith("YYYY-MM-DD"))'`
   If found, PATCH it in place to merge into the current bullet — rewrite
   its time range (first commit → latest commit of that stretch). Append a
   new bullet only when a new work stretch begins. Only post a new comment
   (`gh issue comment 812 --repo bmw-ntust-internship/internship --body-file <tmp>`)
   when no comment exists yet for the current calendar day. **NEVER create
   a new comment without checking first.**
2. **Comment → plan link**: when you add a new plan file to
   `.opencode/plans/` in the ai-meeting-agent repo that the user should be
   able to read from the thread, include its GitHub URL in the comment's
   Links block.
3. **Daily-log daily**: post at least one comment per working day even if
   no code changed — a one-liner is fine ("no code changes; planning
   only").
4. **Edit in place, never delete**: if a comment needs a fix (wrong time,
   typo, broken link), edit it in place with the `gh api` PATCH endpoint —
   `PATCH repos/bmw-ntust-internship/internship/issues/comments/<id>` — do
   **not** delete and repost. **Default to PATCH for any correction**, even
   trivial ones (time ranges, single typos, link fixes). A gap in the
   thread is a permanent public record of the mistake, and the thread is
   the lab mentors' log of progress. The only narrow exception is a comment
   posted within the last few minutes with no replies and nothing depending
   on its id; even then, prefer edit.

   **Single-PATCH correction workflow:** When you need to make multiple
   corrections to the same comment (iterative feedback from the user),
   PATCH the same comment multiple times in sequence. Do not create
   multiple new comments or ask whether to post a correction comment —
   just PATCH the existing one directly. Use the same comment ID for all
   iterations until the user is satisfied with the result.
5. **Ask before documenting to the issue**: before posting a new comment
   to #812 that documents a decision, rule, format, or piece of behavior,
   **ask the user first** ("should I document this on #812?"). The user
   curates the public thread and may want the change kept as a one-off
   instead. This applies to new comments, not to PATCHes fixing an
   already-posted comment (step 4 covers that case).

When a fresh session lands on this branch, the first action is: read
`AGENTS.md` (which proxies `CLAUDE.md` via the `@CLAUDE.md` token at the
top — if `CLAUDE.md` exists, read it next so project-level
build/test/conventions are in context)
