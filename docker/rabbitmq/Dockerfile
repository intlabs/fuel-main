FROM centos

MAINTAINER Aleksandr Didenko adidenko@mirantis.com

WORKDIR /root

RUN rm -rf /etc/yum.repos.d/*
RUN echo -e "[nailgun]\nname=Nailgun Local Repo\nbaseurl=http://$(route -n | awk '/^0.0.0.0/ { print $2 }'):_PORT_/os/x86_64/\ngpgcheck=0" > /etc/yum.repos.d/nailgun.repo
RUN yum clean all
RUN yum --quiet install -y ruby21-puppet rabbitmq-server

ADD etc /etc

RUN mkdir -p /var/lib/hiera && touch /var/lib/hiera/common.yaml; mkdir -p /var/log/rabbitmq
RUN /usr/bin/puppet apply -v /etc/puppet/modules/nailgun/examples/rabbitmq-only.pp

ADD start.sh /usr/local/bin/start.sh
RUN chmod +x /usr/local/bin/start.sh

RUN echo -e "[nailgun]\nname=Nailgun Local Repo\nbaseurl=file:/var/www/nailgun/centos/fuelweb/x86_64\ngpgcheck=0" > /etc/yum.repos.d/nailgun.repo
RUN yum clean all

EXPOSE 4369
EXPOSE 5672
EXPOSE 15672
EXPOSE 61613

CMD ["/usr/local/bin/start.sh"]
