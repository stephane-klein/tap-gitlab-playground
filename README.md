# Tap-GitLab playground

Project status: Work in progress

In this project I use `tap-gitlab` to extract GitLab data via API and import this data in PostreSQL database.

Question: [Should I use https://github.com/singer-io/tap-gitlab or https://gitlab.com/meltano/tap-gitlab ?](https://github.com/singer-io/tap-gitlab/issues/19)

In this playground, I will use:

- [`meltano/tap-gitlab`](https://gitlab.com/meltano/tap-gitlab)
- [`datamill-co/target-postgres`](https://github.com/datamill-co/target-postgres)

## Prerequisites

On macOS, install with Brew:

```
$ brew install python
$ brew cask install docker
```

## Install and configure tap-gitlab

```
$ python3 -m venv .venv/tap-gitlab/
$ ./.venv/tap-gitlab/bin/pip install git+https://gitlab.com/meltano/tap-gitlab.git
```

## Install target-postgres

```
$ python3 -m venv .venv/target-postgres/
```

```
$ ./.venv/target-postgres/bin/pip install singer-target-postgres==0.2.4
```

## Start PostgreSQL database

```
$ ./scripts/up.sh
```

## Execute Pipedrive importation to PostgreSQL

The first time, when `state.json` don't exists, execute:

```
$ ./.venv/tap-gitlab/bin/tap-gitlab \
    -c tap-gitlab-config.json \
  | ./.venv/target-postgres/bin/target-postgres \
    --config target_postgres_config.json \
    > state.json
```

Next execute this commands to use `state.json` [Singer State](https://github.com/singer-io/getting-started/blob/master/docs/CONFIG_AND_STATE.md) file:

```
$ ./.venv/tap-gitlab/bin/tap-gitlab \
    -c tap-gitlab-config.json \
    -t state.json \
  | ./.venv/target-postgres/bin/target-postgres \
    --config target_postgres_config.json \
    > state-target.json
$ tail -1 state-target.json > state.json
```
