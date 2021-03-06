# CEPH DAEMON IMAGE
# CEPH VERSION: Kraken
# CEPH VERSION DETAIL: 11.1.X

FROM openestuary/centos:v300
MAINTAINER Huang Jinhua "sjtuhjh@hotmail.com"

ENV CEPH_VERSION kraken
ENV CONFD_VERSION 0.10.0

# Install Ceph
RUN rpm --import 'https://download.ceph.com/keys/release.asc'
RUN rpm -Uvh http://download.ceph.com/rpm-${CEPH_VERSION}/el7/noarch/ceph-release-1-1.el7.noarch.rpm
RUN yum install -y epel-release && yum clean all
ARG PACKAGES="unzip ceph-mon ceph-osd ceph-mds ceph-mgr ceph-base ceph-common ceph-radosgw rbd-mirror device-mapper sharutils etcd kubernetes-client"
RUN yum install -y $PACKAGES && rpm -q $PACKAGES && yum clean all

# Install confd
#ADD https://github.com/kelseyhightower/confd/releases/download/v${CONFD_VERSION}/confd-${CONFD_VERSION}-linux-amd64 /usr/local/bin/confd
#confd will be builted locally
ADD confd_bin /usr/local/bin/confd 
RUN chmod +x /usr/local/bin/confd && mkdir -p /etc/confd/conf.d && mkdir -p /etc/confd/templates

# Download forego
#ADD https://bin.equinox.io/c/ekMN3bCZFUn/forego-stable-linux-amd64.tgz /forego.tgz
ADD forego /usr/local/bin/forego
RUN chmod +x /usr/local/bin/forego

# Add bootstrap script, ceph defaults key/values for KV store
ADD *.sh ceph.defaults check_zombie_mons.py ./osd_scenarios/* entrypoint.sh.in disabled_scenario /

# Modify the entrypoint
RUN chmod +x /generate_entrypoint.sh && \
  bash -c "/generate_entrypoint.sh" && \
  rm -f /generate_entrypoint.sh

# Add templates for confd
ADD ./confd/templates/* /etc/confd/templates/
ADD ./confd/conf.d/* /etc/confd/conf.d/

# Add volumes for Ceph config and data
VOLUME ["/etc/ceph","/var/lib/ceph", "/etc/ganesha"]

# Execute the entrypoint
WORKDIR /
ENTRYPOINT ["/entrypoint.sh"]
