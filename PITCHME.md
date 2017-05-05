
# Spring Cloud Services


---

## Agenda

* Overview Spring Cloud Services
* Config Server
* Eureka Service Registry
* Hystrix
* Spring Boot vs Spring Cloud (Services)
* PCF Dev adjustments

---

## Sources

 * docs.cloudfoundry.org
 * twitter
 * slideshare
 * github
 * https://12factor.net

---

# Config Server

+++

### Review twelve-factor app

# III. Config

+++

### Store config in the environment

An app’s config is everything that is likely to vary between deploys (staging, production, developer environments, etc). This includes:

- Resource handles to the database, Memcached, and other backing services
- Credentials to external services such as Amazon S3 or Twitter
- Per-deploy values such as the canonical hostname for the deploy

+++

## Apps sometimes store config as constants in the code. This is a violation of twelve-factor, which requires strict separation of config from code. Config varies substantially across deploys, code does not.

+++

### External configuration by file

- Can be injected through a properties file
- Prior to Spring Boot the config file had to be referenced

```java
@PropertySource("file:/path/to/simple.properties")
public class Application {
```

```java
    @Value("${spring.application.name}")
    private String appName;
```
+++

### External configuration by file

- Properties can be set with a default value, which will be picked in case property is not found or set
- In case no default is defined and the property can't be found the app won't start

```java
    @Value("${spring.application.name: Default Name}")
    private String appName;
```
```
2017-05-03 13:55:26.342 [..] Exception encountered during context initialization - cancelling refresh attempt: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'messageRestController': Injection of autowired dependencies failed; nested exception is java.lang.IllegalArgumentException: Could not resolve placeholder 'spring.application.name' in value "${spring.application.name}"
```
+++

### External configuration by file with Spring Boot

- Spring Boot will read the properties in `src/main/resources/application.properties` by default
- Spring Boot allows you to use yaml files instead of properties-
- Both examples will work

```yaml
application.name=a-bootiful-client
```
```yaml
application:
    name: a-bootiful-client
```
+++

### External configuration by file with Spring Boot- POJO

```java
@Component
@ConfigurationProperties("application")
class ApplicationProperties{

	private String name;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

}
```

+++

### External configuration by file with Spring Boot- POJO Invocation

```java
@RestController
class MessageRestController {

    @Autowired
    private ApplicationProperties ap;

    @RequestMapping("/appNameViaPOJO")
    String appNameViaPOJO() {
        return this.ap.getName();
    }
}
```

+++

# Remember Actuator?

+++

### Point the browser to: http://localhost:8080/configprops

```json
"applicationProperties": {
  "prefix": "application",
  "properties": {
    "name": "a-bootiful-client"
  }
},
```

+++

### Limitations of current approach

- restart required to change the property files
- no traceability of changes
- de-centralized configuration
- no ootb security/encryption mechanism

+++

# Config Server 
### to address limitations

+++

- provides dynamic properties to the clients during runtime
- requires a git repository (online or file based)
- serves as proxy for configuration keys and values 
- http://cloud.spring.io/spring-cloud-config/

+++

![Config_Server](https://docs.pivotal.io/spring-cloud-services/1-0/images/config-server/config-server-fig1.png)

+++

## Java

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {

	public static void main(String[] args) {
		SpringApplication.run(ConfigServerApplication.class, args);
	}
}
```
+++

## application.yml

```yaml
server:
  port: 8888

spring:
  cloud:
    config:
      server:
        git :
          uri: https://github.com/maeddes/config-server
```

+++

Embed

https://github.com/maeddes/config-server

+++

## Validation
http://localhost:8888/config-client/master

```json
{
  "name": "config-client",
  "profiles": [
    "master"
  ],
  "label": null,
  "version": null,
  "state": null,
  "propertySources": [
    {
      "name": "https://github.com/maeddes/config-server/config-client.properties",
      "source": {
        "config.property": "43"
      }
    }
  ]
}
```
+++

# Config client

+++

## Java

```java
@RestController
@RefreshScope
class ConfigServerRestController{
	
	@Value("${config.property: Default}")
	private String configProperty;

	@RequestMapping("/valueFromServer")
	String valueFromServer() {
		return this.configProperty;
	}
	
	
}
```

+++

## bootstrap.yml

```yaml
spring:
  application:
    name: config-client
    cloud:
      config:
        uri: http://localhost:8888
```
+++

## Updates

- To make a runtime change you need to update it in the git repo first
- You can validate with the config server URL, if it has been picked up
- The clients will not update automatically
- It requires a POST action to the http://127.0.0.1:8080/refresh endpoint

```yaml
mhs@R2-D2:~$ curl -d{} http://127.0.0.1:8080/refresh
["config.property"]mhs@R2-D2:~$ 
```

+++

Cloud Service Bus

---

# Service Registry

+++

### Review Microservices

- Microservice are meant to run independent
- Mulitple services are allowed to run distributed and in parallel
- To enable communication the services need to know how to find each other
- Manual configuration can be a hell of a task

+++ 

### Alternative Service Discovery and Registry

- 

+++

### High - Level Architecture

![Eureka](https://docs.pivotal.io/spring-cloud-services/1-2/images/service-registry/Netflix-Eureka-d1.png)
(javaworld.com)

+++

### Eureka Server

+++

### Java

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@EnableEurekaServer
@SpringBootApplication
public class ServiceEurekaApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceEurekaApplication.class, args);
	}
}
```

+++

### application.yml

```yaml
server:
  port: 8761

eureka:
  client:
    registerWithEureka: false
    fetchRegistry: false
```
+++

(image goes here)

+++

# start.spring.io

-

+++

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class MhsEurekaServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(MhsEurekaServiceApplication.class, args);
	}
}
```

### Eureka Service

+++

# Eureka Client

- Both service provider and service consumer register at Eureka server as client
- The server will register them by the property spring.application.name

+++

### Eureka client

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class MhsEurekaServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(MhsEurekaServiceApplication.class, args);
	}
}

...
}
```

+++

### Exposed service

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;


@RestController
class ConfigFileRestController {
	
    @RequestMapping("/service")
    String service() {
        return "Service exposed to Eureka";
    }
}
```

+++

### bootstrap.yml

```yaml
spring:
  application:
    name: mhs-service
```

+++

### application.yml

```yaml
server:
  port: 0
```

- The service will be bound to a random port
- Port information will not be exposed to client directly
- Application is supposed to be lookup up by id only

+++

# Service Consumer

+++

### Java Header part

```java

import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
...

@EnableDiscoveryClient
@SpringBootApplication
public class MhsEurekaConsumerApplication {

	public static void main(String[] args) {
		SpringApplication.run(MhsEurekaConsumerApplication.class, args);
	}
}
```

+++

### Java functional part

```java
@RestController
class ServiceInstanceRestController{

    @Autowired
    private DiscoveryClient discoveryClient;

    @RequestMapping("/serviceList")
    public String serviceList(){
            
        return this.discoveryClient.getServices().toString();
    }

    @RequestMapping("/service-instances/{applicationName}")
    public List<ServiceInstance> serviceInstancesByApplicationName(
            @PathVariable String applicationName) {
        return this.discoveryClient.getInstances(applicationName);
    }
    
}
```

+++

### bootstrap.yml

```yaml
spring:
  application:
    name: service-consumer
```

- can also be empty or non-existent
- client does not need to ne looked up
- better style still ;-)

+++

# Feign

+++




---

# Reading List

+++

- https://spring.io/blog/2015/01/20/microservice-registration-and-discovery-with-spring-cloud-and-netflix-s-eureka
- https://docs.pivotal.io/spring-cloud-services/1-3/common/service-registry/writing-client-applications.html
- https://spring.io/guides/gs/service-registration-and-discovery/
- https://www.slideshare.net/AndreasFalk2/cloud-foundry-meetup-stuttgart-2017-spring-cloud-development
- https://www.innoq.com/de/articles/2016/12/microservices-a-la-netflix/
- http://cloud.spring.io/spring-cloud-netflix/
- http://callistaenterprise.se/blogg/teknik/2015/04/10/building-microservices-with-spring-cloud-and-netflix-oss-part-1/
- http://cloud.spring.io/spring-cloud-netflix/spring-cloud-netflix.html
- http://stackoverflow.com/questions/31976236/whats-the-difference-between-enableeurekaclient-and-enablediscoveryclient
- http://www.baeldung.com/spring-cloud-netflix-eureka
- http://www.baeldung.com/intro-to-feign
