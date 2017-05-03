
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

```file
application.name=a-bootiful-client
```
```yaml
application:
    name: a-bootiful-client
```
+++

### External configuration by file with Spring Boot




