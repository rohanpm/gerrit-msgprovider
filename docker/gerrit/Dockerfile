FROM rohanpm/javabase:latest
MAINTAINER rohan@mcgovern.id.au

RUN curl https://gerrit-releases.storage.googleapis.com/gerrit-2.9.1.war -o /usr/local/libexec/gerrit.war
RUN echo 0bc3781eaa98914b51fa77f58e0ec5ff79b3db0e86b55cca4422a65087d4b564  /usr/local/libexec/gerrit.war | sha256sum --check

RUN yum -y install git && yum clean all

RUN useradd --system --home /var/lib/gerrit --create-home gerrit
USER gerrit
RUN java -jar /usr/local/libexec/gerrit.war init --batch -d /var/lib/gerrit

RUN git config --file /var/lib/gerrit/etc/gerrit.config auth.type HTTP
RUN git config --file /var/lib/gerrit/etc/gerrit.config gerrit.canonicalWebUrl http://gerrit:8080/

COPY start /start

EXPOSE 29418 8080

VOLUME [ \
  "/var/lib/gerrit/logs", \
  "/var/lib/gerrit/git", \
  "/var/lib/gerrit/db" \
]

CMD ["/start"]