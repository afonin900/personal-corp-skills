# Fable Ruki-Agenty (Fable Hands-Agents)

A manually-invoked orchestration mode for Claude Fable 5 under tight usage limits: Fable never writes or reads code itself. It writes complete, self-sufficient specs into GitHub issue bodies and dispatches ready tasks to hands: coding and code review go to Codex (GPT 5.5, openai-codex plugin) or to external worker terminals via Orca, scouting and verification to Sonnet subagents.

## Core rules

- **Issue-first, no exceptions.** Every task becomes a GitHub issue whose body is a full spec (goal, context, resolved forks, steps, boundaries, DoD) before anything is dispatched.
- **Dispatch by pointer.** The implementer reads the issue body via `gh issue view N`; the prompt is just an operational envelope.
- **Fresh-context verifier per task.** A separate subagent runs the DoD check; the one who built it never accepts it.
- **Escalation ladder on failure:** two reworks by the same implementer → fresh implementer with the verifier's diagnosis → `blocked` label and a short report to the user.
- **Async spec-ahead.** While an implementer works, Fable writes the next specs instead of waiting.
- Parallelism is decided by file overlap, not agent count: same-file tasks run sequentially with direct commits to main; file-disjoint groups may run in parallel worktrees.

## Why

Fable tokens are the most expensive resource in the session. Every tool call a cheaper model could have made is burned quota. This skill turns Fable into a pure spec-writer and dispatcher.

This skill is invoked manually (`disable-model-invocation: true`).

Русская версия: [README.ru.md](README.ru.md)
