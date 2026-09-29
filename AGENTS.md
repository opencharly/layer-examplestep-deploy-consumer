# AGENTS.md — layer-examplestep-deploy-consumer

Standalone reference/fixture candy repo for the **deploy-context**
(verb-as-step deploy-execute) leg of external plugin execution. The candy lives
in `charly.yml` at the repo root: the plugin-candy `candy:` reference, the
deploy-context `run:` step with `plugin: examplestep`, and the deploy-context
`check:` step that proves the marker landed on the target venue. It carries **no
`skill:` entity**, so no owning `/charly-<family>:<name>` skill is projected into
the marketplace corpus.

Canonical files:

- `charly.yml` — the `examplestep-deploy-consumer:` candy entity (no `skill:`
  entity present).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-internals:plugin` — the closest owning skill: the plugin model, the
  deploy-context `ExternalPluginStep` lowering + `OpExecute` over the reverse
  channel this fixture exercises, and the per-plugin CUE schema contract. Load
  before editing or troubleshooting the fixture.
- `/charly-internals:install-plan` — the InstallPlan / deploy-execute IR the
  deploy-context step lowers into. Load when changing the deploy step shape.
- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema,
  `plan:` step verbs incl. `check:`/`run:`, the plugin-step form, `context:`
  selection). Load before editing any entity field or plan step.

There is no dedicated `/charly-*:examplestep-deploy-consumer` owning skill — this
repo's candy carries no `skill:` entity. The gap is recorded against
`opencharly/opencharly#291`; when one is authored, add it here.

## Build / validate / test

- The merge gate is the **org-wide** `charly/pr-validator` (required check
  `validate / validate`, defined in `opencharly/.github`); this repo has **no
  per-repo candy gate** and ships only `.github/workflows/tag-on-merge.yml`.
- The candy's `plan:` `check:` step is the functional evidence:
  `examplestep-deploy-applied` asserts
  `/tmp/charly-examplestep/examplestep-deploy/probe` exists on the target venue
  (deploy context, `in_container: false`), proving the deploy-time `OpExecute`
  over the reverse channel ran.
- The fixture only proves anything when composed WITH
  `candy/plugin-example-step`, which provides the `examplestep` verb, and
  deployed to a local/VM target.

## Modify this repo

- Edit the `examplestep-deploy-consumer:` candy entity in `charly.yml`. Keep the
  `plugin:` verb name aligned with the provider the plugin candy declares, and
  keep the deploy-context `check:` asserting the marker on the venue.
- This is a reference/fixture candy — a change here is a change to the documented
  deploy-context external-plugin example, so keep it minimal and observable.

## Landing

- PR-only. Every change lands through a pull request; the org-required
  `charly/pr-validator` validates the diff and body and arms native auto-merge
  on PASS. Direct pushes to `main` are blocked.
- History lives in `CHANGELOG/` (written by `tag-on-merge` at merge time); the
  PR body IS the changelog.
- The authoritative rulebook is the umbrella `AGENTS.md` in
  `opencharly/opencharly` and `charly/AGENTS.md` in the charly repo. Do not
  restate its rules here.
