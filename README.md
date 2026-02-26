# claude-toolkit

A Claude Code plugin providing agents, skills, commands, and hooks — refined and restructured from [everything-claude-code](https://github.com/affaan-m/everything-claude-code).

## Why This Fork Exists

[everything-claude-code](https://github.com/affaan-m/everything-claude-code) is a popular (50K+ stars) Claude Code configuration collection. However, after using it extensively, several issues became deal-breakers:

- **Recursive script calls in v1.2.0**: The `observe.sh` hook in the continual-learning-v2 module could enter infinite recursive invocations, spawning background `claude -p` processes that hang or loop. Multiple bug reports confirm this — environment variable syntax errors ([#227](https://github.com/affaan-m/everything-claude-code/issues/227), [#228](https://github.com/affaan-m/everything-claude-code/issues/228)), pipe-broken env propagation ([#232](https://github.com/affaan-m/everything-claude-code/issues/232)), and ~49% observation data loss from JSON parsing failures ([#205](https://github.com/affaan-m/everything-claude-code/issues/205)). The observer agent still hangs on Windows to this day ([#295](https://github.com/affaan-m/everything-claude-code/issues/295)).
- **Fragile hook system**: Hooks block legitimate operations — plan file creation ([#240](https://github.com/affaan-m/everything-claude-code/issues/240)), documentation in `docs/` directories ([#264](https://github.com/affaan-m/everything-claude-code/issues/264)), Windows path issues ([#275](https://github.com/affaan-m/everything-claude-code/issues/275)). Hooks cannot be individually disabled ([#248](https://github.com/affaan-m/everything-claude-code/issues/248)) and the npm package ships missing hook scripts ([#287](https://github.com/affaan-m/everything-claude-code/issues/287)).
- **Complexity without reliability**: Rapid feature additions (Codex CLI, OpenCode, Cursor, GitHub Marketplace) expanded scope at the cost of stability for the core Claude Code use case.

Rather than continue patching upstream, I rebuilt the toolkit from scratch with a focus on **reliability over breadth**.

## What's Different

| Aspect | everything-claude-code | claude-toolkit |
|--------|----------------------|----------------|
| Hook stability | Recursive observer, missing scripts | Lightweight JS hooks, no background agents |
| Hook control | Cannot disable individually | Each hook is independent and removable |
| Scope | 4 platforms, npm package, GitHub App | Claude Code only — does one thing well |
| Continual learning | v2: fragile observe.sh with data loss | Paired with [continual-learning](https://github.com/QLYYLQ/continual-learning) v3 |

## Features

### Agents (13)

Specialized sub-agents invoked via the Task tool for focused, autonomous work:

| Agent | Purpose |
|-------|---------|
| `planner` | Break down features into phased implementation plans with risk assessment |
| `architect` | System design, scalability analysis, Architecture Decision Records |
| `tdd-guide` | Enforce Red-Green-Refactor cycle with 80%+ coverage |
| `code-reviewer` | Review uncommitted changes for security, quality, and performance |
| `security-reviewer` | OWASP Top 10 scan, secret detection, `npm audit` integration |
| `build-error-resolver` | Fix TypeScript/build errors with minimal surgical diffs |
| `e2e-runner` | Playwright E2E tests with screenshot/video/trace capture |
| `refactor-cleaner` | Dead code detection via `knip`/`depcheck`/`ts-prune`, safe removal |
| `doc-updater` | Generate codemaps and sync documentation from source |
| `database-reviewer` | PostgreSQL/Supabase query optimization, schema review, RLS audit |
| `go-build-resolver` | Fix `go build`, `go vet`, `staticcheck` errors incrementally |
| `go-reviewer` | Go code review — concurrency safety, error handling, idiomatic patterns |
| `python-reviewer` | Python code review — PEP 8, type hints, `ruff`/`mypy`/`black` integration |

### Commands (16)

Slash commands for common workflows:

| Command | Description |
|---------|-------------|
| `/plan` | Create implementation plan, wait for approval before coding |
| `/tdd` | Full TDD workflow: scaffold → test → implement → refactor → coverage |
| `/code-review` | Review uncommitted changes, block on critical issues |
| `/build-fix` | Parse build errors, fix one at a time, verify after each |
| `/go-build` | Incremental Go build error resolution |
| `/go-review` | Comprehensive Go code review with static analysis |
| `/go-test` | Go TDD with table-driven tests |
| `/python-review` | Python review with `ruff`, `mypy`, `black`, `bandit` |
| `/e2e` | Generate and run Playwright E2E tests |
| `/verify` | Full verification loop: build → type check → lint → test → audit |
| `/test-coverage` | Find files below 80% coverage, generate missing tests |
| `/refactor-clean` | Detect and safely remove dead code |
| `/orchestrate` | Multi-agent sequential workflows (feature, bugfix, refactor, security) |
| `/checkpoint` | Git checkpoint management with timestamps |
| `/eval` | Eval-driven development: define criteria, check pass/fail, report |
| `/setup-pm` | Configure package manager preference (npm/pnpm/yarn/bun) |

### Skills (25)

Context-aware knowledge modules automatically loaded when relevant:

**General**
- `coding-standards` — TypeScript/JS/React/Node.js conventions
- `security-review` — 10-category security checklist with patterns
- `tdd-workflow` — Red-Green-Refactor enforcement with coverage tracking
- `eval-harness` — Eval-driven development with pass@k metrics
- `strategic-compact` — Context window management with phase-aware compaction
- `iterative-retrieval` — Multi-round context retrieval for sub-agent workflows

**Frontend / Backend**
- `frontend-patterns` — React, Next.js, state management, performance
- `backend-patterns` — Node.js, Express, API design, database optimization

**Database**
- `postgres-patterns` — PostgreSQL optimization, indexing, Supabase best practices
- `clickhouse-io` — ClickHouse analytics patterns for high-performance workloads
- `jpa-patterns` — JPA/Hibernate entity design, transactions, pagination

**Python / Django**
- `python-patterns` — Pythonic idioms, PEP 8, type hints
- `python-testing` — pytest, TDD, fixtures, mocking, parametrization
- `django-patterns` — Django architecture, DRF, ORM, signals, middleware
- `django-security` — Authentication, CSRF, SQL injection prevention
- `django-tdd` — Django testing with `pytest-django` and `factory_boy`
- `django-verification` — Django deployment readiness checks

**Go**
- `golang-patterns` — Idiomatic Go, interfaces, concurrency, error handling
- `golang-testing` — Table-driven tests, benchmarks, fuzzing, coverage

**Java / Spring Boot**
- `java-coding-standards` — Java naming, immutability, Optional, streams
- `springboot-patterns` — REST API, layered architecture, caching, async
- `springboot-security` — Spring Security, validation, rate limiting
- `springboot-tdd` — JUnit 5, Mockito, MockMvc, Testcontainers
- `springboot-verification` — Build, static analysis, tests, security scans

### Hooks (11)

Lightweight, non-blocking hooks with no background agent processes:

**PreToolUse** — Dev server blocker (use tmux), tmux reminder for long-running commands, git push warning, doc file blocker, compact suggestion at 50-call intervals

**PostToolUse** — PR URL logger, async build analysis, Prettier auto-format, TypeScript type check, `console.log` warning

**Stop** — `console.log` audit across all modified files

## Continual Learning

This toolkit is designed to work with [continual-learning](https://github.com/QLYYLQ/continual-learning) — a separate plugin that records session data, intercepts risky bash commands via configurable instincts, and detects reusable patterns from actual usage. The combination enables a workflow that **evolves with use**: skills, rules, and instincts improve based on real coding sessions rather than static configuration.

This is the core design philosophy that distinguishes it from everything-claude-code's continual-learning-v2 — reliable recording without recursive processes, pattern extraction without data loss, and instinct-based interception without fragile observer agents.

## Installation

```bash
# Add marketplace
/plugin marketplace add QLYYLQ/claude-toolkit

# Install plugin
/plugin install claude-toolkit@claude-toolkit
```

For the continual learning system:

```bash
/plugin marketplace add QLYYLQ/continual-learning
/plugin install continual-learning@continual-learning
```

## License

MIT
