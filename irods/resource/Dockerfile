from ubuntu
run apt-get -y update
run apt-get -y install wget
run wget http://ftp.us.debian.org/debian/pool/main/o/openssl/libssl0.9.8_0.9.8o-4squeeze14_amd64.deb
run dpkg -i libssl0.9.8_0.9.8o-4squeeze14_amd64.deb
run apt-get -y install super openssl python openssh-server vim net-tools libcurl4-gnutls-dev help2man libpcre3-dev libxml2-dev
run wget ftp://ftp.renci.org/pub/irods/releases/4.0.0rc2/irods-resource-4.0.0rc2-64bit.deb
run dpkg -i irods-resource-4.0.0rc2-64bit.deb
#set paths
run echo "export PATH=\$PATH:$IRODS_HOME:$IRODS_HOME/clients/icommands/bin" >>$IRODS_HOME/../.bashrc
run chown irods:irods $IRODS_HOME/../.bashrc
#visible ports and sshd
expose 1247 22
cmd /usr/sbin/sshd -D
#
#after install, login with ssh 
#  su - irods
#  ./packaging/setup_resource.sh
