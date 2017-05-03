
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

Apps sometimes store config as constants in the code. This is a violation of twelve-factor, which requires strict separation of config from code. Config varies substantially across deploys, code does not.

+++
