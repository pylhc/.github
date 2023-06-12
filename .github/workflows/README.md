# Continuous Integration Workflows

This package implements different reusable workflows for CI.
They are organized as follows.

## Documentation

The `documentation` workflow builds the documentation. 
It runs on `ubuntu-latest` and one of our supported version, currently `Python 3.9`.
If the workflow is run from `master` it pushes the successfully build documentation to the `gh-pages` branch,
if it is run from a `pull_request` the documentation is uploaded as an artifact to allow manual checks.

This workflow should only be triggered on pushes to `master` and on `pull_request`s.

## Testing Suite

Tests are ensured in the `tests` workflow.
Tests run on a matrix of all supported operating systems (`ubuntu-20.04`, `ubuntu-22.04`, `windows-latest` and `macos-latest`) for all supported Python versions (currently `3.8` to `3.11`).

Input parameters: 
  - `name` - an optional string, to identify different pytest-configurations (defaults to `tests`).
  - `pytest-options` - options to be passed on to `pytest`, e.g. `-m "not extended and not cern_network"` (defaults to an empty string).

These tests are usually run in two stages, which is easy to achieve by calling the test with different `pytest-options`.
It should be run on all push events except to `master`. 
The first stage runs our simple tests (the `basic` tests) , and the second one runs the rest of the testing suite (the `extended` tests).

## Test Coverage

Test coverage is calculated in the `coverage` workflow.
It runs on `ubuntu-latest` and `Python 3.9`, and reports the coverage results of the test suite to `CodeClimate`.

Input parameters: 
  - `src-dir` - a required string, which indicates the name of the directory 
                containing the source-code for which the coverage is calculated.
  - `pytest-options` - options to be passed on to `pytest`, e.g. `-m "not cern_network"` (defaults to an empty string).

Secrets:
  - `CC_TEST_REPORTER_ID` - The CodeClimate test-reporter ID.

This workflow should be triggered on pushes to `master` and any push to a `pull request`

## Regular Testing

A `cron` workflow runs the full testing suite, on all available operating systems and supported Python versions.
It is very similar to the normal Testing Suite, but in addition also runs on `Python 3.x` so that newly released Python versions that would break tests are automatically included.

Input parameters are: 
  - `pytest-options` - options to be passed on to `pytest`, e.g. `-m "not cern_network"` (defaults to an empty string).

The workflow should be triggered in regular intervals, e.g. every Monday at 3am (UTC time).

## Publishing

Publishing to `PyPI` is done through the `publish` workflow, 
which builds a `wheel`, checks it, and pushes to `PyPI` if checks are successful.

Secrets:
  - `PYPI_USERNAME` - The pypi username.
  - `PYPI_PASSWORD` - The pypi password.

This workflow should be set to trigger anytime a `release` is made of the GitHub repository.