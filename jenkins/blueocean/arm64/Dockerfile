FROM jenkins4eval/jenkins:slim-arm64

USER root

# Add the docker binary so running Docker commands from the master works nicely
RUN apt update
RUN apt install -y apt-transport-https ca-certificates curl gnupg2 software-properties-common
RUN curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
RUN add-apt-repository \
    "deb [arch=arm64] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli

# RUN usermod -a -G docker jenkins
USER jenkins

RUN install-plugins.sh antisamy-markup-formatter matrix-auth blueocean:1.24.6
