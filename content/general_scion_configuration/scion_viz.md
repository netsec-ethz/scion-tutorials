# Installing and running SCION-viz


## Introduction

SCION-viz is a web-based visualization tool of the SCION topology. Connecting to a running infrastructure instance, it reads necessary information about the network topology.

## Prerequisites 

In order to run SCION-viz, you must have your SCION infrastructure up and running.

!!! tip "SCION VM setup"
    If you are using SCION VM, then SCION-viz is already installed and running as a service. You can access it over [localhost:8000](http://localhost:8000)
    This port is automatically forwarded inside the VM, so you can access SCION-viz from your normal host machine.

SCION-viz uses libraries that are located in the `python` directory of the SCION repository. In order to make those libraries visible, we need to export the `PYTHONPATH` environment variable.

```shell
echo 'export PYTHONPATH="$GOPATH/src/github.com/netsec-ethz/scion/python:$PYTHONPATH"' >> ~/.profile
source ~/.profile
```

## Installation

Navigate to the root SCION directory:

```shell
cd $GOPATH/src/github.com/netsec-ethz/scion
```

Checkout the project and install the dependencies:

```shell
cd sub
git clone git@github.com:netsec-ethz/scion-viz
cd scion-viz/python/web
pip3 install --user --require-hashes -r requirements.txt
python3 ./manage.py migrate
```

## Running 

To run SCION-viz:

```shell
python3 ./manage.py runserver 0.0.0.0:8000
```
You can replace the port (8000) by any other that suits your taste.

!!! Tip
    Make sure that your SCION infrastructure is running in order to see meaningful results.
    In particular, SCION-viz requires the `sciond` process to be running.

To verify that the setup is working correctly, visit [localhost:8000](http://localhost:8000). The result should be similar to the following:

![SCION-viz](/images/scion_viz.png)

## Using SCION-viz

TBA