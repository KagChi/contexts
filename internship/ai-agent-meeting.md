## Documentation Sync Rule (mandatory)

Whenever a change affects user-facing configuration, features, build,
deployment, or usage of ai-meeting-agent, update **both** the todo and study
notes in this repo in the same commit:

- **`docs/daily-logs/08_MeetingAgent.md`**: check off completed items
  (`- [ ]` → `- [x]`), add new phases or items when the scope changes.
- **`docs/study-notes/14_Simple_Whisper_Transcription_Service.md`**: update
  implementation details when the ai-meeting-agent transcription client
  evolves (new providers, config changes, API shape changes).

If a change touches neither the todo nor the study notes (e.g. formatting
fix, typo), this rule does not apply. When in doubt, update.

**Two-repo structure:**
- **This repo** (`/Users/kagchi/Documents/projects/bmw-ntust-internship`, branch `2026-TEEP-5-Samuel`): Source of truth for todo, study notes, daily logs
- **ai-meeting-agent workspace** (external `bmw-ece-ntust/ai-meeting-agent`, branch `main`): Implementation, plan files, code changes

**Key files in this repo:**
- Todo: `docs/daily-logs/08_MeetingAgent.md`
- Study Notes: `docs/study-notes/14_Simple_Whisper_Transcription_Service.md`
- Daily Logs: `Daily-Logs.md` (aggregated from #812 comments)

## Active Plans

Per-phase action files live in the ai-meeting-agent repo at
`.opencode/plans/`. **One file per phase**; each describes the work for that
phase only, and is deleted from that directory when the phase is complete.

This repo (bmw-ntust-internship) does **not** maintain plan files. It tracks
progress via:
- Todo checkboxes in `docs/daily-logs/08_MeetingAgent.md`
- Issue #812 comment thread

At the start of every ai-meeting-agent session, read this repo's
`docs/daily-logs/08_MeetingAgent.md` for context, then switch to the
ai-meeting-agent workspace and read its `AGENTS.md` and `.opencode/plans/`.

## Daily-Logs Sync Rule (mandatory)

The todo in `docs/daily-logs/08_MeetingAgent.md` is the single source of
truth for ai-meeting-agent phase status. Whenever you complete work on
ai-meeting-agent, you **must** update this file to check off matching items
(`- [ ]` → `- [x]`).

Sync workflow:

1. **Code complete in ai-meeting-agent → todo update here**: when an item in
   an ai-meeting-agent phase plan file is done, edit
   `docs/daily-logs/08_MeetingAgent.md` to flip the corresponding `[ ]` to
   `[x]`. Do this in the same session, before posting the #812 comment.
2. **Phase complete → plan file removal**: when a whole phase is fully
   checked off in the todo, the ai-meeting-agent repo should delete that
   phase's plan file from `.opencode/plans/` so the directory only describes
   remaining phases.
3. **Manual edits to the todo**: any time the todo itself is edited (renamed,
   re-scoped, items added/removed), ensure the next #812 comment reflects
   the change.

read `docs/daily-logs/08_MeetingAgent.md` for current phase status, scan the
last 3 comments on issue #812 to see the most recent context, reconcile
any drift between them, then proceed.