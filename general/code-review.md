---
id: code-review
subject: coding
universal: false
applies-when: []
labels: [review]
---

principles:

- id: structural-examination
  rule: "Examine the working diff for four structural drifts that emerge from solving the problem rather than designing it, and resolve each: (1) implicit coupling via stringly-typed contracts — magic keys, selector strings, attribute names standing in for an explicit interface — give the contract a type or named constant; (2) thin wrappers whose only job is bundling two things with no identity of their own — inline them; (3) logic blocks with a clear purpose but no explicit name — extract and name them; (4) emergent groupings — types and functions that belong together but ended up separated during implementation — regroup them."
  condition: "On the review pass over a working implementation's diff, before the work is considered done — a fresh pass, not the author's own terminal checkpoint."
  reason: "Running code reveals structural seams planning can't predict — thin wrappers and stringly-typed contracts emerge from solving the problem, not from designing the solution. A stringly-typed contract that drifts silently (an aria-label doubling as a test selector, a magic status key standing in for an interface) is exactly the failure a fresh structural pass catches that the author, focused on making it work, slides past."

- id: deletion-test-for-suspected-shallow-module
  rule: "When a module is suspected of being shallow — a wrapper, a pass-through, an abstraction with no clear payoff — imagine deleting it. If the complexity it held simply vanishes, it was a pass-through and should go. If the complexity reappears at every caller that used it, it was earning its keep."
  condition: "When judging whether an existing or proposed module is worth keeping as a separate seam, distinct from inlining it into its caller(s)."
  reason: "A module can look justified by its existence alone — it has a name, a file, a test — without that presence proving it does anything its callers couldn't do as easily inline. The deletion test forces the question onto what actually happens to the complexity, not whether the module currently exists, which is the only reliable way to tell a real abstraction from a decorative one."

- id: single-callsite-helper-scoped
  rule: "A function that computes a value and has exactly one callsite should not be extracted as a standalone function. Resolve it where it's used — as a local in the calling scope (preferred when the expression is long), or inlined directly when it's short."
  condition: "When a standalone helper has exactly one callsite. Does not apply to functions called from two or more places — those earn the extraction."
  reason: "A standalone function implies reuse. A single-callsite helper adds a named concept with no benefit. Keeping the resolution local is more honest about its scope."

- id: minimize-comments-prefer-self-documenting-code
  rule: "Default to no comments; precise naming and clear structure should communicate intent. Add a comment only to explain a genuinely non-obvious constraint, invariant, or deliberate workaround that isn't recoverable by reading the code itself — never to describe what the code does, and never to document UI/UX look, layout reasoning, or behavior."
  condition: "When writing or editing any code, inline or via a spawned implementation agent."
  reason: "Comments drift out of sync with the code they describe — one earned instance required fixing three stale ones in a single session, each describing behavior or symmetry that had since changed or been deleted. Needing a comment to explain what code does is itself a sign the code isn't clear enough. UI/UX documentation has a dedicated home in this system — `ui-library.md`/`ux-library.md` — with its own staleness-detection (the ratify gate's sync trigger); inline comments duplicating that have no equivalent mechanism keeping them honest."

- id: derivable-arithmetic-is-not-a-hidden-constraint
  rule: "Don't justify a numeric or config literal with a comment that only restates arithmetic performed on values already visible at the call site — a ratio, percentage, or unit conversion against a library default or a neighboring literal. That derivation is recoverable by any reader in the time it takes to compute it, so it fails minimize-comments-prefer-self-documenting-code's 'not recoverable by reading the code itself' bar. A literal earns a comment only when it encodes information from outside the code that reading the value can't reveal — a spec, a hardware limit, a prior incident, a compatibility requirement."
  condition: "When about to write a comment justifying a numeric or config literal, especially one framed as a derivation from a library default or another value already present nearby."
  reason: "Being able to show your work computing where a number came from feels like satisfying 'non-obvious constraint,' but the test is whether the reader gains information they couldn't already reconstruct from what's on screen — not whether the author can narrate a derivation. A self-review that only checks a comment for reasoning-leak can still pass one of these: the surviving justification must also be checked against the originating principle's own bar."
