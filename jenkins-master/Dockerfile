FROM centos:7
MAINTAINER silvertoken <1569232+silvertoken@users.noreply.github.com>

# install base packages
RUN sed -i -e 's/enabled=1/enabled=0/g' /etc/yum/pluginconf.d/fastestmirror.conf && \
    rm -f /var/lib/rpm/__* && \
    rpm --rebuilddb -v -v && \
	yum clean all && \
    yum -y update && \
	yum -v install -y \
	wget \
	zip \
	openssh-client \
	unzip \
	java-1.8.0-openjdk \
	java-1.8.0-openjdk-devel \
	git && \
	yum clean all

# jenkins arguments	
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000
ARG http_port=8080
ARG agent_port=50000
ARG TINI_VERSION=v0.16.1

# jenkins version being bundled in this docker image
ARG JENKINS_VERSION
ENV JENKINS_VERSION ${JENKINS_VERSION:-2.89.4}
ENV JENKINS_OPTS="--logfile=/var/log/jenkins/jenkins.log  --webroot=/var/cache/jenkins/war"
ARG JENKINS_URL=https://repo.jenkins-ci.org/public/org/jenkins-ci/main/jenkins-war/${JENKINS_VERSION}/jenkins-war-${JENKINS_VERSION}.war

# SET Jenkins Environment Variables
ENV JENKINS_HOME /var/jenkins_home
ENV JENKINS_SLAVE_AGENT_PORT ${agent_port}
ENV JENKINS_VERSION 2.89.3
ENV JENKINS_UC https://updates.jenkins-ci.org
ENV COPY_REFERENCE_FILE_LOG $JENKINS_HOME/copy_reference_file.log

# Jenkins is run with user `jenkins`, uid = 1000
# If you bind mount a volume from the host or a data container,
# ensure you use the same uid
COPY tini_pub.gpg /var/jenkins_home/tini_pub.gpg
RUN groupadd -g ${gid} ${group} && \
    useradd -d "$JENKINS_HOME" -u ${uid} -g ${gid} -m -s /bin/bash ${user} && \
	mkdir /var/log/jenkins && \
	mkdir /var/cache/jenkins && \
    chown -R ${user}:${group} /var/log/jenkins && \
	chown -R ${user}:${group} /var/cache/jenkins && \
	mkdir -p /usr/share/jenkins/ref/init.groovy.d && \
    curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-static-amd64 -o /sbin/tini && \
    curl -fsSL https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini-static-amd64.asc -o /sbin/tini.asc && \
	gpg --import /var/jenkins_home/tini_pub.gpg && \
	gpg --verify /sbin/tini.asc && \
	rm -rf /sbin/tini.asc /root/.gnupg && \
	chmod +x /sbin/tini && \
	curl -fsSL ${JENKINS_URL} -o /usr/share/jenkins/jenkins.war && \
	chown -R ${user} "$JENKINS_HOME" /usr/share/jenkins/ref

# add log volume
VOLUME ["/var/log/jenkins", "/var/jenkins_home"]

# Expose Ports for web and slave agents
EXPOSE ${http_port}
EXPOSE ${agent_port}


# Copy in local config files
COPY init.groovy /usr/share/jenkins/ref/init.groovy.d/tcp-slave-agent-port.groovy
COPY jenkins.sh /usr/local/bin/jenkins.sh
COPY jenkins-support /usr/local/bin/jenkins-support
COPY install-plugins.sh /usr/local/bin/install-plugins.sh
COPY plugins.txt /tmp/plugins.txt

RUN chmod +x /usr/local/bin/install-plugins.sh && \
    chmod +x /usr/local/bin/jenkins.sh
	
RUN xargs /usr/local/bin/install-plugins.sh < /tmp/plugins.txt

USER ${user}

# Tini as the entry point to manage zombie processes
ENTRYPOINT ["/sbin/tini", "--", "/usr/local/bin/jenkins.sh"]