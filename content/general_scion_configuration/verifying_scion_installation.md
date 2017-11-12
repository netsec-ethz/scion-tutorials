# Verifying SCION installation

## Introduction

After running SCION infrastructure it is necessary to verify its correctly working.

There are several methods of doing this few of them are described in this post.

## Running SCION-viz

Recommended way of verifying correct SCION infrastructure deployment is running visualization tool [SCION-viz](/general_scion_configuration/scion_viz)

## Inspecting log files

SCION log files can be accessed with following command:

```shell
tail -f $GOPATH/src/github.com/netsec-ethz/scion/logs/bs*.DEBUG
```

### What to look for?

TBA

!!! Tip
    If you are running SCION virtual machine image you can display logs by running:

    ```
    checkbeacons
    ```

    from any directory