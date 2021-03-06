# Docker container images with Pytorch and Xfce4 VNC Desktop.

This repository developed from ConSol/docker-headless-vnc-container, with provide the headless VNC environments for docker container


## Spec
This is a Docker environmentalist equipped with pytorch and no-vnc(http vnc service).
The container is developed under xfce-docker-container source and add the ROS, TensorFlow GPU environment on top of it, to provide a essential kit for anyone who develop with robotic and deep learning.

## Usage
- Run command with mapping to local port `5901` (vnc protocol) and `6901` (vnc web access):

      docker run -d -p 5901:5901 -p 6901:6901 dl_vnc:dev

- If you want to use GPU in container:

      docker run --gpus all -d -p 5901:5901 -p 6901:6901 dl_vnc:dev

- If you want to get into the container use interactive mode `-it` and `bash`
      
      docker run -it -p 5901:5901 -p 6901:6901 dl_vnc:dev bash

- If you want to connect to tensorboard, run command with mapping to local port `6006`:
      
      docker run -it -p 5901:5901 -p 6901:6901 -p 6006:6006 dl_vnc:dev

- Build an image from scratch:

      docker build -t dl_vnc:dev .

- Build an image with script:

      ./build.sh
    


## Connect & Control
If the container runs up, you can connect to the container throught the following 
* connect via __VNC viewer `localhost:5901`__, default password: `vncpassword`
* connect via __noVNC HTML5 full client__: [`http://localhost:6901/vnc.html`](http://localhost:6901/vnc.html), default password: `vncpassword` 
* connect via __noVNC HTML5 lite client__: [`http://localhost:6901/?password=vncpassword`](http://localhost:6901/?password=vncpassword) 
* connect to __Tensorboard__ if you do the tensorboard mapping above: [`http://localhost:6006`](http://localhost:6006)
* The default username and password in container is ros:ros

## Detail Environment setting

#### 1.1) Using root (user id `0`)
Add the `--user` flag to your docker run command:

    docker run -it --user root -p 5901:5901 dl_vnc:dev

#### 1.2) Using user and group id of host system
Add the `--user` flag to your docker run command (Note: uid and gui of host system may not able to map with container, which is 1000:1000. If that is the case, check with 3):

    docker run -it -p 5901:5901 --user $(id -u):$(id -g) dl_vnc:dev

### 2) Override VNC and Container environment variables
The following VNC environment variables can be overwritten at the `docker run` phase to customize your desktop environment inside the container:
* `VNC_COL_DEPTH`, default: `24`
* `VNC_RESOLUTION`, default: `1920x1080`
* `VNC_PW`, default: `vncpassword`
* `USER`, default: `ros`
* `PASSWD`, default: `ros`

#### 2.1) Example: Override the VNC password
Simply overwrite the value of the environment variable `VNC_PW`. For example in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_PW=vncpassword dl_vnc:dev

#### 2.2) Example: Override the VNC resolution
Simply overwrite the value of the environment variable `VNC_RESOLUTION`. For example in
the docker run command:

    docker run -it -p 5901:5901 -p 6901:6901 -e VNC_RESOLUTION=800x600 dl_vnc:dev

### 3) Mounting local directory to conatiner
You should run with following environment variable in order to mapping host user/group with container, and retrieve R/W permission of mounting directory in container (Note: after running this command, the user account in container will be same as host account):

      docker run -it -p 5901:5901 \
        --user $(id -u):$(id -g) \
        --volume /etc/passwd:/etc/passwd \
        --volume /etc/group:/etc/group \
        --volume /etc/shadow:/etc/shadow \
        --volume /home/ros/Desktop:/home/ros/Desktop:rw \
        dl_vnc:dev

### 4) Connecting jupyter notebook within container
- Run command with mapping to local port `8888` (jupyter protocol) and `8888` (host web access):

      docker run -d -p 8888:8888 dl_vnc:dev

- Check your local IP within container using `` $ifconfig``, then you can start up jupyter notebook in container with following command: 

      jupyter notebook --ip={YOUR CONTAINER IP} --port=8888 --allow-root

- After start up the jupyter kernel, you can access the notebook from host browser through HTTP service.

      http://localhost:8888/

## Contributors

* [ConSol/docker-headless-vnc-container](https://github.com/ConSol/docker-headless-vnc-container) - developed the ConSol/docker-headless-vnc-container