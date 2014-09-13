FROM rohanpm/centos:latest
MAINTAINER rohan@mcgovern.id.au

RUN useradd --home /home/tester --create-home tester
USER tester
COPY start /start
CMD ["/start"]