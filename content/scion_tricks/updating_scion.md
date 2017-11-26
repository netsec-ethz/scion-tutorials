# Updating SCION to newest version

## Introduction

As SCION is hosted on git, updating to latest version is simple as `pull`-ing changes.

## Getting the latest version

To download newest version first navigate to SCION root directory:

```shell
cd $SP
```

and pull newest changes from `scionlab` branch:

```shell
git pull origin scionlab
```

If git reports that new modifications are downloaded, it is necessary to restart scion infrastructure:

```shell
./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```