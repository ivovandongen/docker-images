## Build the image

`docker build -t qt-neptune3 .`

## Creating the ccache volume
from  http://frungy.org/docker/using-ccache-with-docker

`docker create -v /usr/local/ccache-docker:/ccache --name ccache ubuntu:18.04`

## Run

- `open -a XQuartz`
- `IP=$(ifconfig en0 | grep inet | awk '$1=="inet" {print $2}')`
- `xhost + $IP`
- `docker run -e CCACHE_DIR=/ccache --volumes-from ccache -e DISPLAY=$IP:0 -v /tmp/.X11-unix:/tmp/.X11-unix -it qt-neptune3`
- `cd install/neptune3-ui/neptune3 && ./neptune3-ui --start-session-dbus -r`
