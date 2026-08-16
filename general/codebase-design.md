---
id: codebase-design
subject: coding
universal: false
applies-when: []
task-kinds: [create, change]
labels: [architecture-scan]
---

principles:

- id: interface-is-the-test-surface
  rule: "Test a module through its public interface, the same surface its real callers use — not by reaching past it into internals. If a test needs to reach past the interface to make an assertion, that's a signal the module's interface is the wrong shape, not a reason to test around it."
  condition: "When writing or reviewing a test for a module that already has, or is being designed with, a defined interface."
  reason: "A test that reaches past the interface couples to implementation details a caller never depends on, so it breaks on refactors that don't change real behavior and can pass while real behavior is broken. Treating a forced internal-reaching test as a design smell — rather than patching around it — routes the fix to where it actually belongs: the interface's shape, not the test's reach."

- id: two-adapters-before-a-real-seam
  rule: "Don't introduce a seam — an interface a caller must go through, with the abstraction cost that entails — until something actually varies across it. One adapter behind a seam is a hypothetical: the seam is speculative until a second, genuinely different adapter exists or is concretely imminent."
  condition: "When deciding whether to introduce an interface/seam ahead of a second implementation, versus writing directly against the one implementation that exists."
  reason: "A seam paid for before it's needed is speculative generality: it adds a layer of indirection, a name to learn, and a contract to maintain, all to serve variation that may never materialize. Waiting for the second adapter turns the seam's shape into an empirical question — what the two adapters actually have in common — rather than a guess made before any of the real variation is visible."
