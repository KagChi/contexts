Always call ltm-mcp_search_memories and search based on scope bmw-internship or by collection bmw-internship-rules and DO NOT CLEAN UP THIS CONTEXT.

## Task Completion Logging

After completing each task or work session, store a summary to LTM using:
- **Collection**: `bmw-internship-daily-logs`
- **Scope**: `bmw-internship`

Include: date, task description, outcomes, and any blockers or follow-ups.

**Important**: LTM storage and comment posting/updating are post-work activities. Do NOT include them as tasks in plan files.

## Daily Log Comments Workflow

**Mandatory**: After each work session, post or update a daily log comment following this workflow.

### Comment Format

```markdown
**Date**: YYYY-MM-DD

**Goals**:
- [Goal 1]
- [Goal 2]

**Daily Logs**:
- `HH:MM–HH:MM` — [Work description] (commits: [commit-range-link])
- `HH:MM–HH:MM` — [Work description] (commits: [commit-range-link])

**Links**:
- [Plan: Phase Name](link-to-plan-file)
```

### Content Guidelines

1. **Timestamps**: Use wall-clock time of last push activity in current session
2. **Time Ranges**: Derive from session start to last push time
3. **Commit Links**: Use GitHub/GitLab compare URLs with commit ranges
4. **One Bullet Per Stretch**: Group related commits into single work descriptions
5. **Plan Links**: Reference active plan files when applicable

### Time Range Derivation

Use wall-clock time when you pushed commits, not commit author timestamps.

Example:
- Started working: 14:30 (wall-clock)
- Last push: 16:45 (wall-clock)
- Time range: `14:30–16:45`

### Known Failure Modes

- **Using commit timestamps**: Use wall-clock push time, not git log timestamps
- **Missing commits**: Not linking to actual commit ranges in repo
- **Fragmented bullets**: Creating separate bullets for closely-related commits

### Sync Workflow

1. **Check Existing**: Read current comment thread first
2. **PATCH vs New**: 
   - If comment exists for today's date → PATCH it
   - If no comment for today → POST new comment with initial time range `09:12–09:15`
3. **Plan Links**: Always include links to active plan files
4. **Daily Posting**: Post once per day, update throughout the day via PATCH
5. **Edit in Place**: When correcting mistakes, use single PATCH to fix
6. **Ask Before Documenting**: Confirm with user before posting/updating comments
