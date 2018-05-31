FROM openjdk:8-jdk-alpine
MAINTAINER Venu Rachakonda <venu.rachakonda@rackspace.com>

WORKDIR /opt/demo/

ARG appname
ENV artifact ${appname}

ADD ["./target/${artifact}","/opt/demo/webapps/"]
VOLUME ["/opt/demo/","/opt/demo/"]

EXPOSE 8081

CMD ["java","-jar","/opt/demo/webapps/${artifact}","--server.port=8081"]
