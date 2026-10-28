---
type: Change
title: Updated supported Python versions to 3.11 through 3.14
description: Dropped Python 3.10 support, added Python 3.14 support, and upgraded locked dependencies that lacked Python 3.14 wheels.
tags: [python-versions, ci, dependencies]
status: stable
generated: { by: claude-code/claude-opus-5-5, at: 2026-10-28T14:59:06Z }
sources:
  - id: pyproject
    resource: /pyproject.toml
    title: Project metadata and Python version requirement
  - id: cicd
    resource: /.github/workflows/CICD.yml
    title: CI/CD workflow Python version matrix
  - id: e2e
    resource: /.github/workflows/EndToEnd.yml
    title: End-to-end workflow Python version matrix
  - id: python-eol
    resource: https://devguide.python.org/versions/
    title: Status of Python versions
---

# Summary

The supported Python range changed from 3.10–3.13 to 3.11–3.14.[^pyproject]

# Changes

| Area | Before | After |
|------|--------|-------|
| `requires-python` in `pyproject.toml` and `docs/custom_plugin.md` | `>= 3.10` | `>= 3.11` |
| Trove classifiers | 3.10–3.13 | 3.11–3.14 |
| CI test matrix (`CICD.yml`)[^cicd] | 3.13, 3.12, 3.11, 3.10 | 3.14, 3.13, 3.12, 3.11 |
| CI package build version (`python_package_version`) | 3.13 | 3.11 |
| End-to-end matrix (`EndToEnd.yml`)[^e2e] | 3.10, 3.13 | 3.11, 3.14 |
| Copier template answers (`python_versions`) | 3.10–3.13 | 3.11–3.14 |
| Compatibility statement in `docs/index.md` | 3.10 and above | 3.11 and above |

Supporting changes:

- `tests/ExecuteModules_UnitTest.py`: removed the Python < 3.11 branch that
  expected `ReturnCode.DOESNOTAPPLY` in output. From 3.11 onward,
  `IntEnum.__str__` returns the integer value, so only the `2` form remains.
- `uv.lock`: re-resolved for `>=3.11`, and upgraded the packages whose locked
  versions shipped no Python 3.14 wheels and failed to build from source on
  Windows:
  - `pyyaml` 6.0.2 → 6.0.3 (source build requires a C++ compiler)
  - `rpds-py` 0.24.0 → 2026.9.1 (PyO3 0.24 supports at most Python 3.13)
  - `markupsafe` 3.0.2 → 3.0.4
- Formatting-only changes to YAML quoting in workflow files, inline comment
  alignment in `pyproject.toml`, and trailing whitespace in
  `docs/custom_plugin.md`.

# Rationale

- Python 3.10 reaches end of life in October 2026.[^python-eol] Dropping it
  removes the version-specific test branch and the 3.10 CI jobs.
- Python 3.14 is a released, supported version; adding it to the matrices
  validates the package against it.
- Without the lockfile upgrades, environment creation on Python 3.14 failed
  before any test ran, so the new 3.14 CI jobs would fail.

# Verification

- Unit tests (`pytest -k "not EndToEndTest"`) pass on Python 3.11, 3.13, and
  3.14 on Windows: 264 passed, 1 skipped, coverage 99.6%.
- End-to-end tests were not run locally; they require a GitHub PAT file and
  run in the `EndToEnd` workflow.

[^pyproject]: Project metadata and Python version requirement
[^cicd]: CI/CD workflow Python version matrix
[^e2e]: End-to-end workflow Python version matrix
[^python-eol]: Status of Python versions
