---
name: git-commit
description: Standards, specifications, and protocols for writing local git commits using Conventional Commits and Karma conventions, with optional read-only Official GitHub MCP (github-official) metadata verification and zero remote writes.
---

# Git Commit Skill

## 1. Overview & Purpose

This skill defines the mandatory standard for creating git commit messages in the project, adhering strictly to **Conventional Commits** and the **Karma Git Commit** convention as referenced in the project [README.md](../../../README.md).

Every commit must be structured, meaningful, and written in natural language without AI-internal meta-comments.

> [!IMPORTANT]
> **No Remote Operations (Pull / Push / MCP Writes) During Commit**:
> The `git-commit` skill is strictly scoped to local staging and committing changes. It **must NEVER execute `git pull`** (or `git pull --rebase`), **`git push`** (or `git push --force`), nor **any GitHub MCP write/mutation tool** (`create_or_update_file`, `push_files`, `delete_file`, `create_branch`, `create_pull_request`).
> Commits must remain atomic, deterministic, and isolated to local staged files. Remote synchronization and pushing belong exclusively to [push-request.md](./push-request.md) and [pull-request.md](./pull-request.md).

---

## 2. Commit Message Structure

Commit messages must strictly follow the canonical format:

```text
<type>(<scope>): <subject>

[optional body]

[optional footer(s)]
```

### 2.1 Allowed Types

| Type | Meaning | Changelog Mapping |
| :--- | :--- | :--- |
| **`feat`** | A new feature or capability | `[NEW]` |
| **`fix`** | A bug fix or patch | `[FIX]` |
| **`docs`** | Documentation changes only (e.g., MkDocs, README) | `[DOC]` |
| **`style`** | Code formatting, whitespace, semicolons (no code logic changes) | `[CHG]` |
| **`refactor`** | Code refactoring without changing observable behavior | `[REFACTOR]` |
| **`perf`** | Performance improvement or query optimization | `[PERF]` |
| **`test`** | Adding, refactoring, or updating automated tests | `[TEST]` |
| **`build`** | Build system, toolchain, or dependencies (pip, npm, docker) | `[BUILD]` |
| **`ci`** | CI/CD configurations, GitHub Actions, linters | `[BUILD]` |
| **`sec`** | Security patch, vulnerability resolution | `[SEC]` |
| **`chore`** | Maintenance, miscellaneous tasks not altering src or test | `[OTHER]` |
| **`revert`** | Reverting a previous commit | `[ROLL]` |

### 2.2 Scope
The scope provides context on the affected module or boundary:
- Common scopes: `(auth)`, `(api)`, `(users)`, `(database)`, `(worker)`, `(docs)`, `(frontend)`, `(backend)`, `(infra)`.
- Use lowercase, enclosed in parentheses immediately after the type.

### 2.3 Subject Line Rules
- Use imperative, present tense: `"add user validation"`, not `"added"` or `"adds"`.
- Do not capitalize the first letter of the subject: `feat(api): add pagination support`.
- Do not place a period (`.`) at the end of the subject line.
- Keep the subject line under **72 characters**.

### 2.4 Body & Footers
- Leave a blank line between the subject line and the body.
- Explain the **"why"** and **"what"** rather than the "how".
- Reference breaking changes explicitly with `BREAKING CHANGE: <explanation>`.
- Link GitHub board issues or cards in footers: `Card: #12` or `Fixes #34`.

---

## 3. Concrete Examples

### Example 1: New Feature
```text
feat(auth): implement refresh token rotation mechanism

Store cryptographically signed refresh tokens in Redis with 7-day TTL.
Automatically invalidate revoked family tokens upon reuse detection.

Card: #14
```

### Example 2: Bugfix
```text
fix(database): resolve connection pool starvation on retry timeout

Set explicit idle timeout to 30 seconds and increase pool max connections
to prevent hanging socket exhaustion during high concurrency bursts.

Fixes #22
```

### Example 3: Documentation & Diagrams
```text
docs(architecture): add sequence diagram for checkout endpoint

Document HTTP request-response flow, payment gateway interaction, and error
branches in Documentation/config/docs/api/endpoints/checkout.md.
```

---

## 4. Mandatory Changelog Synchronization (`changelog.md`)

Per [AI/Rules.md](../../Rules.md), **every commit must include or be preceded by an update to [changelog.md](../../../changelog.md)**. The agent must never perform a commit without editing the changelog.

### 4.1 Changelog Entry Pattern
Entries must be inserted at the top of the `### Changelog` section in [changelog.md](../../../changelog.md):

```markdown
YYYY-MM-DD - [TYPE] - [GENERAL-DESCRIPTION]

Detalhes:

- [DETAILED-DESCRIPTION] - [AUTHOR] - [DATE (YYYY-MM-DD)] - [CARD-OR-N/A] - [COMMIT-OR-PENDING]
```

### 4.2 Type Mapping Matrix

| Git Commit `<type>` | Changelog Tag | Description |
| :--- | :---: | :--- |
| `feat` | `[NEW]` | New feature or capability |
| `fix` | `[FIX]` | Bug fix |
| `docs` | `[DOC]` | Documentation and diagrams |
| `style` / `chore` | `[CHG]` / `[OTHER]` | Minor styling, config, or chores |
| `refactor` | `[REFACTOR]` | Code refactoring without behavior change |
| `perf` | `[PERF]` | Performance optimization |
| `test` | `[TEST]` | Test additions or modifications |
| `build` / `ci` | `[BUILD]` | Build system, CI pipelines, Docker, AI rules |
| `sec` | `[SEC]` | Security patch |
| `revert` | `[ROLL]` | Rollback or revert |

### 4.3 Integrated Execution Workflow

Whenever preparing a commit, follow this exact sequence (do **NOT** run `git pull`, `git push`, or remote GitHub MCP write tools):

1. **Step 1: Check Git Diff, Date & Optional Read-Only MCP Metadata**:
   Determine the current date in ISO 8601 `YYYY-MM-DD` format, verify files being committed, and (optionally, if a card number or author is needed and GitHub MCP is active) verify card/author metadata read-only per Section 5.
2. **Step 2: Edit `changelog.md`**:
   Insert the new entry under `### Changelog` following the template above.
3. **Step 3: Stage `changelog.md` Together with Code**:
   ```bash
   git add changelog.md <other-changed-files>
   ```
4. **Step 4: Execute Local Commit**:
   ```bash
   git commit -m "<type>(<scope>): <subject>" -m "Optional body..."
   ```

> [!CAUTION]
> Never prepend or append `git pull`, `git push`, or GitHub MCP file-mutation tools (`create_or_update_file`, `push_files`, `delete_file`) before or after `git commit`. Commits must be performed strictly against the local working branch state without implicit remote fetches, merges, API commits, or premature pushes. Remote sync and pushing are delegated exclusively to [push-request.md](./push-request.md) and [pull-request.md](./pull-request.md).

---

## 5. GitHub MCP Compatibility (Read-Only Metadata Verification)

`git-commit` performs **zero remote writes**; all file staging and committing happens strictly through local `git add` and `git commit`. When the **Official GitHub MCP Server (`github-official`)** inside `nievo_ai` Docker MCP Gateway is available, it may be used **read-only** to verify metadata before writing `changelog.md` and commit footers.

> [!NOTE]
> **Tool Name Versioning**: Official `github/github-mcp-server` tools may appear with consolidated names (`issue_read`) or classic aliases (`get_issue`), and MCP clients may prefix them by server/plugin name. Always match the registered tool name in the active runtime.

### 5.1 Read-Only MCP Capability Mapping

| Capability | Official GitHub MCP (`github-official`) | Local / `gh` Fallback | Purpose in `git-commit` |
| :--- | :--- | :--- | :--- |
| **Verify card / issue before footer & changelog** | `issue_read` / `get_issue` | `gh issue view <card>` or user-supplied number | Validate issue number and title before writing `Card: #<n>` / `Fixes #<n>` and `[CARD-OR-N/A]` in `changelog.md`. |
| **Resolve `[AUTHOR]` when local git config is unset** | `get_me` | `git config user.name` (preferred) | Fallback author identifier for `changelog.md` if `git config user.name` is empty. |

### 5.2 Strictly Forbidden MCP Anti-Patterns & Offline Graceful Fallback
- **NEVER use `create_or_update_file`, `push_files`, `delete_file`, or `create_branch` to commit changes via the GitHub API**: Doing so bypasses local `changelog.md` synchronization, local test gates, `.gitignore` safety, and atomic branch history.
- **Never block local commits when offline or unauthenticated**: If GitHub MCP and `gh` are unavailable or unauthenticated, proceed using local `git config user.name` and the card number already confirmed (or `N/A`).
- **Never read `.env` or `mcp_secrets.env`**: Never inspect secret files or stage them (`git status --short` must confirm they remain untracked/ignored).

---

## 6. Pre-Commit Protocol & Checklist

Before executing `git commit`, the agent must complete this verification:

- [ ] Current working tree inspected (`git status --short`, `git diff`).
- [ ] No `git pull`, `git push`, or remote GitHub MCP write operations (`create_or_update_file`, `push_files`, `delete_file`, `create_branch`) executed.
- [ ] Optional read-only card/author verification (`issue_read` / `get_issue`, `get_me`) performed without blocking offline local commits.
- [ ] [changelog.md](../../../changelog.md) edited with the new entry matching the commit changes.
- [ ] [changelog.md](../../../changelog.md) staged alongside the modified files (`git add changelog.md ...`).
- [ ] Documentation and diagrams updated if code contracts, endpoints, or rules changed.
- [ ] Unit and integration tests pass cleanly.
- [ ] Commit message conforms strictly to `<type>(<scope>): <subject>` syntax.
- [ ] No `.env`, `mcp_secrets.env`, cache, or temporary files read or staged.
