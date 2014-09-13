FROM rohanpm/gerrit:latest
MAINTAINER rohan@mcgovern.id.au
RUN yum -y install httpd git && yum clean all
RUN git config --file /var/lib/gerrit/etc/gerrit.config auth.type HTTP
RUN git config --file /var/lib/gerrit/etc/gerrit.config --unset auth.httpHeader