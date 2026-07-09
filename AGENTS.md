# Agent Guidelines

Instructions for AI agents working on this or any codebase.

## Tool Execution Constraints

- **CRITICAL:** Before executing any terminal command, running a shell script, or mutating files, you MUST explicitly run `pwd` (or the environment equivalent) to verify your current working directory.
- Never assume you are in the project root or the directory of the previously executed command.
- Print the output of the current working directory to the console buffer to ensure context alignment before any subsequent execution.

## Pre-commit Verification

Before committing any changes, verify and run all applicable pre-commit checks:

### Detection Process
1. Identify the project type by checking for configuration files
2. Run the appropriate checks based on what you find
3. Fix any issues before proceeding with the commit

### Language-Specific Checks

**Rust Projects** (if `Cargo.toml` exists):
- Run `cargo fmt` to format code
- Run `cargo clippy` for linting
- Run `cargo build` to verify compilation
- Run `cargo test` if tests exist

**TypeScript/JavaScript Projects** (if `package.json` exists):
- Check for and run configured linters (eslint, prettier)
- Run `npm run lint` or `yarn lint` if available
- Run `tsc --noEmit` for TypeScript type checking

**Python Projects** (if `pyproject.toml`, `setup.py`, or `requirements.txt` exists):
- Run formatting tools (black, ruff, autopep8)
- Run type checking with mypy if configured
- Run linters (pylint, flake8) if configured

**Go Projects** (if `go.mod` exists):
- Run `go fmt ./...`
- Run `go vet ./...`
- Run `golangci-lint run` if configured

### General Rules
- Always check for `.pre-commit-config.yaml` and honor its configuration
- Report any check failures clearly
- Do not commit until all checks pass
- If checks fail, fix the issues and re-run verification

## Git Commit Policy

**CRITICAL:** Never create commits without explicit user permission.

### When to Commit

Only create commits when the user explicitly requests it with clear instructions such as:
- "commit this"
- "commit these changes"
- "make a commit"
- "git commit"

**Important:** Permission is per-commit only. Even if the user gave permission for a previous commit, you must ask again for the next one.

### When NOT to Commit

Do not commit when:
- User says "continue", "go ahead", "proceed", "do it", "let's go" - these refer to the current operation (editing, building, testing), NOT committing
- User approves a plan or implementation approach
- You finish implementing changes
- Pre-commit checks pass

### Correct Workflow

1. Make the changes (edit files, refactor code, etc.)
2. Show the user what changed (with `git diff` if appropriate)
3. **Stop and wait for explicit commit instruction**
4. Only after user says "commit", then run `git commit`

### Examples

**Wrong:**
```
User: "let's go"
Agent: [edits file] [commits without asking]
```

**Correct:**
```
User: "let's go"  
Agent: [edits file] [shows diff] "Changes ready. Would you like me to commit?"
User: "commit this"
Agent: [commits]
```

## Commit Message Format

Always use Conventional Commits format for all commit messages.

### Format Structure
```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

### Types
- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that don't affect code meaning (whitespace, formatting, etc.)
- `refactor`: Code change that neither fixes a bug nor adds a feature
- `perf`: Performance improvement
- `test`: Adding or correcting tests
- `build`: Changes to build system or dependencies
- `ci`: Changes to CI configuration files and scripts
- `chore`: Other changes that don't modify src or test files
- `revert`: Reverts a previous commit

### Scope
- Optional but recommended
- Represents the section of the codebase affected (e.g., `parser`, `api`, `auth`)

### Description
- Use imperative, present tense: "change" not "changed" nor "changes"
- Don't capitalize first letter
- No period (.) at the end

### Examples
```
feat(auth): add OAuth2 login support
fix(parser): handle null values in JSON input
docs: update installation instructions
refactor(api): simplify error handling logic
test(utils): add unit tests for string helpers
```

### Breaking Changes
For breaking changes, add `!` after the type/scope and include `BREAKING CHANGE:` in the footer:
```
feat(api)!: remove deprecated v1 endpoints

BREAKING CHANGE: v1 API endpoints have been removed. Migrate to v2.
```

## LTM (Long-Term Memory) Usage

Agents should leverage LTM to maintain continuity across sessions and avoid redundant discovery work.

### Session Initialization

At the start of **every session**, proactively load context to establish situational awareness:

**1. Discover Active Work**
```bash
ls .opencode/plans/
```
- If plan files exist, read them to understand active work
- Plan files present = work not yet done (directory listing is source of truth)

**2. Load Project Context from LTM**

Search LTM with progressive specificity:

```bash
# Repository-specific context
ltm-mcp_search_memories query="<repo-name>" scope="<repo-name>"

# Project type and language patterns
ltm-mcp_search_memories query="<language> <framework> workflows"

# Common workflows and tooling
ltm-mcp_search_memories query="git workflow pre-commit"
```

**Example queries for this project:**
- `query="contexts" scope="contexts"` - Repository-specific patterns
- `query="typescript npm workflows"` - Language-specific patterns
- `query="opencode agent guidelines"` - Tool-specific patterns

**3. Detect Project Type**

Identify project configuration to determine available workflows:
- Check for `package.json`, `Cargo.toml`, `pyproject.toml`, `go.mod`, etc.
- Determine build tools, test runners, linters from config files
- Load corresponding workflow memories from LTM

**Why this matters:**
- Avoids redundant discovery across sessions
- Surfaces relevant patterns and decisions immediately
- Establishes context before user states their intent
- Reduces "cold start" exploration time

**Context budget consideration:**
- Session initialization should consume <5% of context budget
- Load only actionable context (patterns, decisions, active work)
- Detailed implementation can be queried later on-demand

### Search Before Acting

Always search LTM before starting work on a task:
- Query for relevant patterns, decisions, or solutions before implementation
- Use multiple keyword searches to cover different aspects of the task
- Review existing memories to avoid repeating past mistakes or reinventing solutions

### Store After Completion

Save useful findings to LTM after completing significant work:
- **What to store:**
  - Project-specific patterns (e.g., "uses Redux Toolkit for state management")
  - Architectural decisions (e.g., "API routes follow /api/v1/resource pattern")
  - General workflows (e.g., "pre-commit hooks require cargo fmt + clippy")
  - Debugging solutions (e.g., "node EADDRINUSE fixed by killing process on port")
  - Configuration patterns (e.g., "CI/CD uses GitHub Actions with caching strategy")

### Scope Strategy

Use appropriate scope for memory organization:
- **`scope: "<repo-name>"`** - For repository-specific knowledge
  - Project architecture and patterns
  - Codebase-specific conventions
  - Local development setup details
  - Repository-specific tooling configuration
  
- **`scope: "global"`** - For universal guidelines and patterns
  - General development workflows
  - Language-agnostic best practices
  - Tool usage patterns (git, cargo, npm, gh CLI)
  - Debugging techniques applicable across projects

### Search Strategy

Effective LTM queries:
- Use multiple relevant keywords (e.g., "rust cargo fmt clippy pre-commit")
- Search before major implementation decisions
- Query when encountering unfamiliar patterns in a codebase
- Review memories when debugging recurring issues

### Memory Organization

- Use **collections** to group related memories (e.g., "rust-workflows", "ci-cd-patterns")
- Add **tags** for cross-cutting concerns (e.g., ["debugging", "performance"], ["authentication", "security"])
- Keep memory content concise but complete - enough detail to be actionable

### Session Summarization

At the end of **every completed session**, summarize key outcomes and learnings to LTM for future reference.

**When to Summarize:**
- Session work is complete (features implemented, bugs fixed, research concluded)
- Significant decisions were made or patterns discovered
- New workflows or debugging solutions were established
- Before closing a long-running or complex session

**What to Capture:**

Store separate memories for different aspects of the session:

1. **Technical Outcomes**
   - Features implemented or bugs fixed
   - Code patterns or architectural decisions
   - Configuration changes or tooling updates
   - Test coverage additions

2. **Process Learnings**
   - Workflow optimizations discovered
   - Build/test/deploy process insights
   - Debugging approaches that worked
   - Tool usage patterns (gh CLI, cargo, npm, etc.)

3. **Project Context Updates**
   - New dependencies or libraries added
   - API contracts or interface changes
   - Documentation updates
   - Codebase structure changes

**Summary Format:**

Each memory should be concise but complete:
```
Content: "Implemented OAuth2 authentication using passport.js. Flow: user clicks login → redirects to provider → callback validates token → creates session. Config in config/auth.ts. Environment variables: GOOGLE_CLIENT_ID, GITHUB_CLIENT_ID, SESSION_SECRET. Tests in tests/auth/oauth.test.ts cover success and error cases."

Tags: ["authentication", "oauth", "passport", "configuration"]
Collection: "auth-system"
Scope: "<repo-name>"
```

**Scope Guidelines:**
- Repository-specific implementations → `scope: "<repo-name>"`
- Reusable patterns or general workflows → `scope: "global"`

**Efficiency Tips:**
- Store 2-5 focused memories per session (avoid single monolithic summary)
- Each memory addresses one topic: a feature, a pattern, a workflow, a decision
- Use descriptive queries as memory content (makes future searches more effective)
- Tag generously to enable discovery from multiple angles

**Example Session Summary:**

After implementing authentication system:
```
# Memory 1: Feature implementation
ltm-mcp_store_memory(
  content="OAuth2 authentication implemented with passport.js. Supports Google and GitHub providers. Configuration in config/auth.ts requires GOOGLE_CLIENT_ID, GITHUB_CLIENT_ID, SESSION_SECRET env vars. User flow: login button → provider redirect → callback validation → session creation. Tests cover success, failure, and token validation scenarios.",
  tags=["authentication", "oauth", "passport", "implementation"],
  collection="auth-system",
  scope="contexts"
)

# Memory 2: Debugging solution
ltm-mcp_store_memory(
  content="OAuth redirect URI mismatch error resolved by ensuring callback URL in provider dashboard exactly matches app URL including port. Local dev uses http://localhost:3000/auth/callback. Production uses https://domain.com/auth/callback. Both must be registered.",
  tags=["debugging", "oauth", "configuration"],
  collection="troubleshooting",
  scope="global"
)

# Memory 3: Testing pattern
ltm-mcp_store_memory(
  content="OAuth flow testing uses mock provider responses. Test setup in tests/helpers/auth-mocks.ts creates fake tokens and user profiles. Allows testing authentication logic without external API calls. Pattern: mock provider → test flow → verify session state.",
  tags=["testing", "oauth", "mocking"],
  collection="test-patterns",
  scope="contexts"
)
```

**Integration with Compress:**
- Session summarization to LTM is separate from context compression
- Compress manages conversation context window during active sessions
- LTM stores distilled knowledge for cross-session continuity
- Both serve context management but at different timescales

## Plan Files

Agents should use plan files in `.opencode/plans/` to document multi-step work and maintain continuity across sessions.

### When to Create Plan Files

Create a plan file **only when the user explicitly requests planning**:
- User says "create a plan", "let's plan this out", "make a plan first"
- User asks to "break down" or "outline" a complex task before implementation
- User requests a phased approach or multi-step strategy

**Do not** create plan files automatically based on task complexity alone.

### Session Start Discovery

At the start of **every session**, check for active plan files:

```bash
ls .opencode/plans/
```

If plan files exist:
1. Read each plan file to understand active work
2. Reference plan context when making decisions
3. The directory listing is the source of truth — files present = work not yet done

### Plan File Format

**Naming Convention:**
```
.opencode/plans/plan-<task-slug>.md
```

Examples:
- `plan-add-oauth-support.md`
- `plan-refactor-api-layer.md`
- `plan-fix-authentication-bug.md`

**Structure (Phase-style):**

```markdown
# Plan: Add OAuth Support

## Objectives
- Implement OAuth2 authentication flow
- Support multiple providers (Google, GitHub)
- Add comprehensive tests and documentation

## Tasks
- Research OAuth2 flow patterns and best practices
- Implement OAuth2 provider interface
- Add tests for OAuth flows
- Update authentication documentation
- Configure redirect URLs for each provider

## Implementation Notes
- Using passport.js for OAuth integration
- Need environment variables for client IDs and secrets
- Redirect URLs must be registered with each provider

## Blockers / Decisions
- Which OAuth providers to support initially? (Google + GitHub confirmed)
- Session storage strategy? (Redis vs in-memory for development)
```

### Plan File Lifecycle

**Creation:**
1. User explicitly requests planning
2. Agent creates `.opencode/plans/plan-<task-slug>.md`
3. Agent populates plan with objectives, tasks, notes, and open questions
4. Agent uses TodoWrite tool separately for task tracking (plan file is reference documentation only)

**Maintenance:**
- Keep plan files **separate** from TodoWrite todos
- Plan file = reference documentation and strategic context
- TodoWrite = operational task tracking
- Update plan file's "Implementation Notes" and "Blockers / Decisions" sections as work progresses

**Deletion:**
When the planned work is complete:
1. Verify all objectives accomplished
2. Delete the plan file from `.opencode/plans/`
3. File deletion signals phase completion

### Plan Files vs TodoWrite

**Plan Files:**
- Strategic documentation
- Phase objectives and context
- Implementation notes and decisions
- Blockers and open questions
- Lives in `.opencode/plans/`
- Created only when user requests planning

**TodoWrite:**
- Operational task tracking
- Real-time status updates (pending, in_progress, completed)
- Managed by OpenCode's todo system
- Automatically tracked across sessions

Both systems complement each other but serve different purposes.

## Parallel Agent Workflow

When working on complex tasks with multiple independent components, split the work across parallel agents, each working in isolated git worktrees and creating separate PRs.

**Plan File Integration:**
- Check for active plan files in `.opencode/plans/` before decomposing tasks
- If plan files exist, follow the objectives and task breakdown defined in them
- Reference plan file context when spawning parallel agents
- Update plan file's "Implementation Notes" section as parallel work progresses

### Task Decomposition

Split work into parallel agents when:
- Multiple independent features can be developed simultaneously
- Bug fixes can be isolated from feature work
- Refactoring can happen in parallel with new development
- Each sub-task is independently testable and mergeable

Criteria for parallelizable tasks:
- Minimal or no code overlap between tasks
- Clear interface boundaries
- Independent test coverage
- Can be reviewed and merged separately

### Worktree Management

Create a new git worktree for each parallel agent to ensure complete isolation:

```bash
# Create a new worktree with a feature branch
git worktree add ../worktree-<task-name> -b feature/<scope>

# Example
git worktree add ../worktree-auth-oauth -b feature/auth-oauth
git worktree add ../worktree-api-pagination -b feature/api-pagination
```

**Naming Conventions:**
- Worktree directory: `worktree-<feature-slug>` or `worktree-<fix-slug>`
- Branch names: Follow Conventional Commits types (`feature/<scope>`, `fix/<scope>`, `refactor/<scope>`)

**Critical: Directory Verification**
Before any operations in a worktree, ALWAYS verify your location:
```bash
pwd  # Confirm you're in the correct worktree
```

**Cleanup After Merge:**
```bash
# Remove worktree after PR is merged
git worktree remove ../worktree-<task-name>

# If worktree has uncommitted changes, use --force
git worktree remove --force ../worktree-<task-name>
```

### Agent Spawning Strategy

Use the Task tool to spawn multiple agents in parallel. Each agent should receive:
- **Worktree path:** Absolute path to its isolated worktree directory
- **Task scope:** Specific, well-defined work to complete
- **Expected deliverables:** What should be implemented, tested, and ready for PR
- **Interface requirements:** APIs, types, or contracts to maintain

**Coordination Pattern:**
1. Main agent decomposes the task
2. Main agent creates worktrees and branches
3. Main agent spawns sub-agents with Task tool (in parallel when possible)
4. Sub-agents work independently in their worktrees
5. Sub-agents report completion with PR links
6. Main agent tracks progress and handles any cross-cutting concerns

### GitHub Operations

**CRITICAL:** Always use `gh` CLI for all GitHub interactions. Never assume or guess GitHub API behavior.

**Before Starting:**
```bash
# Verify authentication
gh auth status
```

**Common Operations:**
```bash
# Create a PR from current branch
gh pr create --title "feat(auth): add OAuth2 support" --body "Implements OAuth2 login flow..."

# Create draft PR for work-in-progress
gh pr create --draft --title "feat(api): add pagination" --body "WIP: pagination implementation"

# Check PR status
gh pr status

# List all PRs
gh pr list

# View specific PR
gh pr view 123

# Check CI/checks status
gh pr checks 123

# List CI runs
gh run list

# Merge PR after approval and passing checks
gh pr merge 123 --squash

# Query related issues
gh issue list --label "enhancement"
```

**PR Linking:**
Reference related PRs or issues using `#<number>` in PR descriptions:
```
Depends on #456
Relates to #789
Fixes #321
```

### PR Workflow Per Worktree

Each worktree produces one focused, independently reviewable PR:

1. **Verify pre-commit checks pass** (formatting, linting, tests)
2. **Check authentication:** `gh auth status`
3. **Create PR** with descriptive title and body:
   ```bash
   gh pr create --title "feat(scope): description" --body "Detailed explanation..."
   ```
4. **Use draft PRs** for work-in-progress or when feedback is needed early
5. **Verify CI status** before requesting review:
   ```bash
   gh pr checks
   ```
6. **Link related PRs** if there are dependencies or related work
7. **After merge:** Clean up the worktree immediately
   ```bash
   git worktree remove ../worktree-<task-name>
   ```

### Context Management Strategy

Monitor context usage throughout task execution. When approaching context limits, split work into manageable chunks to maintain agent effectiveness.

**Monitoring Threshold:**
- Trigger chunking strategy when context usage exceeds **75%** (~150k/200k tokens)
- Proactively compress completed work before hitting limits

**When Approaching Context Limits:**

1. **Compress completed work immediately**
   - Archive finished implementations
   - Keep only active work uncompressed
   
2. **Split remaining work into logical chunks**
   - One file or module per chunk
   - One feature component per chunk
   - One test suite per chunk
   
3. **Spawn fresh agents for each chunk**
   - Pass only essential context (file paths, interfaces, type definitions)
   - Avoid duplicating already-compressed information
   - Each agent works with a clean context window

**Chunking Strategy for Large File Writes:**

When writing large files (>150 lines):
- Split into logical sections (imports, types, functions, exports)
- Write and verify each section incrementally
- Use append operations when possible instead of full rewrites
- Compress each completed section before moving to the next

**Progressive Implementation Cycle:**
```
Implement → Verify → Compress → Next Chunk
```

Keep this cycle tight:
- Implement one logical unit
- Run relevant tests/checks to verify
- Compress the completed work immediately
- Move to next chunk with fresh context

**Active Work Policy:**
- Keep currently active work uncompressed for easy reference
- Archive all completed implementations aggressively
- Avoid holding completed work "just in case"

### Example Workflow

**Scenario:** User requests implementing authentication system with OAuth2, API key support, and permission middleware.

**Step 1: Task Decomposition**
Main agent identifies three parallel tasks:
- Task A: OAuth2 login flow
- Task B: API key authentication
- Task C: Permission middleware

**Step 2: Create Worktrees**
```bash
pwd  # Confirm we're in project root
git worktree add ../worktree-auth-oauth -b feature/auth-oauth
git worktree add ../worktree-auth-apikey -b feature/auth-apikey
git worktree add ../worktree-auth-middleware -b feature/auth-middleware
```

**Step 3: Spawn Parallel Agents**
Main agent uses Task tool to spawn three agents in parallel:

```
Agent A: Work in /path/to/worktree-auth-oauth
- Implement OAuth2 flow
- Add tests for OAuth2 handlers
- Update documentation

Agent B: Work in /path/to/worktree-auth-apikey
- Implement API key validation
- Add tests for key generation and validation
- Document API key usage

Agent C: Work in /path/to/worktree-auth-middleware
- Implement permission checking middleware
- Add tests for various permission scenarios
- Document middleware usage
```

**Step 4: Each Agent Works Independently**
Each agent in its worktree:
1. Verifies location with `pwd`
2. Implements assigned feature
3. Runs pre-commit checks (format, lint, test)
4. Creates PR with `gh pr create`

**Step 5: Context Management Throughout**
If any agent approaches context limits during implementation:
- Compress completed portions immediately
- Split remaining work into smaller chunks
- Spawn fresh sub-agent if needed with minimal context

**Step 6: PR Creation**
Each agent creates its PR:
```bash
# Agent A in worktree-auth-oauth
gh pr create --title "feat(auth): add OAuth2 login support" \
  --body "Implements OAuth2 authentication flow with Google and GitHub providers. Relates to #123"

# Agent B in worktree-auth-apikey  
gh pr create --title "feat(auth): add API key authentication" \
  --body "Implements API key generation, validation, and rotation. Relates to #123"

# Agent C in worktree-auth-middleware
gh pr create --title "feat(auth): add permission middleware" \
  --body "Implements role-based permission checking middleware. Depends on #456 #457"
```

**Step 7: Cleanup After Merge**
Once each PR is reviewed and merged:
```bash
git worktree remove ../worktree-auth-oauth
git worktree remove ../worktree-auth-apikey
git worktree remove ../worktree-auth-middleware
```

**Result:** Three focused, independently reviewable PRs, each with clean git history, passing tests, and clear documentation. Work completed in parallel with maximum efficiency.
