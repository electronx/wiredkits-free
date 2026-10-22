# Starter Kit for Claude Code (free)

Five free Claude Code skills and four agents. The skills handle the small jobs you do every day: they read your repo before they write anything, follow its conventions, and only state what they checked. The agents and `pick-model-effort` send each piece of work to the right model, so file searches don't run at Opus prices.

| Skill | What it does | Try saying |
|---|---|---|
| `write-claude-md` | Writes a short, accurate `CLAUDE.md` from what the code actually does: real build and test commands, the rules your team enforces, and the traps that trip Claude up. | "write a CLAUDE.md for this repo" |
| `commit-message` | Writes the commit message for your staged changes in your repo's own style (Conventional Commits, ticket prefixes, whatever the log uses), with the why when it can find one. | "commit this", "write a commit message" |
| `release-notes` | Turns the commits and merged PRs since the last tag into release notes or a CHANGELOG entry, flags breaking changes with what users must do, and suggests the next version. | "release notes since v1.2", "update the changelog" |
| `explain-code` | Explains a function, file or flow with every step pointing at a file and line, traced from the code rather than guessed from names. | "how does checkout work?", "explain this function" |
| `pick-model-effort` | Picks the model and effort for each task and subagent: Haiku for lookups, Sonnet for routine edits, Opus for review and design. Catches the common trap where built-in Explore agents silently run on your most expensive model. | "which model should I use?", or just ask for something that spawns subagents |

## Agents

| Agent | Model, effort | Use for |
|---|---|---|
| `searcher` | Haiku, low, read-only | finding code, lookups, reading many files and returning a short summary |
| `implementer` | Sonnet, medium | routine edits and tests with a clear spec |
| `reviewer` | Opus, high, read-only | reviewing diffs, hunting bugs, checking plans |
| `architect` | Opus, xhigh, read-only | design, plans, trade-offs |

The plugin install sets these up for you. If you copy files by hand, put `agents/*.md` in `~/.claude/agents/`.

## Install (1 minute)

Pick one:

- **Claude Code plugin:** run `/plugin marketplace add electronx/wiredkits-free`, then `/plugin install starter-kit@wiredkits-free`.
- **skills CLI:** `npx skills add electronx/wiredkits-free`
- **Copy:** `mkdir -p ~/.claude/skills && cp -R skills/* ~/.claude/skills/` (or into `.claude/skills/` in one repo to share it with your team).

Restart Claude Code and run `/skills` to check that the five skills are listed. To check the agents, ask Claude "which subagents can you use?" and look for searcher, implementer, reviewer and architect.

## Want more?

Get new free skills and Claude Code tips by email: [wiredkits.com/#notify](https://wiredkits.com/#notify).

WiredKits also makes paid skill packs for the bigger jobs: PR review, debugging, security audits and legacy code. All four for $9.99 at [wiredkits.com](https://wiredkits.com/?ref=github).

Free to use, share and modify under the MIT license (see LICENSE.txt). Questions: hello@wiredkits.com
