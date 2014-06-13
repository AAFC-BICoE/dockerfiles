There are two working Docker images--one for a storage node, and one for a proxy node.

GETTING DOCKER TO RUN ON A HOST
In order to get Docker to run properly on Ubuntu 14.04, several steps have to be taken.

First off, you need to get the Docker PPA and install the Docker package.
1) Download key: sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
2) Create file /etc/apt/sources.list.d/docker.list with contents "deb https://get.docker.io/ubuntu docker main"
3) Run apt-get update & then install lxc-docker

To configure the host system to properly run the Docker daemon, the following system configuration steps also need to be taken:
1) Comment out the "dns=dnsmasq" line in /etc/NetworkManager/NetworkManager.conf
2) In /etc/default/grub, change the GRUB_CMDLINE_LINUX line to: GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1". 
3) Run sudo update-grub and reboot.

SETTING UP AND RUNNING THE PROXY SERVER
The ring files, the proxy server configuration file, and the swift.conf file used in the proxy server image are all taken directly from the host filesystem when the image is built. That means that, at build time, those files must be available on the host, and placed in the same directory as the Dockerfile. Therefore, the first step is make sure that files account.ring.gz, container.ring.gz, object.ring.gz, proxy-server.conf, swift.conf, AS WELL AS the Dockerfile are in the same directory & are properly configured for the cluster that you intend the proxy server to govern.

Once that is verified, build the proxy server image, by executing following command: 
sudo docker build -t="swift-proxy" .
This command has to be executed from the directory that the proxy Dockerfile & config files are in, because of Docker's context requirements.

Docker may give out an error that the docker daemon is not running. If so, start it up with the command:
sudo docker -d &

The very first time this is built, it will take some time (probably about 5-10 minutes), but after that, re-builds to account for changed configuration files will be done in seconds, as when images are rebuilt, Docker will only commit incremental changes.

After it has been built, start up the proxy server container with the command:
sudo docker run -i -t --net=host --name="proxy" swift-proxy /bin/bash
Then, after it has run, type the command swift-init proxy start.

To escape from the container whilst keeping it running in the background, use the escape sequence CTRL+P CTRL+Q. To enter it again use the command:
sudo docker attach proxy

The docker container will have the same IP address as its host. This means that the port used by the proxy-server--80--must be open on the host, otherwise the proxy server will fail to start properly.

SETTING UP AND RUNNING THE STORAGE SERVER
Just like with the proxy server container, there are configuration files that need to be same directory as the Dockerfile. In the case of the storage server, they are account.ring.gz, container.ring.gz, object.ring.gz, rsyncd.conf, and swift.conf.

Once that is verified, build the proxy server image, by executing following command: 
sudo docker build -t="swift-proxy" .
Once again, this command has to be executed from the directory that the proxy Dockerfile & config files are in, because of Docker's context requirements.

In order for Docker to use whole filesystems from its host OS, those filesystems need to be mounted on the host OS, and they need to be passed to the container in the form of -v parameters.

The syntax for a -v parameter in Docker is this:
-v <HOST MOUNT POINT>:<CONTAINER MOUNT POINT>:rw
Where HOST MOUNT POINT is the mount directory of the disk in question on the host, and CONTAINER MOUNT POINT is the mount directory intended to be used in the container. In the Dockerfile, the /srv/node/ directory is setup for Swift to use, so ensure that CONTAINER MOUNT POINT is within that directory (for example, /srv/node/d1). Disks mounted anywhere else in the container will not be reachable by the proxy server.

Example: to run storage server with host disks mounted at /media/a, /media/b, and media/c as the three storage disks for the node:
sudo docker run -i -t --net=host --name="storage" -v /media/a:/srv/node/a:rw -v /media/b:/srv/node/a:rw -v /media/c:/srv/node/c swift-storage /bin/bash
Then, after it has run, type the command swift-init all start. It's normal for error messages to output saying the proxy-server service & object-expirer service could not start; neither are used for the storage nodes and so they are not configured in this container.

To escape from the container whilst keeping it running in the background, use the escape sequence CTRL+P CTRL+Q. To enter it again use the command:
sudo docker attach storage
