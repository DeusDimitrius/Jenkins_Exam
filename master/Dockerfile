# Starting off with the Jenkins base Image
FROM jenkins/jenkins:latest

ENV REFRESHED_AT 2019-01–28

# Installing the plugins we need using the in-built install-plugins.sh script
RUN /usr/local/bin/install-plugins.sh git matrix-auth blueocean credentials-binding locale

# Setting up environment variables for Jenkins admin user and developer user
ENV JENKINS_USER admin
ENV JENKINS_PASS admin
ENV JENKINS_USER developer
ENV JENKINS_PASS developer

ARG http_port=80
ARG agent_port=50000

# Creating a volume where the Jenkins configuration will be stored
VOLUME /var/jenkins_home
