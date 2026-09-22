# pod-qdrant

The Qdrant vector-search stack for
[opencharly/charly](https://github.com/opencharly/charly):

- **box/qdrant** — the Fedora 43 image composing the qdrant service layer
  ([`layer-qdrant`](https://github.com/opencharly/layer-qdrant)) and the external
  command:qdrant + verb:qdrant plugin
  ([`plugin-qdrant`](https://github.com/opencharly/plugin-qdrant)).
- **check-qdrant-pod** — the disposable R10 bed: deploys the qdrant box and drives
  the FULL capability surface through BOTH the `qdrant:` check verb (host side, Go
  client over the reverse channel) and the `charly qdrant` CLI (R3 parity), from
  charly.yml alone.

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

The bed asserts the pod reaches steady state, REST `/readyz` answers, the admin
key is ENFORCED (unauth rejected / auth accepted), and the full management flow
works: collection create → list → info → exists, point upsert → count → query →
get → scroll → delete, snapshot create → list → delete — through the verb and
re-read through the `charly qdrant` CLI. Baked candy checks cover readiness, the
service, and the auth boundary.
