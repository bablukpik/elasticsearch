#We are using 'docker.elastic.co/logstash/logstash:7.11.0' as our base image for logstash
FROM logstash:7.7.0
MAINTAINER Bablu Ahmed <bablukpik@gmail.com>
WORKDIR /app
USER root

RUN yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
RUN yum install wget -y
RUN wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.16.tar.gz \
  && tar -xzf mysql-connector-java-8.0.16.tar.gz \
  && cp mysql-connector-java-8.0.16/mysql-connector-java-8.0.16.jar /usr/local/bin/mysql-connector.jar \
  && mv mysql-connector-java-8.0.16/mysql-connector-java-8.0.16.jar /usr/share/logstash/logstash-core/lib/jars/mysql-connector-java-8.0.16.jar \
  && rm -rf mysql-connector-java-8.0.16.tar.gz mysql-connector-java-8.0.16

ADD ./config/pipelines.yml /usr/share/logstash/config/pipelines.yml
ADD ./config/logstash.yml /usr/share/logstash/config/logstash.yml
ADD ./pipelines ./pipelines
ADD ./statement ./statement
USER logstash
