FROM jenkins/jenkins:2.283-alpine

USER root
RUN apk add --update git docker
USER jenkins

COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt