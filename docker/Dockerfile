FROM openjdk:8-jdk-alpine
MAINTAINER Venu Rachakonda <venu.rachakonda@rackspace.com>

WORKDIR /opt/demo/

ADD ["./target/spring-boot-demo*jar","/opt/demo/webapps/"]
VOLUME ["/opt/demo/","/opt/demo/"]


COPY start.sh /sbin/start.sh
RUN chmod 755 /sbin/start.sh

EXPOSE 8081
ENTRYPOINT ["/sbin/start.sh"]