# fuel-cobbler
#
# Version     0.1

FROM centos
MAINTAINER Matthew Mosesohn mmosesohn@mirantis.com

WORKDIR /root

RUN rm -rf /etc/yum.repos.d/*
RUN echo -e "[nailgun]\nname=Nailgun Local Repo\nbaseurl=http://$(route -n | awk '/^0.0.0.0/ { print $2 }'):_PORT_/os/x86_64/\ngpgcheck=0" > /etc/yum.repos.d/nailgun.repo
RUN yum clean all
RUN yum --quiet install -y ruby21-puppet
RUN yum --quiet install -y httpd cobbler dnsmasq xinetd tftp-server

ADD etc /etc
#Workaround for dnsmasq startup
RUN echo -e "NETWORKING=yes\nHOSTNAME=$HOSTNAME" > /etc/sysconfig/network
#FIXME workaround for ssh key
RUN mkdir -p /root/.ssh; chmod 700 /root/.ssh; touch /root/.ssh/id_rsa.pub

RUN /etc/init.d/httpd start && puppet apply -v /etc/puppet/modules/nailgun/examples/cobbler-only.pp

RUN mkdir -p /usr/local/bin
ADD start.sh /usr/local/bin/start.sh
RUN chmod +x /usr/local/bin/start.sh
RUN ln -s /etc/dnsmasq.conf /etc/cobbler.dnsmasq.conf

RUN echo -e "[nailgun]\nname=Nailgun Local Repo\nbaseurl=file:/var/www/nailgun/centos/fuelweb/x86_64\ngpgcheck=0" > /etc/yum.repos.d/nailgun.repo
RUN yum clean all

EXPOSE 67
EXPOSE 69
EXPOSE 80
EXPOSE 443
VOLUME /etc/cobbler
CMD /usr/local/bin/start.sh
