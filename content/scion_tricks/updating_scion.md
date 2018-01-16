# Updating SCION to newest version

## Introduction

As SCION is hosted on git, updating to latest version is simple as `pull`-ing changes.

## Getting the latest version

To download newest version first navigate to SCION root directory:

```shell
cd $SC
```

fetch newest changes from remote `scionlab` branch and rebase:

```shell
git fetch origin scionlab
git rebase origin/scionlab
```

If git reports that new modifications are downloaded, it is necessary to restart scion infrastructure:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```