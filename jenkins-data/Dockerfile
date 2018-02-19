FROM centos:7
MAINTAINER silvertoken <1569232+silvertoken@users.noreply.github.com>

# user arguments	
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000

# SET Jenkins Environment Variables
ENV JENKINS_HOME /var/jenkins_home

RUN sed -i -e 's/enabled=1/enabled=0/g' /etc/yum/pluginconf.d/fastestmirror.conf && \
    rm -f /var/lib/rpm/__* && \
    rpm --rebuilddb -v -v && \
	yum clean all && \
    yum -y update && \
	groupadd -g ${gid} ${group} && \
    useradd -d "$JENKINS_HOME" -u ${uid} -g ${gid} -m -s /bin/bash ${user} && \
    mkdir /var/log/jenkins && \
    chown -R ${user}:${group} /var/log/jenkins

#COPY jenkinsexport.tar.gz /tmp/jenkinsexport.tar.gz
#COPY jenkinsdefaultsecrets.tar.gz /tmp/jenkinsdefaultsecrets.tar.gz
#RUN tar -xvf /tmp/jenkinsexport.tar.gz 
#RUN tar -xvf /tmp/jenkinsdefaultsecrets.tar.gz 
#RUN rm /tmp/jenkinsexport.tar.gz 
#RUN rm /tmp/jenkinsdefaultsecrets.tar.gz
#RUN chown -R jenkins:jenkins /var/jenkins_home

# add log volume
VOLUME ["/var/log/jenkins", "${JENKINS_HOME}"]

# switch to jenkins user
USER jenkins

# output to the console
CMD ["echo", "Data container for Jenkins"]