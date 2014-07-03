#Adapted from garland/docker-openstack-keystone
FROM dockerfile/ubuntu

#Install software needed for keystone
RUN apt-get update -y
RUN apt-get install keystone supervisor python-mysqldb -y

#Copy over configuration files for keystone
ADD supervisord-keystone.conf /etc/supervisor/conf.d/supervisord-keystone.conf
ADD keystone.conf /etc/keystone/keystone.conf
RUN keystone-manage db_sync

#Start up keystone service
RUN service supervisor restart

CMD ["/usr/bin/supervisord", "-n"]
