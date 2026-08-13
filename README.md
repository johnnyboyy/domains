# domains

A curated **domain bucket** — a standalone repository of domain files, seeded
from praxis's plugin `domains/` directories, that exists to be published from
and grown by import, not injected into anything directly.

## Layout

One top-level directory per **collection**; the directory name is the
collection's owner name:

```
domains/
  general/         8 domain files
  coding-stack/     7 domain files
  uiux/            10 domain files
  planner/          4 domain files
```

Each collection may carry its own `audit.md` — a per-principle provenance
ledger, append-only, written by `promote`/`import` on every successful
write-back. `audit.md` has no domain-file frontmatter; corpora's
`discover`/`compose` recognize it by filename and skip it — it is **never
composed into a working context**, only read at ratify/retrospective time.
This is the same restored-provenance model documented in corpora's
`DESIGN.md` ("The audit ledger — restored provenance, never composed").

## The model

This bucket is a **curated library and publisher**, not a live source any
project reads through implicitly. It is a praxis/corpora-managed root like
any other — a directory of domain files corpora's own machinery can operate
on directly:

- **`corpora:fold`** (collapse / supersede / retire) and **`corpora:distill`**
  (cluster instances into a meta-principle) maintain this bucket's own pool,
  standing in it, the same way a project maintains `.praxis/domains` or a
  plugin maintains its `domains/`.
- **`corpora:import`** pulls principles INTO this bucket from another
  on-disk domain repository (a project's `.praxis/domains`, a plugin's
  `domains/`, or another bucket). This is how **grown principles come
  home**: a project discovers and ratifies something useful in its own
  work, and importing FROM that project while standing in the bucket is how
  it gets folded back into the shared library, stamped
  `imported-from: <project>/<domain>#<id>`.

Projects consume the bucket in one of two ways:

- **`corpora:import`** — pull specific principles from a collection here
  into the project's own `.praxis/domains`, adopted with `imported-from`
  lineage. Adopted items are project-owned from then on; a later
  `corpora:sync` re-diffs against this bucket by that lineage (changed /
  removed / new / unchanged) and lets the project pull updates deliberately.
  This is the live, editable path — the project can diverge, and sync makes
  divergence visible rather than silently overwriting it.
- **listing a collection directory as a `sources` entry** in the project's
  own corpora config — a read-only baseline. The collection's domains
  compose into the project's working context every time, unedited, with no
  local copy and no lineage to sync. This is the "curated starter library"
  path: cheap to adopt, nothing to maintain, but the project can't fork a
  principle without first importing it.

You edit the domains of the repo you're standing in — when standing in
`domains`, that means editing a collection here directly via `fold`/
`distill`/`promote`. Every other repo (a project, a plugin) is a source you
only ever `import` from, read-only. Reaching OUT to modify another repo's
domains is not a thing this bucket does.

## Provenance

Every file in each collection here was copied verbatim from the
corresponding praxis plugin `domains/` directory at seed time (`general`,
`coding-stack`, `uiux`, `planner`). No principles have been ratified,
folded, or imported into this bucket since seeding — collections start life
with no `audit.md`; one appears the first time a write-back lands.
