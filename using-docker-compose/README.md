# Elasticsearch with Logstash, Kibana (ELK Stack)

## Using 3 Different Docker Images (Official Elastic Docker Images)

- Step 1: Setup Elasticsearch container and verify elastic its working
  
`docker run --rm -it -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" --net elasticsearch --name elasticsearch elasticsearch:7.10.1`

    curl http://localhost:9200/​

- Step 2: Setup Kibana container and verify

`docker run --rm -d --name kibana --net elasticsearch -p 5601:5601 kibana:7.10.1`
  
    http://localhost:5601​

- Step 3: Create logstash config file and use it to setup Logstash container

Let's change the directory first to logstash by using `cd logstash`

`docker run --name logstash --net elasticsearch -it --rm -v $(pwd):/config-dir docker.elastic.co/logstash/logstash:7.11.0 -f /config-dir/logstash.conf`

### logstash.conf
Standard user input from terminal. Try writing something from your keyboard in the terminal once writing hit enter when the logstash container is ready and then discover it in the Kibana by creating an index like this `logstash-*`.
 
    input { stdin { } }
    
    output {
      elasticsearch { hosts => ["elasticsearch:9200"] }
    }

### logstash2.conf 
The following format is like a real world example. This might look like in your case. Logstash can listen any port and can grab data out of it and then parse/filter it and finally send it to elasticsearch.

    input { 
      tcp { 
        port => 9500
      } 
    }
    
    filter {}
    
    output {
      elasticsearch { hosts => ["http://elasticsearch:9200"] }
    }


## Using 3 Different Docker Custom Images (Using 3 Different Dockerfiles)

### Logstash Dockerfile

Let's change the directory first to logstash by using `cd logstash` and then create an image using this command: `docker build -t "logstash:Dockerfile" .`

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


