FROM rohanpm/centos:latest
MAINTAINER rohan@mcgovern.id.au
RUN yum -y install httpd && yum clean all
COPY gerrit-proxy.conf /etc/httpd/conf.d/gerrit-proxy.conf
COPY start /start
EXPOSE 80
CMD ["/start"]