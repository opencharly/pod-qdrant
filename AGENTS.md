# AGENTS.md — pod-qdrant

The `pod-qdrant` repo ships the Qdrant vector-search image (`box/qdrant`) and the
disposable `check-qdrant-pod` R10 bed. The box composes the qdrant service layer
([`layer-qdrant`](https://github.com/opencharly/layer-qdrant)) and the external
`command:qdrant` + `verb:qdrant` plugin
([`plugin-qdrant`](https://github.com/opencharly/plugin-qdrant)) onto the Fedora
43 base; the bed proves the whole stack.

Canonical files:

- `charly.yml` — the `check-qdrant-pod` bed (`pod:`, `disposable: true`,
  `lifecycle: dev`, `plan:`) plus the root `discover:` block that loads `box/`.
- `box/qdrant/charly.yml` — the `qdrant` box entity (base, `distro` tag chain,
  composed candy pins, build-context checks).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-image:image` — the box authoring reference (`charly.yml` box entity,
  `base:`, composed `candy:`, build/deploy scope). Load before editing or
  building the box.
- `/charly-check:check` — the check/R10 framework and disposable-bed authoring
  (`charly check run <bed>`). Load before editing or running the bed.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`, service declarations).
- `/charly-internals:git-workflow` — before any git/PR action.

This repo's candy carries no `skill:` entity of its own. The qdrant service layer
and the `charly qdrant` CLI / `qdrant:` verb each carry a `skill:` entity in their
own repos (`layer-qdrant`, `plugin-qdrant`), but neither is projected into the
marketplace corpus yet, so no `/charly-qdrant:*` skill is loadable. The gap is
routed to the named skill-authoring batch
[opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291);
when the qdrant family is projected, add the owning skill here.

## Build / validate / test

- `charly box validate` at the repo root — the structural check: the manifests
  must parse and validate at the installed charly.
- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no**
  per-repo candy gate. Its only workflow file is
  `.github/workflows/tag-on-merge.yml`.
- The live R10 witness is the bed itself: `charly check run check-qdrant-pod`
  deploys the box (`disposable: true`) and drives the full capability surface —
  collection, point, and snapshot CRUD through the `qdrant:` verb and the
  `charly qdrant` CLI, plus persistence across a fresh `charly update`. The box's
  own build-context checks assert the qdrant binary and its version.

## Modify this repo

- Edit the bed in `charly.yml` or the box in `box/qdrant/charly.yml`. The
  `discover:` block in the root manifest loads `box/`; keep the bed's `image:` in
  step with the box name.
- The `distro` tag chain in `box/qdrant/charly.yml` is load-bearing — a box that
  declares `base:` alone matches no per-distro section and installs nothing.
- Keep the composed `layer-qdrant` / `plugin-qdrant` pins in step with their
  released tags; a bump moves the CLI, verb, and service together.
- The bed's `${HOST_PORT:6333}` / `${HOST_PORT:6334}` substitutions and the
  `charly/api-key qdrant` secret path must stay in step with the layer's port and
  auth contract.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge on
  PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the PR
  body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
