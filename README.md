# Starter Kit for Claude Code (free)

Four free Claude Code skills for the small jobs you do every day. They read your repo before they write anything, follow its own conventions, and only state what they checked.

| Skill | What it does | Try saying |
|---|---|---|
| `write-claude-md` | Writes a short, accurate `CLAUDE.md` from what the code actually does: real build and test commands, the rules your team enforces, and the traps that trip Claude up. | "write a CLAUDE.md for this repo" |
| `commit-message` | Writes the commit message for your staged changes in your repo's own style (Conventional Commits, ticket prefixes, whatever the log uses), with the why when it can find one. | "commit this", "write a commit message" |
| `release-notes` | Turns the commits and merged PRs since the last tag into release notes or a CHANGELOG entry, flags breaking changes with what users must do, and suggests the next version. | "release notes since v1.2", "update the changelog" |
| `explain-code` | Explains a function, file or flow with every step pointing at a file and line, traced from the code rather than guessed from names. | "how does checkout work?", "explain this function" |

## Install (1 minute)

Pick one:

- **Claude Code plugin:** run `/plugin marketplace add electronx/wiredkits-free`, then `/plugin install starter-kit@wiredkits-free`.
- **skills CLI:** `npx skills add electronx/wiredkits-free`
- **Copy:** `mkdir -p ~/.claude/skills && cp -R skills/* ~/.claude/skills/` (or into `.claude/skills/` in one repo to share it with your team).

Restart Claude Code and run `/skills` to check that the four skills are listed.

## Want more?

Get new free skills and Claude Code tips by email: [wiredkits.com/#notify](https://wiredkits.com/#notify).

WiredKits also makes paid skill packs for the bigger jobs: PR review, debugging, security audits and legacy code. All four for $9.99 at [wiredkits.com](https://wiredkits.com/?ref=github).

Free to use, share and modify under the MIT license (see LICENSE.txt). Questions: hello@wiredkits.com
