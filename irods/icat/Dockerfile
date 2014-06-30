FROM ubuntu
RUN apt-get -y update
RUN apt-get -y install wget
RUN wget http://ftp.us.debian.org/debian/pool/main/o/openssl/libssl0.9.8_0.9.8o-4squeeze14_amd64.deb
RUN dpkg -i libssl0.9.8_0.9.8o-4squeeze14_amd64.deb
RUN apt-get -y install postgresql libodbc1 odbc-postgresql odbcinst odbcinst1debian2 super unixodbc openssl python openssh-server vim net-tools libcurl4-gnutls-dev help2man libpcre3-dev libxml2-dev
RUN wget ftp://ftp.renci.org/pub/irods/releases/4.0.0rc2/irods-icat-4.0.0rc2-64bit.deb
RUN wget ftp://ftp.renci.org/pub/irods/releases/4.0.0rc2/irods-database-plugin-postgres-1.0.deb
RUN dpkg -i irods-icat-4.0.0rc2-64bit.deb irods-database-plugin-postgres-1.0.deb
##
##postgresql database initialization
RUN /etc/init.d/postgresql start
RUN echo "ALTER user postgres with encrypted password 'blarg';" >/var/lib/postgresql/tmp001
RUN echo "CREATE ROLE irods ENCRYPTED PASSWORD 'md55e58ffabcd64778c5752d12ca68ed870' SUPERUSER CREATEDB CREATEROLE INHERIT LOGIN;" >>/var/lib/postgresql/tmp001
RUN echo "CREATE DATABASE ICAT;" >>/var/lib/postgresql/tmp001
RUN echo "GRANT ALL PRIVILEGES ON DATABASE ICAT TO irods;" >>/var/lib/postgresql/tmp001
RUN echo "/etc/init.d/postgresql start" >/var/lib/postgresql/tmp002
RUN echo "su -c 'psql -f /var/lib/postgresql/tmp001' postgres" >>/var/lib/postgresql/tmp002
RUN echo "/etc/init.d/postgresql restart" >>/var/lib/postgresql/tmp002
RUN chmod a+x /var/lib/postgresql/tmp002 
RUN /var/lib/postgresql/tmp002
##
##add a delay in the irods setup to give odbc extra time to connect
RUN sed -i -e "s/# Create the tables/sleep(6);# Create the tables/" /var/lib/irods/iRODS/scripts/perl/irods_setup.pl
##
##flag setup script to use stored values such as DATABASE_HOST 
RUN su -c 'mkdir /tmp/irods' - irods
RUN su -c 'touch /tmp/irods/setup_database.flag' - irods
RUN sed -i -e "s/^\\\\$DATABASE_HOST =.*$/\\\\$DATABASE_HOST = 'localhost';/" /etc/irods/irods.config
##
##init paths
RUN echo "export PATH=\$PATH:$IRODS_HOME:$IRODS_HOME/clients/icommands/bin" >>$IRODS_HOME/../.bashrc
RUN chown irods:irods $IRODS_HOME/../.bashrc
##
##visible ports and ssh service
EXPOSE 1247 22
CMD /usr/sbin/sshd -D
#Spin up the server
RUN bash -c 'echo "service postgresql start" >> /.bashrc'
RUN bash -c 'echo "su irods -c /var/lib/irods/packaging/setup_database.sh" >> /.bashrc'
