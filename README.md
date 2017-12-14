# scs-source-http
## Based on [Spring Cloud Stream](https://cloud.spring.io/spring-cloud-stream/) 
# Option 1. Local Deployment as Spring Cloud Stream Application

## 1-1. Prerequisites for Local Deployment

* [Homebrew](http://brew.sh/) if you use Mac OS X and prefer homebrew otherwise you need to install/start all the middleware components manually.
* [Homebrew Services](https://github.com/Homebrew/homebrew-services)
* [Maven](http://brewformulas.org/Maven)
* [RabbitMQ](http://brewformulas.org/Rabbitmq)
* [Kafka](http://brewformulas.org/Kafka) is an alternative for messaging.
* [Kinesis] is the default messaging choice.

## 1-2. Test with Local Deployment
### 1-2-1. Download and Build
```
$ git clone https://github.com/komushi/scs-source-http.git
$ cd scs-source-http
$ mvn clean package
```

### 1-2-2. Start RabbitMQ
```
brew services start rabbitmq
```

### 1-2-3. Start scs-source-http
```
java -jar target/scs-source-http-1.0.0-RELEASE.jar
```

### 1-2-4. Post a message

```
curl -H "Content-Type: text/plain" -X POST -d'468244D1361B8A3EB8D206CC394BC9E9,BB899DFEA9CC964B50C540A1D685CCFB,2013-01-01 00:00:00,2013-01-01 00:04:00,240,1.71,139.752268,35.677043,139.771699,35.697283,CSH,6.50,0.50,0.50,0.00,0.00,7.50' 'http://localhost:9000'
```

# Option 2. Another deployment option - spring boot in docker

## 2-1. Download and Build
### 2-1-1. assembly.xml under src/main/docker
```
<assembly
        xmlns="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/plugins/maven-assembly-plugin/assembly/1.1.2 http://maven.apache.org/xsd/assembly-1.1.2.xsd">
    <id>scs-source-http</id>
    <dependencySets>
        <dependencySet>
            <includes>
                <include>info.cloudnative:scs-source-http</include>
            </includes>
            <outputDirectory>.</outputDirectory>
            <outputFileNameMapping>scs-source-http.jar</outputFileNameMapping>
        </dependencySet>
    </dependencySets>
</assembly>
```

### 2-1-2. Modify docker-maven-plugin inside pom.xml
Under plugin/configuration/images/image/build/, modify entryPoint like below.
```
<entryPoint>
  <exec>
    <arg>java</arg>
    <arg>-jar</arg>
    <arg>/maven/${project.name}.jar</arg>
  </exec>
</entryPoint>
```

### 2-1-3. Build docker image by maven
```
mvn clean package docker:build
```

if necessay push to docker hub

```
mvn docker:push -Ddocker.username=[provide your username] -Ddocker.password=[provide password]
```

## 2-2. Test with HTTP Source

```
docker run -p <host_port>:<container_port> komushi/scs-source-http <app_args>
```

### 2-2-1. Start docker containers

```
docker run -p 9000:9000 komushi/scs-source-http --spring.cloud.stream.bindings.output.destination=http_raw --spring.rabbitmq.host=docker.for.mac.localhost --spring.rabbitmq.port=5672 --spring.rabbitmq.username=guest --spring.rabbitmq.password=guest
```

### 2-2-2. Post a message

```
curl -H "Content-Type: text/plain" -X POST -d'468244D1361B8A3EB8D206CC394BC9E9,BB899DFEA9CC964B50C540A1D685CCFB,2013-01-01 00:00:00,2013-01-01 00:04:00,240,1.71,139.752268,35.677043,139.771699,35.697283,CSH,6.50,0.50,0.50,0.00,0.00,7.50' 'http://localhost:9000'
```

