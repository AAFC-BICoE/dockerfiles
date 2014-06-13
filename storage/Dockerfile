#This Dockerfile sets up a container to act as a swift storage server

# BUILD INSTRUCTIONS
#
# Imports rsyncd.conf, swift.conf, and ring files from the host.
# THIS IMAGE MUST BE REBUILT WHENEVER ANY CHANGE TO THE ABOVE FILES HAPPENS (such as when disks are added/removed)
# See readme for more details

# RUN INSTRUCTIONS
#
# Host networking mode is needed for this container to work, so the --net=host parameter must be used when starting up an instance of this container
# Swift storage containers need to be able to access the host disks directly. By default, this is disabled in Docker.
# Because of this, instances of this container need to be started with -v parameters (this allows direct disks access)
# Example: if this storage node is to use disks mounted at /media/a and /media/b on the host, run a new instance with the command: 
# "sudo docker run -i -t --net=host -v /media/a:/srv/node/a:rw -v /media/b:/srv/node/a:rw swift-storage /bin/bash" 
# See readme for more details

FROM ubuntu
CMD /bin/bash

#Install swift & dependencies
RUN apt-get update
RUN apt-get install -y gcc bzr python-configobj python-coverage python-dev python-nose python-setuptools python-simplejson python-xattr python-webob python-eventlet python-greenlet debhelper python-sphinx python-all python-openssl python-pastedeploy python-netifaces bzr-builddeb xfsprogs memcached git python-setuptools vim rsync swift python-swiftclient python-software-properties
WORKDIR /opt
RUN git clone http://github.com/openstack/swift.git
WORKDIR /opt/swift
RUN easy_install pip
RUN python setup.py install
RUN mkdir -p /etc/swift
WORKDIR /opt/swift/etc
RUN cp drive-audit.conf-sample /etc/swift/drive-audit.conf

#Copy over rings & conf
ADD account-server.conf /etc/swift/account-server.conf
ADD container-server.conf /etc/swift/container-server.conf
ADD object-server.conf /etc/swift/object-server.conf
ADD account.ring.gz /etc/swift/account.ring.gz
ADD container.ring.gz /etc/swift/container.ring.gz
ADD object.ring.gz /etc/swift/object.ring.gz
ADD swift.conf /etc/swift/swift.conf

#Copy over & configure rsync
ADD rsyncd.conf /etc/rsyncd.conf
RUN perl -pi -e 's/RSYNC_ENABLE=false/RSYNC_ENABLE=true/' /etc/default/rsync
RUN service rsync restart

#Configure logging
RUN echo 'local0.* /var/log/swift/all.log' > /etc/rsyslog.d/0-swift.conf
RUN service rsyslog restart

#Runtime directories & permissions that swift needs
RUN mkdir /var/log/swift
RUN mkdir /var/run/swift
RUN chown swift:swift /var/run/swift
RUN chown -R syslog.adm /var/log/swift
RUN chown -R swift:swift /etc/swift

#Make storage directories
RUN mkdir /srv/node
RUN chown -R swift:swift /srv/node

#Ensure Swift is installed at startup
RUN bash -c 'echo "swift-init all start" >> /.bashrc'
