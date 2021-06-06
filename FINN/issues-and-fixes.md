# Issues and Fixes

- Issue

Got permission denied while trying to connect to the Docker daemon socket

- Fix

Follow [this guide](https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket). Make sure you have been added to the docker group. You must be logged out and logged back in after adding to the docker group.

- Issue

You get the error ```start () got an unexpected keyword argument 'port'``` when trying to visualize in Netron. 

- Fix

The fix is in the development branch. For now, what you can do is:

In ```finn/src/finn/util/visualization.py``` replace ```netron.start(model_filename, port=8081, host="0.0.0.0")``` with ```netron.start(model_filename, address=("0.0.0.0", 8081))```
 
 - Issue

![plot](error/parameter_error.png)

- Fix

Replace 
```raw_i = get_data("finn", "data/onnx/mnist-conv/test_data_set_0/input_0.pb")```

with

```raw_i = open('/workspace/finn-base/src/finn/data/onnx/mnist-conv/test_data_set_0/input_0.pb','rb').read()```

- Issue

If you try to run the docker container you get the message:

```bash
docker: Error response from daemon: driver failed programming external connectivity on endpoint dreamy_ellis (7ffe77f92e06d01fd2d390bfc9d5294dab8c73afa94f7b2c23b70ed61338735b): Bind for 0.0.0.0:8888 failed: port is already allocated.
```

- Fix

Remove the docker container by running

```bash
docker container ls #this will give the docker container ID
docker rm -f <docker container ID>
```
