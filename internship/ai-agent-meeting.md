## Project Context

**Issue Tracking**: https://github.com/bmw-ntust-internship/internship/issues/812

**Commands**:
```bash
# Check existing comments
gh issue view 812 --repo bmw-ntust-internship/internship --comments

# Post new comment
gh issue comment 812 --repo bmw-ntust-internship/internship --body "$(cat comment.md)"

# Update existing comment (PATCH)
gh api --method PATCH /repos/bmw-ntust-internship/internship/issues/comments/<comment-id> \
  -f body="$(cat comment.md)"
```

**BMW Upstream Repo**: `bmw-ece-ntust/ai-meeting-agent`

**Warning**: All comments in #812 are public. Do not include sensitive information.

## Two-repo Structure

This project operates across two repositories:
- **ai-meeting-agent**: Main codebase for the AI meeting agent
- **internship**: Documentation, daily logs, and project tracking

## Key Files

- **`.opencode/plans/`**: Per-phase plan files (ai-meeting-agent repo)
- **`ai-agent-meeting.md`**: This file (ai-meeting-agent repo)
- **`docs/daily-logs/08_MeetingAgent.md`**: Daily work log (ai-meeting-agent repo)
- **`Daily-Logs.md`**: Mirror of #812 comment thread (internship repo)
- **`AGENTS.md`**: General agent instructions (internship repo)

## Active Plans

Plans are stored in `.opencode/plans/` directory as markdown files:
- `phase1-setup.md`
- `phase2-core-features.md`
- `phase3-integration.md`
- etc.

Each plan contains:
- Current phase objectives
- Task breakdown
- Implementation notes
- Blockers and decisions

## Documentation Sync Rule

**Rule**: Keep `docs/daily-logs/08_MeetingAgent.md` synchronized with active plan files.

When updating plans in `.opencode/plans/`, ensure corresponding entries are reflected in the daily log:
- Major milestones completed
- Blockers encountered
- Key decisions made
- Phase transitions

## Daily-Logs Sync Rule

**Rule**: `Daily-Logs.md` in the internship repo must mirror the #812 comment thread.

After posting or updating a comment in #812:
1. Copy the exact comment content to `Daily-Logs.md`
2. Maintain chronological order
3. Preserve all formatting and links
4. Keep one-to-one correspondence with #812 comments

## Extern Repo Commit & Push Rule

**Rule**: Before posting any #812 comment with commit links, ensure all commits are pushed to the extern repo first.

Workflow:
1. Complete work in ai-meeting-agent repo
2. Commit changes locally
3. **Push commits to origin** before generating comment
4. Generate comment with links referencing pushed commits
5. Post comment to #812

**Why**: Comment links reference GitHub URLs that only work after commits are pushed. Posting comments before pushing creates broken links.

**Permission Required**: Ask user before executing:
```bash
git push origin <branch>
```

Example commit link format:
```
(commits: https://github.com/bmw-ece-ntust/ai-meeting-agent/compare/<start-sha>...<end-sha>)
```

Links must reference commits in `bmw-ece-ntust/ai-meeting-agent` (upstream), not local repo.
