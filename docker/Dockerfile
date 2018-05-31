FROM openjdk:8-jdk-alpine
MAINTAINER Venu Rachakonda <venu.rachakonda@rackspace.com>

WORKDIR /opt/demo/

ADD ["./target/ARTIFACT","/opt/demo/webapps/"]
VOLUME ["/opt/demo/","/opt/demo/"]

EXPOSE 8081

CMD ["java","-jar","/opt/demo/webapps/ARTIFACT","--server.port=8081"]
