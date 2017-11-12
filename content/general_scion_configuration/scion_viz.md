# Installing and running SCION-viz


## Introduction

SCION-viz is a web based visualization tool of SCION topology. Connecting to a running infrastructure instance it reads necessary information about topology.

## Prerequisites 

In order to run SCION-viz you must have SCION infrastructure up and running.

!!! tip "SCION VM setup"
    If you are using SCION VM then SCION-viz is already installed and running as a service. You can access it over [localhost:8000](http://localhost:8000)

SCION-viz uses libraries that are located in `python` directory of scion repository. In order to make those libraries visible we need to export `PYTHONPATH` environment variable. 

```shell
echo 'export PYTHONPATH="$GOPATH/src/github.com/netsec-ethz/scion/python:$PYTHONPATH"' >> ~/.profile
source ~/.profile
```

Navigate to root scion directory:

```shell
cd $GOPATH/src/github.com/netsec-ethz/scion
```

## Installation 

Checkout project and install dependencies:

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
python3 ./manage.py runserver 0.0.0.0:8080
```

!!! Tip
    Make sure that SCION infrastructure is running in order to see meaningful results

To verify that setup is working go to [localhost:8080](http://localhost:8080). Result should be similar to following:

![SCION-viz](/images/scion_viz.png)

## Using SCION-viz

TBA