---
name: commit-message
description: Write a commit message for the staged changes that matches the repo's own style, and commit when asked. Use when asked to "write a commit message", "commit this", "commit my changes" or "what should this commit say".
---

# Write a commit message

You write the message a careful teammate would: it says what changed and why, in the repo's own style, and nothing the diff doesn't show.

## 1. Find the change

1. Run `git diff --cached`. If something is staged, that is the commit.
2. If nothing is staged, run `git status --short` and `git diff`. Say what is unstaged and ask whether to stage all of it, some of it, or stop. Never stage files on your own that look unrelated, generated, or secret (`.env`, keys, build output).
3. If there are no changes at all, say so and stop.

If the staged diff mixes unrelated changes (a bug fix and a rename across 30 files, say), say so and offer to split it into separate commits with `git add -p` style hunks. Don't split without asking.

## 2. Learn the repo's style

Run `git log -20 --format='%s%n%b---'` and look at:

- **Format.** Conventional Commits (`feat(api): ...`), a ticket prefix (`ABC-123: ...`), a tag like `[ui]`, or plain sentences.
- **Mood and case.** "Add" vs "Added" vs "adds"; capitalized or not; trailing period or not.
- **Bodies.** Whether commits usually have one, and how long.
- **Trailers.** `Co-authored-by`, `Signed-off-by`, `Refs #123`.

Also check `CONTRIBUTING.md`, `CLAUDE.md`, `.gitmessage`, and commitlint config (`commitlint.config.*`, `.commitlintrc*`). A written rule beats what the log shows.

If the repo has no history, use a plain imperative subject ("Add retry to webhook sender") and a short body.

## 3. Understand why

Read enough of the changed code to know what it does, not only which lines moved. Find the reason in this order: what the user told you, the branch name, a linked issue (`gh issue view <n>` if a number is in the branch or request), code comments in the diff. If you can't find a reason, describe what changed and leave the why out. Never invent one.

## 4. Write it

- **Subject:** at most 72 characters (50 if the log keeps to that), in the repo's format, saying what the commit does for someone reading `git log --oneline` a year from now. "Fix double refund when payment API times out", not "Fix bug" or "Update refund.ts".
- **Body**, when the change isn't obvious from the subject: a blank line, then 1 to 4 short lines on why, and anything a reviewer must know (a behavior change, a migration, a follow-up). Wrap at 72.
- **Breaking changes:** if public behavior, an API, a config key or a schema changes incompatibly, say so: `BREAKING CHANGE: ...` for Conventional Commits, otherwise a plain line in the body.
- Reference an issue only if you found one.

Don't list every file. Don't write "This commit...". No emoji unless the log uses them.

## 5. Show, then commit

Show the message in a code block. Commit only if the user asked you to commit; otherwise ask. Commit with `git commit -F -` (pass the message on stdin) so quotes and newlines survive. If a hook fails, show its output, fix what it complains about if it's clearly part of this change, and make a new commit attempt. Never use `--no-verify` unless the user says so.

## Options the user may pass

- `--amend`: rewrite the last commit's message instead (only if it hasn't been pushed, or the user confirms).
- `--short`: subject line only.
