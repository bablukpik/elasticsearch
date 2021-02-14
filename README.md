## Using 3 Different Docker Images (Official Elastic Docker Images)

- Step 1: Setup Elasticsearch container and verify elastic its working
`docker run --rm -it -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" --net elasticsearch --name elasticsearch elasticsearch:7.10.1`
curl http://localhost:9200/​

- Step 2: Setup Kibana container and verify
`docker run --rm -d --name kibana --net elasticsearch -p 5601:5601 kibana:7.10.1`
http://localhost:5601​

- Step 3: Create logstash config file and use it to setup Logstash container
`docker run --name logstash --net elasticsearch -it --rm -v $(pwd):/config-dir docker.elastic.co/logstash/logstash:7.11.0 -f /config-dir/logstash.conf`

### logstash.conf
Standard user input from keyboard
 
    input { stdin { } }
    
    output {
      elasticsearch { hosts => ["elasticsearch:9200"] }
    }

### logstash2.conf 
The following format is like a real world example. Logstash can listen any port and can grab data out of it and then parse/filter it and finally send it to elasticsearch.

    input { 
      tcp { 
        port => 9500
      } 
    }
    
    filter {}
    
    output {
      elasticsearch { hosts => ["http://elasticsearch:9200"] }
    }


## Using 3 Different Docker Custom Images

### Logstash Dockerfile

First of all create an image using this command: `docker build -t "logstash:Dockerfile" .`

And then run a container from that image like this: `docker run --rm -it --name logstash --net elasticsearch logstash:Dockerfile`

### Dockerfile:

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


