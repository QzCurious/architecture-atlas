# AGENTS.md

## Development policy: clean break

This repository represents only the **current state and current decisions**. The working tree does not need to preserve or explain the history of how the project arrived there; Git history is the historical record.

Treat every change as a **clean break** unless the current task explicitly requires otherwise.

- Remove superseded code, documentation, concepts, terminology, examples, diagrams, tests, and configuration rather than retaining historical traces.
- Do not add migrations, compatibility layers, fallback paths, deprecated aliases, transitional adapters, or other backward-compatibility mechanisms for prior project states.
- Do not preserve old behavior, APIs, structures, or naming merely because they previously existed.
- When a decision changes, update the repository so it describes and implements only the new decision.
- Rewrite affected documentation and examples as though the superseded design had never existed; do not narrate the transition unless that history is itself a current product requirement.
- Prefer deletion over deprecation when something is no longer part of the current design.

In short: **no migration, no fallback, no backward compatibility, and no historical residue. The repository should look as if the current design had always been the design.**
