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

## Convert to virtualbox image

```
docker export cdae169d8f3e > export.tar # replace container id from docker container list
dd if=/dev/zero of=neptune3.img bs=1 count=0 seek=20g

# Start docker container to make the raw image
docker run --privileged --rm -it --name tmp_ubuntu -v "$(pwd)":/app ubuntu:18.04

# On Docker image
cd /app
mkfs.ext2 -F neptune3.img
mount -o loop neptune3.img /mnt
tar xf export.tar -C /mnt/
sudo umount /mnt
exit

# Back on mac
qemu-img convert -f raw -O vmdk neptune3.img neptune3.vmdk
```
