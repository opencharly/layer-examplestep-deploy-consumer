# examplestep-deploy-consumer

Reference consumer candy for the **deploy-context** (verb-as-step
deploy-execute) leg of external plugin execution.

`examplestep-deploy-consumer`'s `plan:` carries a **deploy-context** `run:` step
with `plugin: examplestep`. At local/VM deploy, charly lowers it to an
`ExternalPluginStep`: the out-of-tree `candy/plugin-example-step` is host-built
and connected out-of-process, and its `OpExecute` is invoked **with the live
target executor** stood up on the go-plugin broker (the `ExecutorService` reverse
channel) — so the plugin writes
`/tmp/charly-examplestep/examplestep-deploy/{applied,probe}` on the target venue
and returns a plugin-script reverse op the host records and replays at
`charly fleet del`. Compose it **with** `candy/plugin-example-step` (which
provides the `examplestep` verb); the deploy-context check proves the marker
landed. This is the deploy-time counterpart of `layer-examplestep-consumer`
(build context).

## What it provides

| Property | Value |
|---|---|
| Layer / candy | `examplestep-deploy-consumer` |
| Plugin verb | `examplestep` (provided by `candy/plugin-example-step`) |
| Markers | `/tmp/charly-examplestep/examplestep-deploy/{applied,probe}` on the target venue |
| Reverse op | recorded at deploy, replayed at `charly fleet del` |
| Service / port | none |

This is a **reference/fixture** candy: it exists to exercise and demonstrate the
deploy-context external-plugin step seam, not to be composed into production
boxes.

## How to use it

Compose it together with the plugin candy that provides the verb, then deploy to
a local/VM target:

```yaml
my-deploy-step-demo:
  candy:
    base: fedora
    candy:
      - '@github.com/opencharly/layer-examplestep-deploy-consumer:v2026.241.1216'
      - '@github.com/opencharly/plugin-example-step/candy/plugin-example-step:v2026.239.1643'
```

After the deploy, the deploy-context check proves the marker landed on the venue:

```bash
test -f /tmp/charly-examplestep/examplestep-deploy/probe
```

## Layout

- `charly.yml` — the `examplestep-deploy-consumer:` candy entity: the
  plugin-candy `candy:` reference, the deploy-context `run:` step, and the
  deploy-context `check:` step.
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — this user overview.

## Related

- Plugin provider: `candy/plugin-example-step`
- Build-context counterpart: `layer-examplestep-consumer`
- Plugin authoring: `/charly-internals:plugin`
- Deploy substrate: `/charly-internals:install-plan`, `/charly-local:local-deploy`
- [`opencharly/charly`](https://github.com/opencharly/charly) — the charly CLI and image builder
- [`opencharly/opencharly`](https://github.com/opencharly/opencharly) — the umbrella
