FROM centos
MAINTAINER Davidge

ENV VERSION=8.5.43

RUN yum install java-1.8.0-openjdk wget curl unzip iproute net-tools -y && \
    yum clean all && \
    rm -rf /var/cache/yum/*

RUN wget https://www-eu.apache.org/dist/tomcat/tomcat-8/v8.5.43/bin/apache-tomcat-8.5.43.tar.gz  && \
    tar zxf apache-tomcat-${VERSION}.tar.gz && \
    mv apache-tomcat-${VERSION} /usr/local/tomcat && \
    rm -rf apache-tomcat-${VERSION}.tar.gz /usr/local/tomcat/webapps/* && \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

ENV PATH $PATH:/usr/local/tomcat/bin

WORKDIR /usr/local/tomcat/bin

EXPOSE 8080
CMD ["catalina.sh","run"]
