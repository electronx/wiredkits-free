---
name: explain-code
description: Explain how a piece of code works (a function, file, module, or a flow like "what happens when a user signs up") with file and line references, traced from the actual code. Use when asked "explain this", "how does X work", "what does this function do", "walk me through", or "where is X handled".
---

# Explain code

You explain code the way a senior teammate would at a whiteboard: the short answer first, then the path through the code, with every claim pointing at a file and line. You only state what you read.

## 1. Pin down the question

Work out what to explain:

- **A symbol or file** named in the request: find it (`rg -n "def name|function name|class Name|name ="`, or by path).
- **A behavior** ("what happens when a user resets their password"): find the entry point first: the route, CLI command, event handler, job or UI action. Search for the URL path, command name, event name or user-facing text.
- **Something vague** ("explain the backend"): ask one question to narrow it, or explain only the top-level layout and offer to go deeper on one part.

If you find several matches, say which one you picked and why.

## 2. Trace it

Read the code; don't guess from names. Starting at the entry point, follow the calls that matter:

- Open each function you're going to describe. If a call goes through an interface, dependency injection, a decorator, middleware, an event bus or config, find the implementation that actually runs and say how you know.
- Note where data is read or written (database, cache, files, external APIs), where errors are caught or turned into responses, and anything async (queues, background jobs, retries).
- Stop where the code leaves the repo (a library or service) and say what is called and with what.
- Skip logging, metrics and boilerplate unless asked.

If something can't be traced statically (dynamic dispatch, reflection, generated code, config chosen at runtime), say so instead of guessing. If running it is cheap and safe (a test, a REPL line), do that to confirm.

## 3. Explain it

Use this shape, sized to the question:

1. **Short answer:** 1 to 3 sentences in plain words.
2. **The path:** numbered steps in the order things happen, each one sentence with a `path/to/file.ext:line` reference. Usually 3 to 10 steps.
3. **Worth knowing:** up to 4 bullets for things that surprise people: side effects, hidden state, error behavior, performance traps, a known bug you spotted, or a comment that no longer matches the code.

Add a small ASCII diagram only if the flow branches or crosses several services. Quote code only when the exact line matters, and keep quotes under 10 lines.

Match the user's level: if they're new to the language or framework, explain its idioms briefly where they appear.

## 4. Follow-ups

End with one line offering the obvious next step ("Want me to trace the error path?" or "I can add a test that pins this behavior"). Don't change any code unless asked.

For a whole repository rather than one part, the Legacy Code Pack's `map-codebase` skill writes a full `ONBOARDING.md`.
