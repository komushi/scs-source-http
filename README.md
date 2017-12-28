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
java -jar target/scs-source-http-1.0.0-RELEASE.jar --spring.cloud.stream.defaultBinder=rabbit
```

### 3-2. Test with scs-source-http and scs-processor-geocoding-reverse
```
java -jar target/scs-source-http-1.0.0-RELEASE.jar --spring.cloud.stream.defaultBinder=rabbit 

java -jar target/scs-processor-geocoding-reverse-1.0.0-RELEASE.jar --spring.cloud.stream.defaultBinder=rabbit --logging.level.info.cloudnative=TRACE --properties.mongo.hostName=localhost

curl -H "Content-Type: text/plain" -X POST -d'468244D1361B8A3EB8D206CC394BC9E9,BB899DFEA9CC964B50C540A1D685CCFB,2013-01-01 00:00:00,2013-01-01 00:04:00,240,1.71,139.752268,35.677043,139.771699,35.697283,CSH,6.50,0.50,0.50,0.00,0.00,7.50' 'http://localhost:9000'
```

### 3-3. Test with scs-source-http and scs-sink-gemfire
```
java -jar target/scs-source-http-1.0.0-RELEASE.jar --spring.cloud.stream.defaultBinder=rabbit --spring.cloud.stream.bindings.output.contentType=application/json

java -jar target/scs-sink-gemfire-1.0.0.RELEASE.jar --server.port=9200 --spring.cloud.stream.defaultBinder=rabbit --spring.cloud.stream.bindings.input.destination=http_raw --spring.cloud.stream.bindings.input.group=1 --spring.cloud.stream.bindings.input.contentType=application/json --spring.rabbitmq.host=localhost --spring.rabbitmq.port=5672 --gemfire.pool.connect-type=server --gemfire.pool.host-addresses=localhost:40404 --gemfire.region.region-name=RegRaw --gemfire.json=true --gemfire.key-expression="payload.getField('uuid')" 

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"aa9ff9bc-83cc-4c73-b9a0-316416a8f6ca","route":"1020020_1010140","timestamp":1514401078283,"pickupAddress":"中央区 京橋","dropoffAddress":"千代田区 千代田","pickupLatitude":35.677283,"pickupLongitude":139.771699,"dropoffLatitude":35.687043,"dropoffLongitude":139.752268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1020020","pickupBlock":"京橋","pickupDistrictCode":"102","pickupDistrict":"中央区","dropoffBlockCode":"1010140","dropoffBlock":"千代田","dropoffDistrictCode":"101","dropoffDistrict":"千代田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"b49f5648-4122-4fe0-beaa-d4ea9678b0ad","route":"1020020_1110580","timestamp":1514401082271,"pickupAddress":"中央区 京橋","dropoffAddress":"大田区 東海","pickupLatitude":35.677283,"pickupLongitude":139.771699,"dropoffLatitude":35.587043,"dropoffLongitude":139.752268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1020020","pickupBlock":"京橋","pickupDistrictCode":"102","pickupDistrict":"中央区","dropoffBlockCode":"1110580","dropoffBlock":"東海","dropoffDistrictCode":"111","dropoffDistrict":"大田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"cc76adfa-d628-4eb3-8ec5-8abc403d8c55","route":"1130310_1010140","timestamp":1514401078266,"pickupAddress":"渋谷区 千駄ケ谷","dropoffAddress":"千代田区 千代田","pickupLatitude":35.677283,"pickupLongitude":139.711699,"dropoffLatitude":35.687043,"dropoffLongitude":139.752268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1130310","pickupBlock":"千駄ケ谷","pickupDistrictCode":"113","pickupDistrict":"渋谷区","dropoffBlockCode":"1010140","dropoffBlock":"千代田","dropoffDistrictCode":"101","dropoffDistrict":"千代田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"df3b65c1-3115-4447-98d1-1b2a2339c193","route":"1130310_1110580","timestamp":1514401074272,"pickupAddress":"渋谷区 千駄ケ谷","dropoffAddress":"大田区 東海","pickupLatitude":35.677283,"pickupLongitude":139.711699,"dropoffLatitude":35.587043,"dropoffLongitude":139.772268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1130310","pickupBlock":"千駄ケ谷","pickupDistrictCode":"113","pickupDistrict":"渋谷区","dropoffBlockCode":"1110580","dropoffBlock":"東海","dropoffDistrictCode":"111","dropoffDistrict":"大田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"ea9ff9bc-83cc-4c73-b9a0-316416a8f6ca","route":"1020020_1010140","timestamp":1514401078283,"pickupAddress":"中央区 京橋","dropoffAddress":"千代田区 千代田","pickupLatitude":35.677283,"pickupLongitude":139.771699,"dropoffLatitude":35.687043,"dropoffLongitude":139.752268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1020020","pickupBlock":"京橋","pickupDistrictCode":"102","pickupDistrict":"中央区","dropoffBlockCode":"1010140","dropoffBlock":"千代田","dropoffDistrictCode":"101","dropoffDistrict":"千代田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"f49f5648-4122-4fe0-beaa-d4ea9678b0ad","route":"1020020_1110580","timestamp":1514401082271,"pickupAddress":"中央区 京橋","dropoffAddress":"大田区 東海","pickupLatitude":35.677283,"pickupLongitude":139.771699,"dropoffLatitude":35.587043,"dropoffLongitude":139.752268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1020020","pickupBlock":"京橋","pickupDistrictCode":"102","pickupDistrict":"中央区","dropoffBlockCode":"1110580","dropoffBlock":"東海","dropoffDistrictCode":"111","dropoffDistrict":"大田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"gc76adfa-d628-4eb3-8ec5-8abc403d8c55","route":"1130310_1010140","timestamp":1514401078266,"pickupAddress":"渋谷区 千駄ケ谷","dropoffAddress":"千代田区 千代田","pickupLatitude":35.677283,"pickupLongitude":139.711699,"dropoffLatitude":35.687043,"dropoffLongitude":139.752268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1130310","pickupBlock":"千駄ケ谷","pickupDistrictCode":"113","pickupDistrict":"渋谷区","dropoffBlockCode":"1010140","dropoffBlock":"千代田","dropoffDistrictCode":"101","dropoffDistrict":"千代田区"}' 'http://localhost:9000'

curl -H "Content-Type: application/json" -X POST -d'{"uuid":"hf3b65c1-3115-4447-98d1-1b2a2339c193","route":"1130310_1110580","timestamp":1514401074272,"pickupAddress":"渋谷区 千駄ケ谷","dropoffAddress":"大田区 東海","pickupLatitude":35.677283,"pickupLongitude":139.711699,"dropoffLatitude":35.587043,"dropoffLongitude":139.772268,"pickupDatetime":"2013-01-01 00:00:00","dropoffDatetime":"2013-01-01 00:04:00","pickupBlockCode":"1130310","pickupBlock":"千駄ケ谷","pickupDistrictCode":"113","pickupDistrict":"渋谷区","dropoffBlockCode":"1110580","dropoffBlock":"東海","dropoffDistrictCode":"111","dropoffDistrict":"大田区"}' 'http://localhost:9000'
```

# 4. Another deployment option - spring boot in docker

### 4-1. Local Execution on RabbitMQ
```
docker run -p 9000:9000 komushi/scs-source-http --spring.cloud.stream.defaultBinder=rabbit --spring.rabbitmq.host=docker.for.mac.localhost --spring.rabbitmq.port=5672

docker run komushi/scs-processor-geocoding-reverse --spring.cloud.stream.defaultBinder=rabbit --spring.rabbitmq.host=docker.for.mac.localhost --spring.rabbitmq.port=5672 --logging.level.info.cloudnative=TRACE
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

