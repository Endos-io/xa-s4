# Reef Labs snapshot — xarray

This repository is a **frozen snapshot** of an upstream open-source project, prepared for
Reef Labs evaluation. It is not a fork for development and it does not track upstream.

| | |
|---|---|
| upstream | https://github.com/pydata/xarray |
| licence | Apache-2.0 (`LICENSE`; `licenses/` holds 8 vendored third-party notices) |
| base commit `C` | `5c1ff1cb2e3ce98906877c56fd267e6f9f886337` |
| C date | 2026-07-29 |
| C subject | Bump the actions group with 4 updates (#11482) |
| history | full ancestry of C, 6,469 commits, **no tags** |

## What was removed from the upstream tree

**`.github/dependabot.yml`.** This is *not* a workflow — it is GitHub-native configuration that
the Dependabot service reads directly, with no workflow file involved, so removing
`.github/workflows/` does not disable it and a workflow census cannot see it. Left in place it
opens pull requests, and **PRs and issues share one number sequence**, so those PRs would
permanently consume the low issue numbers this snapshot needs. (On a sibling snapshot exactly
this happened: two Dependabot PRs took #1 and #2, and GitHub never reuses a number.) Dependabot
is additionally disabled at the repository level — `vulnerability-alerts` returns 404 and
`dependabot_security_updates` reports `disabled`.

`.github/workflows/` — all 11 upstream entries, deleted in the same commit that adds the
single `baseline.yml` below:

```
benchmarks-last-release.yml   benchmarks.yml            ci-additional.yaml
ci.yaml                       configure-testpypi-version.py
hypothesis.yaml               label-prs.yml             nightly-wheels.yml
publish-test-results.yaml     pypi-release.yaml         upstream-dev-ci.yaml
```

Nothing under `.github/` is referenced by any test, `conftest.py`, or packaged module — verified
by grep before removal (the only matches were two documentation URLs, `pydap.github.io` and
`unidata.github.io`, inside docstrings). Removing these files therefore changes no test outcome.

## What was added

- `.github/workflows/baseline.yml` — a single `workflow_dispatch`-only job that installs the
  project and runs the suite once, uploading `junit.xml`. It never fires on push.
- `SNAPSHOT.md` — this file.

## Upstream config that was KEPT because it is inert here

These files configure third-party GitHub Apps. None of the relevant apps is installed on this
account — the only installations are `railway-app` and `claude` — so each acts on nothing:

- `.github/stale.yml` (probot-stale; `daysUntilStale: 600` in any case)
- `.pre-commit-config.yaml` `ci:` block (pre-commit.ci) · `.codecov.yml` (Codecov)
- `.readthedocs.yaml` (Read the Docs) · `.binder/` (mybinder.org)

Two more are now inert because the workflow that read them is gone:
`.github/labeler.yml` (read by `label-prs.yml`) and `.github/nightly-wheel-failure-template.md`
(read by `nightly-wheels.yml`). `.github/ISSUE_TEMPLATE/`, `PULL_REQUEST_TEMPLATE.md`,
`FUNDING.yml` and `release.yml` are GitHub-native but affect only the web UI for
newly-authored issues/PRs/releases; the snapshot's issues are created through the API with
explicit bodies, so templates do not apply to them.

## What was deliberately KEPT

- `CLAUDE.md` and `.claude/skills/upgrade-min-versions.md`. These are upstream's own
  agent-directed files. They are part of the condition being measured and were left in place
  rather than removed, which would have shaped the task and widened the diff exclusions.
- `LICENSE` and all 8 notices under `licenses/`.
- Every other file at `C`, byte for byte.

## Version derivation

The project derives its version with `setuptools-scm` (`[tool.setuptools_scm] fallback_version =
"9999"`). Measured on this tagless tree: with `.git` present and no tags the derived version is
`10000.dev6469+g5c1ff1cb2`; with no `.git` at all it falls back to `9999`. Neither errors, and no
test reads xarray's own version, so **no tags were pushed**.

## Held-out tests

The commit that carries this file also adds the test halves of eight upstream fixes, applied to
the frozen tree **in history order** (two of them take an earlier one's additions as patch
context). The corresponding code halves are **not** present — that is the work.

| item | upstream issue | test files touched |
|---|---|---|
| 1 | #11462 | `xarray/tests/test_computation.py` |
| 2 | #9969  | `xarray/tests/test_indexes.py` |
| 3 | #11238 | `xarray/tests/test_groupby.py` |
| 4 | #10194 | `xarray/tests/test_coordinates.py`, `test_dataset.py`, `test_variable.py` |
| 5 | #11341 | `xarray/tests/test_dataarray.py` |
| 6 | #11452 | `xarray/tests/test_plot.py` |
| 7 | #10305 | `xarray/tests/test_conventions.py` |
| 8 | #11518 | `xarray/tests/test_groupby.py`, `test_rolling.py` |

This commit is **`base_commit`**. The freeze commit below it is not.

## Reproducing the environment

```
python -m pip install -U "pip>=25.1" setuptools wheel   # 25.0.1 rejects --group
python -m pip install -e . --group pyproject.toml:dev
python -m pip install tzdata                            # NOT a Python dependency; without it
                                                        # test_pandas_to_xarray.py raises at
                                                        # import and interrupts collection
```
