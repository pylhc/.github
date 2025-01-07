# Continuous Integration Workflows

This package implements different reusable workflows for CI.
They are organized as follows.

## Documentation

The `documentation` workflow builds the documentation.
It runs on `ubuntu-latest` and our latest supported Python version.
If the workflow is run from `master` it pushes the successfully build documentation to the `gh-pages` branch, if run from a `pull_request` the documentation is uploaded as an artifact to allow manual checks.

Input parameters:

- `extra-dependencies`: name of the extra dependencies required to run the tests (defaults to `doc`).
- `dependency-file`: name of the file which contains the dependencies (used to get a hash-id for the caching).

This workflow should only be triggered on pushes to `master` and on `pull_request`s.

## Testing Suite

Tests are ensured in the `tests` workflow.
Tests run on a matrix of all supported operating systems and all currently supported Python versions.

Input parameters:

- `pytest-options`: options to be passed on to `pytest`, e.g. `-m "not extended and not cern_network"` (defaults to an empty string).
- `extra-dependencies`: name of the extra dependencies required to run the tests (defaults to `test`).
- `dependency-file`: name of the file which contains the dependencies (used to get a hash-id for the caching).

These tests could easily be run in two stages by calling the test with different `pytest-options`.
E.g. the first stage runs our simple tests (the `basic` tests), and the second one runs the rest of the testing suite (the `extended` tests).
The workflow should be run on all push-events except to `master`.

## Test Coverage

Test coverage is calculated in the `coverage` workflow.
It runs on `ubuntu-latest` and our latest supported Python version, and reports the coverage results of the test suite to `CodeClimate`.

Input parameters:

- `src-dir`: a required string, which indicates the name of the directory containing the source-code for which the coverage is calculated.
- `pytest-options`: options to be passed on to `pytest`, e.g. `-m "not cern_network"` (defaults to an empty string).
- `extra-dependencies`: name of the extra dependencies required to run the tests (defaults to `test`).
- `dependency-file`: name of the file which contains the dependencies (used to get a hash-id for the caching).

Secrets:

- `CC_TEST_REPORTER_ID`: The CodeClimate test-reporter ID.

This workflow should be triggered on pushes to `master` and any push to a `pull request`.

## Regular Testing

A `cron` workflow runs the full testing suite, on all supported operating systems and supported Python versions.
It is very similar to the normal Testing Suite, but in addition also runs on `Python 3.x` so that newly released Python versions that would break tests are automatically included.

Input parameters:

- `pytest-options`: options to be passed on to `pytest`, e.g. `-m "not cern_network"` (defaults to an empty string).
- `extra-dependencies`: name of the extra dependencies required to run the tests (defaults to `test`).
- `dependency-file`: name of the file which contains the dependencies (used to get a hash-id for the caching).

The workflow should be triggered in regular intervals, e.g. every Monday at 3am (UTC time).

## Publishing

Publishing to `PyPI` is done through the `publish` workflow, which builds the package, checks the created artifacts, and pushes to `PyPI` if checks are successful.

Input parameters:

- `dependency-file`: name of the file which contains the dependencies (used to get a hash-id for the caching).

Secrets:

- `PYPI_USERNAME`: The pypi username.
- `PYPI_PASSWORD`: The pypi password.

This workflow should be set to trigger anytime a `release` is made of the GitHub repository.

## Assign Labels

Assign labels from json definition file in `labels/` to selected pylhc repos.
This is not a re-usable workflow, but runs on every push to `master` on THIS repo.
