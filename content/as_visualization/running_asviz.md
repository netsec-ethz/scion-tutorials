# Setup and Run AS Visualizations


## 1. Setup Local
If you are using the ScionLab VM distribution you should skip this step. However, if you are installing the AS Visualization locally, you will need to clone a copy of the source.
```shell
cd $GOHOME/src/github.com/netsec-ethz/scion/sub
git clone git@github.com:netsec-ethz/scion-viz
cd scion-viz/python/web
pip3 install --user --require-hashes -r requirements.txt
python3 ./manage.py migrate
```

## 2. Source Update
This step applies to all uses Local and ScionLab VM. Update the source for the `scion` and `scion-viz` repositories.
```shell
./scion.sh stop
./scion.sh clean
cd $GOHOME/src/github.com/netsec-ethz/scion
git pull
./scion.sh run
cd sub/scion-viz
git pull
```

## 3. Running
```shell
cd $GOHOME/src/github.com/netsec-ethz/scion/sub/scion-viz/python/web
python3 ./manage.py runserver 10.0.2.15:8000
```

## 4. Using
The AS Visualization can be used from the [browser](/as_visualization/browser_asviz) or from the [command line](/as_visualization/command_asviz).
