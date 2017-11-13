# Verifying SCION installation

## Introduction

After running your SCION infrastructure, it is necessary to verify tha it is working correctly.

There are several methods of doing this. Some of them are described in this post.

## Running SCION-viz

The recommended way of verifying a correct SCION infrastructure deployment is running the visualization tool [SCION-viz](/general_scion_configuration/scion_viz)

## Inspecting log files

The SCION log files can be accessed with the following command:

```shell
tail -f $GOPATH/src/github.com/netsec-ethz/scion/logs/bs*.DEBUG
```

### What to look for?

TBA

!!! Tip
    If you are running the SCION virtual machine image, you can display the logs by running:

    ```
    checkbeacons
    ```

	from any directory