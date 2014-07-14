#Dockerfile to spin up a Horizon instance

#Setup system
FROM ubuntu
RUN apt-get update
RUN apt-get upgrade -y

#Install packages
RUN apt-get install -y apache2 memcached libapache2-mod-wsgi openstack-dashboard

#This package has to be removed for the web UI to work correctly
RUN apt-get remove -y openstack-dashboard-ubuntu-theme

#Ensure web server starts when container is spun up
RUN bash -c 'echo "service apache2 start" >> /.bashrc'
