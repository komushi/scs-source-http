# scs-source-http
# I. Quick Start Steps 
## 1. Prerequisite

* [Homebrew](http://brew.sh/) if you use Mac OS X and prefer homebrew otherwise you need to install/start all the middleware components manually.
* [Homebrew Services](https://github.com/Homebrew/homebrew-services)
* [Maven](http://brewformulas.org/Maven)
* [RabbitMQ](http://brewformulas.org/Rabbitmq)
* [MongoDB](http://brewformulas.org/Mongodb)
* [Kafka](http://brewformulas.org/Kafka) is an alternative for messaging.
* [scs-source-http](https://github.com/komushi/scs-source-http)

### 1-1. Start RabbitMQ
```
brew services start rabbitmq
```


## 2. Download and Build

```
git clone https://github.com/komushi/scs-source-http.git
cd scs-source-http
mvn clean package docker:build
docker push komushi/scs-source-http
```

## 3. Run in Java CLI
### 3-1. Command to start jar
```
java -jar target/scs-source-http-1.0.0-RELEASE.jar --spring.cloud.stream.defaultBinder=<rabbit/kinesis/kafka>
```

### 3-2. Test with scs-source-http and scs-processor-geocoding-reverse
```
java -jar target/scs-source-http-1.0.0-RELEASE.jar

java -jar target/scs-processor-geocoding-reverse-1.0.0-RELEASE.jar --logging.level.info.cloudnative=TRACE

curl -H "Content-Type: text/plain" -X POST -d'468244D1361B8A3EB8D206CC394BC9E9,BB899DFEA9CC964B50C540A1D685CCFB,2013-01-01 00:00:00,2013-01-01 00:04:00,240,1.71,139.752268,35.677043,139.771699,35.697283,CSH,6.50,0.50,0.50,0.00,0.00,7.50' 'http://localhost:9000'
```

# 4. Another deployment option - spring boot in docker

### 4-1. Local Execution on RabbitMQ
```
docker run -p 9000:9000 komushi/scs-source-http --spring.rabbitmq.host=docker.for.mac.localhost

docker run komushi/scs-processor-geocoding-reverse --spring.rabbitmq.host=docker.for.mac.localhost --properties.mongo.hostName=docker.for.mac.localhost --logging.level.info.cloudnative=TRACE
```

### 4-2. Deploy on AWS
```
docker run komushi/scs-source-http --spring.cloud.stream.defaultBinder=kinesis --cloud.aws.region.static=ap-northeast-1
```

# II. Appendix
### Default Parameters - application.properties
```
server.port=9000
spring.cloud.stream.defaultBinder=kinesis
spring.cloud.stream.default.contentType=text/plain
spring.cloud.stream.bindings.output.destination=http_raw
spring.cloud.stream.bindings.output.producer.partitionKeyExpression=1
cloud.aws.region.static=us-east-1
```

