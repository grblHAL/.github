# AGENTS.md — grblHAL project contribution guide

This file tells human contributors and coding agents how to prepare changes for this repository. It is deliberately conservative: CNC control software can damage tooling, stock, or a machine when a seemingly small change is wrong.

## Start here

Before editing:

1. Read `README.md`, `CONTRIBUTING.md` and the relevant driver/plugin documentation, if present.
2. Search for existing issues, discussions, settings, or implementations before proposing a feature or refactor.
3. Do not assume behaviour is a bug. In grblHAL, an odd-looking choice may preserve protocol compatibility, real-time behaviour, or hardware safety.
4. Ask in an issue or discussion before starting a new feature, changing a public interface, or making a broad refactor.

## Git and pull-request rules

- Fork the repository; do not commit to a maintainer's branch.
- Create a fresh branch from the current upstream default branch for each independent change.
- Keep one logical change per pull request. Do not bundle a bug fix with formatting, dependency updates, UI clean-ups, or unrelated refactors.
- Keep commits focused and readable. A reviewer should be able to revert the change cleanly.
- Rebase or merge the current upstream default branch before requesting review when practical.
- A PR description must state: **problem**, **scope**, **behavioural change**, **hardware/firmware tested**, and **known limits**.
- Write the PR title and description yourself. They must be plain, concise, and specific enough for a maintainer to understand the change without reading the whole diff.
- Do not submit an AI-written wall of technical jargon, generic headings, invented certainty, or “analysis” fluff as the PR message. A PR that hides a simple change behind this kind of text may be closed without review; rewrite it in your own words and resubmit.
- New features are not accepted without prior discussion and maintainer agreement.
- Do not open a giant “AI improvement” PR. Split accepted work into small, independently reviewable PRs.

## Change discipline

- Make the smallest change that solves the agreed problem.
- Preserve existing public names, settings, protocol behaviour and file layout unless the change explicitly requires otherwise.
- Do not rename, reformat, or reorganise unrelated code.
- Do not replace an existing implementation merely because another framework, package, pattern, or generated solution looks cleaner.
- Do not commit generated binaries, NuGet package contents, XML documentation, `bin/`, `obj/`, IDE files, or other build outputs unless the repository already intentionally tracks that exact class of file.
- Prefer existing project dependencies. Adding a dependency requires a clear justification and maintainer approval.

## grblHAL-specific safety and compatibility

- Treat every motion, spindle, coolant, probe, safety-door, limit, tool-change, and I/O change as safety-relevant.
- Preserve the controller protocol and existing settings semantics. If compatibility must change, call it out explicitly and provide a migration path where feasible.
- Keep real-time paths deterministic: avoid blocking work, allocations, file/network I/O, locks, and unbounded loops in time-critical or callback code.
- Respect numeric types and units already used by the firmware/API. Do not “fix” integer/float conversions without confirming the intended command and protocol semantics.
- Check edge cases: zero, negative values where valid, fractional values, min/max limits, disabled features, missing optional hardware, alarm/estop states, and failed communications.
- Hardware-dependent behaviour must fail safe. Never make a software convenience change that can bypass an interlock or turn an output on unexpectedly.

## UI and sender code

- Keep UI changes separate from firmware, protocol, and plugin changes unless they cannot be separated.
- Do not alter polling rates, command ordering, or thread ownership without explaining the reason and testing the effect.
- Maintain a clear distinction between machine state reported by the controller and state inferred by the UI.
- Prefer the project’s established UI controls and patterns. Discuss major UI framework or editor-component additions first.

## Tests and validation

Run the narrowest relevant checks first, then the normal project checks where available.

- Build the affected configuration without warnings introduced by your change.
- Add or update tests when the repository has a suitable test framework.
- Manually test the changed path against supported grblHAL/grbl firmware where possible.
- For behaviour affecting a machine, test with outputs disconnected, simulation enabled, or a safe bench setup before a live machine.
- In the PR, state exactly what you tested and what you could not test. Do not claim hardware validation that was not performed.
- Add any tests related to the PR to the [Unit_test](https://github.com/grblHAL/Unit_tests) repository with a separate PR when applicable. Do not add any as part of the PR itself.

## Documentation and comments

- Write code that is clear without a comment where possible.
- Add comments only for non-obvious constraints, safety reasons, protocol quirks, or intentional trade-offs.
- Match the project’s existing documentation style. If it uses Doxygen, use Doxygen; do not add verbose AI-style prose comments beside isolated lines.
- Update user-facing documentation only when behaviour, setup, configuration, or compatibility changes.
- Do not add boilerplate summaries, marketing language, or speculative “future work” notes to a PR.

## Using AI tools

AI may assist with investigation, drafting, tests, or narrowly scoped code changes, but the contributor remains responsible for every line submitted.

- Give the tool a narrow task and relevant repository context.
- Review the complete diff yourself; do not submit generated output blindly.
- Verify every API, protocol claim, dependency, and test result. AI output can sound plausible while being wrong.
- Do not ask an agent to make broad autonomous changes across the repository.
- Do not hide AI-generated work behind a vague title or a massive mixed commit.

## Stop and ask

Pause for maintainer direction if the work involves any of the following:

- a new feature, plugin, dependency, protocol extension, or user-facing setting;
- a compatibility break or setting default change;
- real-time scheduling, threading, command ordering, or data-race concerns;
- safety inputs/outputs, alarms, limits, probing, spindle or tool-change behaviour;
- a refactor spanning multiple subsystems; or
- an unexpectedly large diff.

When in doubt, open a small issue or discussion first with the observed behaviour, relevant firmware/controller details, proposed scope, and a minimal reproducible example.
