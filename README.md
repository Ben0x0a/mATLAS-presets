# mATLAS presets

This repository holds the preset library used by the mATLAS app. It is deliberately
separate from the mATLAS app repository: presets evolve on their own schedule (new
sources, corrected field mappings, new tools) and are not tied to an app release.

## What's in here

- **[`mATLAS-presets/`](mATLAS-presets)** — the actual preset library (the only folder
  tracked by this repo's git history), one YAML file per source, grouped by
  tool/platform (`axiom/`, `cellebrite/`, `ios/`, `CDR/`, …). This is the folder you
  point the mATLAS app's `--presets` flag (CLI) or "presets folder" field (GUI) at.
- **`AXIOM_model_parser/`** — kept as a local, *untracked* sibling folder (see
  `.gitignore`) for whoever maintains this checkout: a standalone utility that parses
  Magnet AXIOM artifact reference `.htm` files and generates *starter* preset YAML
  templates for `mATLAS-presets/`. Generated templates are drafts: an analyst must fill
  in the `TODO` placeholders and validate them against real export data before adding
  them to `mATLAS-presets/`. Not distributed with this repo — get it separately from
  the mATLAS project if you need to generate new presets. See its README for usage.

## Installing alongside the app

The mATLAS app and this preset library are versioned and released independently.
Clone them as siblings, e.g.:

```text
some-folder/
├── mATLAS/            # the app (or its packaged executable)
└── presets/           # this repo
    └── mATLAS-presets/
```

Then point the app at `presets/mATLAS-presets`:
- **CLI:** `--presets` has no default — always pass it explicitly, e.g.
  `--presets ../presets/mATLAS-presets`.
- **GUI:** the presets folder field defaults to `<current folder>/presets/mATLAS-presets`,
  so placing this repo as `presets/` next to the app (or its working directory) is picked
  up automatically; otherwise browse to it.

## A preset is not automatically correct for your data

A preset encodes one analyst's mapping of one tool/source's export to the mATLAS model,
at the time it was written. Source formats change across tool versions and OS versions.
**Before trusting a preset's output, check:**
- the preset's `preset.version` and any comments about the tool/OS version it targets;
- whether the exported columns you actually have still match `expected_fields` in the
  preset (a mismatch — new/renamed/missing columns — is exactly the kind of drift that
  should make you re-check the mapping, not just proceed);
- run the linter (`utils/lint_presets.py` from an mATLAS checkout, or
  `model_atlas.presets.lint` directly) against the preset and your target file before
  relying on a run in an actual case.

When in doubt, re-derive or re-review the preset rather than assume it is still accurate.

## Contributing a preset

See the mATLAS app's preset-authoring guide (`docs/guide/preset_authoring.md` in the
mATLAS repo) for the YAML schema and worked examples. The AXIOM model parser tool can
generate a starting template for AXIOM-sourced presets; presets for other tools are
written by hand following the same schema.
