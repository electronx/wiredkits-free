---
name: release-notes
description: Write release notes or a CHANGELOG entry from the commits and merged PRs since the last release. Use when asked for "release notes", "update the changelog", "what changed since v1.2", or before tagging a release.
---

# Write release notes

You turn a range of commits into notes a user of the project can act on: what's new, what's fixed, and what they must change. Internal churn stays out.

## 1. Find the range

1. A range or versions in the request ("since v1.2", "v1.2..v1.3"): use it.
2. Otherwise from the latest tag to `HEAD`: `git describe --tags --abbrev=0`. If there are no tags, ask where to start, or use the last `CHANGELOG.md` entry's date.

List the commits: `git log <from>..<to> --no-merges --format='%h %s%n%b---'`. If the repo uses GitHub and `gh` works, also get merged PRs in the range (`gh pr list --state merged --search "merged:>=<date of from>" --json number,title,body,labels,author`), because PR titles and labels are usually better than commit messages.

If the range is empty, say so and stop.

## 2. Learn the format

If `CHANGELOG.md` (or `CHANGES`, `HISTORY.md`, `docs/changelog*`) exists, copy its format exactly: heading style, version and date format, section names, link style. If it says "Keep a Changelog", use its sections: Added, Changed, Deprecated, Removed, Fixed, Security. If there is no changelog, use: Breaking changes, New, Improved, Fixed.

## 3. Sort every change

For each commit or PR, read the diff when the title doesn't make the user-facing effect clear (`git show --stat <sha>`, then the relevant hunks). Then put it in one bucket:

- **Breaking**: anything a user must change to upgrade: removed or renamed APIs, flags, config keys, env vars, changed defaults, dropped runtime versions, schema migrations. Check this hardest. A commit titled "refactor" can still break a public API.
- **New**, **Improved**, **Fixed**, **Security**: user-visible changes.
- **Leave out**: refactors with no behavior change, tests, CI, formatting, dependency bumps with no user effect, reverted pairs (a commit and its revert cancel out). Mention dependency bumps only for security fixes or a raised minimum version.

## 4. Write it

- One line per change, written for users: "Retries failed webhooks up to 3 times", not "add retry logic to WebhookSender class".
- Merge commits that are parts of one change into one line.
- Add the PR or issue link and the author's handle if the existing changelog does.
- Under **Breaking**, say what to do: "Rename `API_KEY` to `ACME_API_KEY` in your env."
- Order inside a section: biggest user impact first.

## 5. Suggest the version

If the project uses semver, suggest the next version: major if anything is breaking (minor while below 1.0), minor for new features, patch for fixes only. Say why in one line. Don't tag or publish.

## 6. Deliver

Show the notes. If asked to update the changelog, insert the entry at the top under any intro text, keep an `## Unreleased` section if the file has one, and show the diff. Don't commit, tag or create a GitHub release unless asked; for a release, use `gh release create <tag> --notes-file <file>` only after the user confirms the tag.
