# To build:
#   $ docker build -t jefferyb/install-openshift .
#
FROM centos
MAINTAINER Jeffery Bagirimvano

RUN \
    yum install -y epel-release && \
    yum install -y ansible pyOpenSSL git python-passlib httpd-tools sos psacct openssl-devel java-1.8.0-openjdk-headless && \
    mkdir /root/.ssh

ADD config /root/.ssh/
# cert used to ssh to your servers
ADD key.pem /root/.ssh/id_rsa
ADD hosts /etc/ansible/hosts
ADD ansible.cfg /etc/ansible/ansible.cfg

VOLUME /playbooks
VOLUME /root/.ssh
VOLUME /etc/ansible

WORKDIR /playbooks

