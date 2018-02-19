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
	openssh-server \
	java-1.8.0-openjdk \
	git && \
	yum clean all && \
	/usr/bin/ssh-keygen -A && \
	sed -ri 's/^session\s+required\s+pam_loginuid.so$/session optional pam_loginuid.so/' /etc/pam.d/sshd

# user arguments	
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000

# Jenkins is run with user `jenkins`, uid = 1000
# If you bind mount a volume from the host or a data container,
# ensure you use the same uid

RUN groupadd -g ${gid} ${group} && \
    useradd -u ${uid} -g ${gid} -m -s /bin/bash ${user} && \
	mkdir /home/${user}/.ssh
	
COPY /files/authorized_keys /home/${user}/.ssh/authorized_keys

RUN chown -R ${user}:${group} /home/${user} && \
    chmod 600 /home/${user}/.ssh/authorized_keys && \
    chmod 700 /home/${user}/.ssh && \
	echo "${user} ALL=(ALL) ALL"

	
EXPOSE 22
CMD ["/usr/sbin/sshd","-D"]