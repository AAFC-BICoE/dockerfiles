FROM ubuntu
RUN apt-get -y update
RUN apt-get -y install wget
RUN wget http://ftp.us.debian.org/debian/pool/main/o/openssl/libssl0.9.8_0.9.8o-4squeeze14_amd64.deb
RUN dpkg -i libssl0.9.8_0.9.8o-4squeeze14_amd64.deb
RUN apt-get -y install super openssl python openssh-server vim net-tools libcurl4-gnutls-dev help2man libpcre3-dev libxml2-dev
RUN wget ftp://ftp.renci.org/pub/irods/releases/4.0.0rc2/irods-resource-4.0.0rc2-64bit.deb
RUN dpkg -i irods-resource-4.0.0rc2-64bit.deb
#set paths
RUN echo "export PATH=\$PATH:$IRODS_HOME:$IRODS_HOME/clients/icommands/bin" >>$IRODS_HOME/../.bashrc
RUN chown irods:irods $IRODS_HOME/../.bashrc
#visible ports and sshd
EXPOSE 1247 22
CMD /usr/sbin/sshd -D
RUN bash -c 'echo "su irods -c /var/lib/irods/packaging/setup_database.sh" >> /.bashrc'
