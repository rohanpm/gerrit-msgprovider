FROM rohanpm/javabase:latest
MAINTAINER rohan@mcgovern.id.au
RUN curl https://gerrit-releases.storage.googleapis.com/gerrit-2.9.1.war -o /usr/local/libexec/gerrit.war
RUN echo 0bc3781eaa98914b51fa77f58e0ec5ff79b3db0e86b55cca4422a65087d4b564  /usr/local/libexec/gerrit.war | sha256sum --check
RUN java -jar /usr/local/libexec/gerrit.war init --batch -d /var/lib/gerrit
EXPOSE 29418 8080
VOLUME [ \
  "/var/lib/gerrit/logs", \
  "/var/lib/gerrit/git", \
  "/var/lib/gerrit/db" \
]
CMD ["/usr/bin/java", "-jar", "/var/lib/gerrit/bin/gerrit.war", "daemon", "-d", "/var/lib/gerrit"]