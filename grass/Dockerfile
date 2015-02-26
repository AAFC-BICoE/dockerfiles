
FROM ubuntu:14.04
MAINTAINER Glen Newton <glen.newton@gmail.com>

RUN apt-get update
RUN apt-get upgrade -y

RUN apt-get install -y --force-yes grass-core  grass-dev grass-dev-doc grass-doc
RUN apt-get install -y --force-yes grass-gui

RUN apt-get install -y --force-yes x11-common x11-session-utils x11-utils x11-apps x11-xfs-utils x11-xserver-utils gnuplot-x11 x11vnc x11vnc-data

ENV TERM=xterm
CMD grass64


