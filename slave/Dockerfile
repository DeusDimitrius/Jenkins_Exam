FROM picoded/ubuntu-openjdk-8-jdk

MAINTAINER Dmitry Balashov <dbalash0w@yandex.ru>

# Install tools:
RUN \
    apt-get update \
   && apt-get install --no-install-recommends -y \
    openssh-server \
    git \
    python-pip \
    ansible \
   && rm -rf /var/lib/apt/lists/* 

# Upgrade pip and setuptols:
RUN \
    apt-get update \
   && apt-get install --no-install-recommends -y \
    python3-pip \
    python-dev \
    build-essential \
    python3-venv \
   && rm -rf /var/lib/apt/lists/* \
   && pip install --upgrade setuptools
   
# Install Ansible
RUN pip install --upgrade ansible

# Install docker:
RUN \
    apt-get update \
   && apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg2 \
    software-properties-common \
   && curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey \
   && add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
    $(lsb_release -cs) stable" \
   && apt-get update \
   && apt-get -y install docker-ce  

# Not works
# VOLUME /var/run/docker.sock:/var/run/docker.sock

# Install user jenkins
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000
ARG JENKINS_AGENT_HOME=/home/${user}

ENV JENKINS_AGENT_HOME ${JENKINS_AGENT_HOME}

# Jenkins is run with user `jenkins`, uid = 1000
# If you bind mount a volume from the host or a data container,ensure you use the same uid
RUN \
    groupadd -g ${gid} ${group} \
   && useradd -d "${JENKINS_AGENT_HOME}" -u ${uid} -g ${gid} -m -s /bin/bash ${user} \
   && usermod -a -G docker jenkins \
   && usermod -a -G root jenkins

# setup SSH server
RUN sed -i /etc/ssh/sshd_config \
        -e 's/#PermitRootLogin.*/PermitRootLogin no/' \
        -e 's/#RSAAuthentication.*/RSAAuthentication yes/'  \
        -e 's/#PasswordAuthentication.*/PasswordAuthentication no/' \
        -e 's/#SyslogFacility.*/SyslogFacility AUTH/' \
        -e 's/#LogLevel.*/LogLevel INFO/' && \
mkdir /var/run/sshd

# Add public key for Jenkins login
RUN \
    mkdir /home/jenkins/.ssh \
   && echo 'ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDKQX5SnDfLe7gxoK5oNNUDOeFaeGwLkE9Oek3QPfJlT3yUIOkVd4HWKtD+DSFV/8uZeO77I7KFkfh/SjVibSIZ7+Fa4qRqUEwPyd6K5iwSdlvRqASxbriWR/Kb+1Q5qk6+hljJE6G9z24QpQmh0+LB4PWJ+pA1E5icR1csJMgugsdGNMJvfs9H1KhYTULDME95RBrZ8JYxZd1Eb0GmEm7AopscXCjCDSgvNVoQmaOlQ0uPFuk3FVfCNEmNTl646TtjNBv02MfSEqjELjIQcu+okKysPkWkZLGgxCjncaQzAt515pUPvadJy81zrDEOQcFJRGNU1y1uFDc7ZiX5UCnX user@MacBook-Air-User.local' >> /home/jenkins/.ssh/authorized_keys \
   && cp /home/jenkins/.ssh/authorized_keys /home/jenkins/.ssh/slave_rsa.pub

VOLUME "${JENKINS_AGENT_HOME}" "/tmp" "/run" "/var/run"

WORKDIR "${JENKINS_AGENT_HOME}"

# Expose the standard SSH port
EXPOSE 22

# Run SSHD
ENTRYPOINT exec /usr/sbin/sshd -D -e "${@}"


