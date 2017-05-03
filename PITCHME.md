
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
