# Setup and Run AS Visualizations

## Introduction

SCION-viz is a web-based visualization tool of the SCION topology. Connecting to a running AS infrastructure, it reads and displays information about the network topology.

## 1. Setup on a local system

If you are using the SCIONLab VM distribution you can skip this step, as SCION-viz is already installed.

First, check if PYTHONPATH is set: `echo $PYTHONPATH`. If it is set, ensure that the scion directory and scion/python directories are both included. If they are missing, you can set PYTHONPATH as follows:

```shell
echo 'export PYTHONPATH="$SC/python:$SC"' >> ~/.profile
source ~/.profile
```

Next, you will need to clone the repository as follows:

```shell
cd $SC/sub
git clone git@github.com:netsec-ethz/scion-viz
cd scion-viz/python/web
pip3 install --user --require-hashes -r requirements.txt
python3 ./manage.py migrate
```

## 2. Source Update
This step applies to all uses Local and SCIONLab VM. Update the source for the `scion` and `scion-viz` repositories.
```shell
./scion.sh stop
./scion.sh clean
cd $SC
git pull
./scion.sh run
cd sub/scion-viz
git pull
```

## 3. Running
To start SCION-viz, you need to provide the local IP address and port number. Note that SCION-viz is automatically started in the SCIONLab VM environment.
```shell
cd $SC/sub/scion-viz/python/web
python3 ./manage.py runserver 10.0.2.15:8000
```

## 4. Using
The AS Visualization can be used from the [browser](/as_visualization/browser_asviz) or from the [command line](/as_visualization/command_asviz).
