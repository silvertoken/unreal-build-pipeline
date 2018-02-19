FROM centos:7
MAINTAINER silvertoken <1569232+silvertoken@users.noreply.github.com>

# install nginx
RUN sed -i -e 's/enabled=1/enabled=0/g' /etc/yum/pluginconf.d/fastestmirror.conf && \
    rm -f /var/lib/rpm/__* && \
    rpm --rebuilddb -v -v && \
	yum clean all && \
    yum -y update && \
    yum -y install http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm && \
	yum -y makecache && \
	yum -y install nginx-1.12.2 && \
	yum clean all && \
	rm /etc/nginx/conf.d/default.conf
	#rm /etc/nginx/conf.d/example_ssl.conf

# copy our config files
COPY conf/jenkins.conf /etc/nginx/conf.d/jenkins.conf
COPY conf/nginx.conf /etc/nginx/nginx.conf

# expose proxy ports
EXPOSE 80

# start nginx
CMD ["nginx"]
