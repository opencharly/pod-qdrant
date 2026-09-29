# pod-qdrant

The Qdrant vector-search stack for
[opencharly/charly](https://github.com/opencharly/charly): the `box/qdrant` image
plus the disposable `check-qdrant-pod` R10 bed.

## What it provides

- **`box/qdrant`** — the Fedora 43 image composing the qdrant service layer
  ([`layer-qdrant`](https://github.com/opencharly/layer-qdrant)) and the external
  `command:qdrant` + `verb:qdrant` plugin
  ([`plugin-qdrant`](https://github.com/opencharly/plugin-qdrant)).
- **`check-qdrant-pod`** — the disposable R10 bed: deploys the qdrant box and
  drives the full capability surface through both the `qdrant:` check verb (host
  side, Go client over the reverse channel) and the `charly qdrant` CLI (R3
  parity), from `charly.yml` alone.

| Property | Value |
|---|---|
| Box | `qdrant` (base `quay.io/fedora/fedora:43`) |
| Ports | REST `6333`, gRPC `6334` |
| Storage | `~/.qdrant` volume |
| Auth | admin API key from the credential store |
| R10 bed | `check-qdrant-pod` (`disposable: true`, `lifecycle: dev`) |

## Deploy

```bash
charly config qdrant
charly start qdrant
charly secrets get charly/api-key qdrant   # the generated admin key
```

## The R10 bed

```bash
charly check run check-qdrant-pod
```

The bed asserts the pod reaches steady state and the full management flow
(collection create → list → info → exists → delete; point upsert → count → query
→ get → scroll → delete; snapshot create → list → delete; the `charly qdrant` CLI
parity steps; and persistence across a fresh `charly update`). The composed
candy's baked checks cover the service, `/readyz`, and the admin-key boundary.

## Layout

- `charly.yml` — the `check-qdrant-pod` R10 bed (with the `discover:` block
  loading `box/`).
- `box/qdrant/charly.yml` — the `qdrant` box entity (base, composed candies,
  build-context checks).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- The owning procedures are `/charly-image:image` (box authoring) and
  `/charly-check:check` (disposable-bed authoring). The qdrant service layer and
  the `charly qdrant` CLI / `qdrant:` verb each carry a `skill:` entity in their
  own repos (`layer-qdrant`, `plugin-qdrant`), but neither is projected into the
  marketplace corpus yet, so no `/charly-qdrant:*` skill is loadable — that
  corpus-refs gap is recorded against
  [opencharly/opencharly#291](https://github.com/opencharly/opencharly/issues/291).
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
