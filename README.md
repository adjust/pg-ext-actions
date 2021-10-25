# pg-ext-actions

A custom Github actions for testing PostgreSQL extensions.

## Actions

### `pg-setup`

Install and run specified PostgreSQL version.
Arguments:
* `version`: major PostgreSQL version, required;
* `install-contrib`: whether to install postgres-contrib package; `'true'` for yes, anything else is considered a no, optional.

### `install-dependency`

Clone, build and install listed PostgreSQL extensions.
Arguments:
* `repository`: a space separated list of repositories of extensions to be installed, required;
* `host`: git platform hostname, optional; GitHub (github.com) is used by default;
* `auth-token`: authentication token (e.g. GitHub personal access token), optional.

### `build-check`

Build and install extension and run regression tests. In case of test failure the `regression.diff` is printed out and error code is returned.
Arguments:
* `working-directory`: directory to run script in, optional; by default runs script in current directory.

## Example for Github Actions

```yaml
name: CI

on:
  push:
    branches: ['*']
  pull_request:
    branches: ['*']

jobs:
  test:
    strategy:
      fail-fast: false
      matrix:
        pg: [14, 13, 12, 11, 10, 9.6]
    name: PostgreSQL ${{ matrix.pg }}
    runs-on: ubuntu-latest
    steps:
      # Install PostgreSQL
      - uses: adjust/pg-ext-actions/pg-setup@master
        with:
          version: ${{ matrix.pg }}
          install-contrib: 'true'

      # Install dependencies
      - uses: adjust/pg-ext-actions/install-dependency@master
        with:
          repository: <account>/<repo>
          auth-token: ${{ secrets.SECRET_TOKEN }}

      # Clone and build extension, run tests
      - uses: actions/checkout@v2
      - uses: adjust/pg-ext-actions/build-check@master
```
