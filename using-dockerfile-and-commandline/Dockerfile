#We are using 'docker.elastic.co/logstash/logstash:7.11.0' as our base image
FROM docker.elastic.co/logstash/logstash:7.11.0
MAINTAINER Bablu Ahmed <bablukpik@gmail.com>

#Volume for logstash Configuration
VOLUME ["/config-dir"]

#Add Default elasticsearch configuration file
ADD logstash.conf /config-dir/logstash.conf 

#Define working directory
WORKDIR /config-dir

#Define command to run
CMD ["-f","/config-dir/logstash.conf"]
