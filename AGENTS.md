# AGENTS.md

This repository contains the Multi-tenant Agentic AI SaaS Reference Architecture.

## Working Agreement

- Treat this repository as the source of truth for architecture, requirements, design, implementation notes, and verification artifacts.
- Prefer small, reviewable changes with clear commit messages.
- Preserve user-authored work. Do not revert or overwrite existing changes unless explicitly asked.
- Before editing, inspect the surrounding files and follow the existing structure and naming conventions.
- Keep documentation practical, current, and connected to the repository contents.

## Spec-Driven Development

- Maintain spec-driven development artifacts in the repository as requirements evolve.
- When the user provides EARS syntax requirements, turn them into structured artifacts and update the relevant specification files.
- Keep requirements atomic, testable, and traceable.
- Preserve the EARS wording unless a correction is needed for clarity, consistency, or testability.
- When changing requirements, update related design notes, task lists, acceptance criteria, and verification notes when those artifacts exist.
- When implementation begins, default to the current active implementation slice in `specs/implementation-slices.md` and do not implement out-of-scope capabilities unless explicitly requested.
- If an EARS requirement is ambiguous, make a reasonable interpretation when safe and record the assumption. Ask for clarification only when the ambiguity changes architecture, data boundaries, security posture, or user-facing behavior.

## EARS Requirement Style

Use EARS patterns such as:

- Ubiquitous: `The <system> shall <response>.`
- Event-driven: `When <trigger>, the <system> shall <response>.`
- State-driven: `While <state>, the <system> shall <response>.`
- Unwanted behavior: `If <condition>, then the <system> shall <response>.`
- Optional feature: `Where <feature is included>, the <system> shall <response>.`

Requirements should include stable identifiers when the surrounding artifact supports them.

## Repository Hygiene

- Use Markdown for human-readable architecture and specification artifacts unless another format is already established.
- Keep generated or derived artifacts separate from hand-authored source artifacts when possible.
- Do not introduce new tooling, frameworks, or folder conventions without a clear repository need.
- Run relevant checks before committing when checks exist.
- If no checks exist, state that clearly in the final response.

## Git

- Check `git status` before and after changes.
- Commit only the files relevant to the current request.
- Use concise, imperative commit messages.
- Do not amend, rebase, reset, or force push unless explicitly requested.
