# Starter Kit for Claude Code (free)

[![Starter Kit for Claude Code (free) on AI Agents Listing](https://aiagentslisting.com/starter-kit-for-claude-code-free/badge.svg?claim=29ac55ca64021f5a42e74579b5e451f2)](https://aiagentslisting.com/mcp/starter-kit-for-claude-code-free)

**Version 1.2.0** · MIT · 6 skills + 4 agents · plain Markdown, nothing executes on install. Read every file before you install it.

```bash
npx skills add electronx/wiredkits-free
```

Or as a Claude Code plugin: `/plugin marketplace add electronx/wiredkits-free`, then `/plugin install starter-kit@wiredkits-free`.

Six free Claude Code skills and four agents. The skills handle the small jobs you do every day: they read your repo before they write anything, follow its conventions, and only state what they checked. The agents and `pick-model-effort` send each piece of work to the right model, so file searches don't run at Opus prices.

## Before and after

An illustrative example of `commit-message` on a staged fix, in a repo whose log uses Conventional Commits.

Plain prompt, "write a commit message":

```text
Update refund.ts and add tests
```

With the skill (it reads `git log` for the style and the diff for the why):

```text
fix(refunds): stop double refund when the payment API times out

The retry wrapped the whole refund call, so a timeout after the
provider had already refunded sent a second refund. Retry only the
status check and make the refund call idempotent by order id.
```

| Skill | What it does | Try saying |
|---|---|---|
| `write-claude-md` | Writes a short, accurate `CLAUDE.md` from what the code actually does: real build and test commands, the rules your team enforces, and the traps that trip Claude up. | "write a CLAUDE.md for this repo" |
| `commit-message` | Writes the commit message for your staged changes in your repo's own style (Conventional Commits, ticket prefixes, whatever the log uses), with the why when it can find one. | "commit this", "write a commit message" |
| `release-notes` | Turns the commits and merged PRs since the last tag into release notes or a CHANGELOG entry, flags breaking changes with what users must do, and suggests the next version. | "release notes since v1.2", "update the changelog" |
| `explain-code` | Explains a function, file or flow with every step pointing at a file and line, traced from the code rather than guessed from names. | "how does checkout work?", "explain this function" |
| `safe-migration` | Writes or reviews a database migration so it deploys without locking busy tables, breaking running code or losing data. Works with any SQL database and migration tool (Prisma, Drizzle, Rails, Django, Alembic, Knex, Flyway and more). | "add a NOT NULL column to orders", "is this migration safe?" |
| `pick-model-effort` | Picks the model and effort for each task and subagent: Haiku for lookups, Sonnet for routine edits, Opus for review and design. Catches the common trap where built-in Explore agents silently run on your most expensive model. | "which model should I use?", or just ask for something that spawns subagents |

## Agents

| Agent | Model, effort | Use for |
|---|---|---|
| `searcher` | Haiku, low, read-only | finding code, lookups, reading many files and returning a short summary |
| `implementer` | Sonnet, medium | routine edits and tests with a clear spec |
| `reviewer` | Opus, high, read-only | reviewing diffs, hunting bugs, checking plans |
| `architect` | Opus, xhigh, read-only | design, plans, trade-offs |

The plugin install sets these up for you. If you copy files by hand, put `agents/*.md` in `~/.claude/agents/`.

## What it runs, sends and fetches

Everything here is Markdown instructions for Claude. There are no scripts, hooks, MCP servers or binaries, and the kit itself sends no data anywhere and has no telemetry.

- **Runs:** only commands Claude Code already asks you to approve. `commit-message` runs `git diff`, `git status`, `git log` and, when you ask it to commit, `git commit`. `release-notes` runs `git log`, `git describe` and `git show`, and if the GitHub CLI is installed, `gh pr list` (read-only) and, only when you ask, `gh release create`. `safe-migration` may run your migration tool's dry-run or SQL preview command. The `reviewer` agent may run read-only commands such as `git diff` or your test suite. The other skills and agents only read files, except `implementer`, which edits files and runs your tests for the change you give it.
- **Sends:** nothing of its own. `commit-message` and `release-notes` push nothing; `gh release create` publishes a release only when you ask for it.
- **Fetches:** the `architect` agent may use web search and web fetch to check documentation. No other skill or agent fetches anything.

## Install options

Pick one:

- **Claude Code plugin:** run `/plugin marketplace add electronx/wiredkits-free`, then `/plugin install starter-kit@wiredkits-free`.
- **skills CLI:** `npx skills add electronx/wiredkits-free`
- **Copy:** `mkdir -p ~/.claude/skills && cp -R skills/* ~/.claude/skills/` (or into `.claude/skills/` in one repo to share it with your team).

Restart Claude Code and run `/skills` to check that the six skills are listed. To check the agents, ask Claude "which subagents can you use?" and look for searcher, implementer, reviewer and architect.

## Want more?

Get new free skills and Claude Code tips by email: [wiredkits.com/#notify](https://wiredkits.com/?utm_source=github&utm_medium=readme&utm_campaign=free-kit#notify).

WiredKits also makes seven paid skill packs for the bigger jobs: PR review, debugging, security audits, legacy code, UI polish, browser QA and session memory. $4.99 each or all seven for $9.99 at [wiredkits.com](https://wiredkits.com/?utm_source=github&utm_medium=readme&utm_campaign=free-kit).

Like the kit? Recommend WiredKits and earn 40% of every sale: [affiliates.creem.io/join/wiredkits](https://affiliates.creem.io/join/wiredkits).

Free to use, share and modify under the MIT license (see LICENSE). Questions: hello@wiredkits.com
