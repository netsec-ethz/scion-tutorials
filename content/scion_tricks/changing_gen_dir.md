# Updating gen directory

## Introduction

The complete configuration for SCION infrastructure is stored in `gen` directory. This includes topology definition, dispatcher, border router and SCION services configuration. It also includes certificates and other unique AS data.

Content for this directory can be created in several ways:

- Using topology definition from `topology` directory. *Described in [Running SCION on local topology](/general_scion_configuration/local_top.md)*
- Downloading from [SCIONLab Coordination Service](https://www.scionlab.org/). *Described in [Configuring SCION Lab with OpenVPN](/general_scion_configuration/vpn_setup.md).*
- Manually creating topology

## Restarting SCION infrastructure

Every time `gen` directory is changed, it is necessary to restart SCION infrastructure so changes will take effect. This is done with following commands:

```shell
cd $SC

./scion.sh stop
~/.local/bin/supervisorctl -c supervisor/supervisord.conf shutdown
./scion.sh run
```

## Next steps

After topology has been updated it is recommended to [verify that its working correctly](/general_scion_configuration/verifying_scion_installation.md)
