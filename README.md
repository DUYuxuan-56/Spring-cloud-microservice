# microservice based on Spring Cloud

This is a microservice project based on Spring Boot、Spring Cloud、Spring Oauth2 and Spring Cloud Netflix.

# Tech Stack
* Spring boot 
* Eureka 
* Spring Cloud Config 
* Hystrix 
* Zuul 
* Spring Cloud Bus 
* Spring Cloud Sleuth 
* Ribbon 
* Turbine 
* Spring Cloud Stream 
* Feign 
* Spring Cloud OAuth2 

# Architecture

this project includes 8 services

* registry - service registry
* config - configuration
* monitor - monitoring
* zipkin - distributed tracking
* gateway - gateway for microservices
* auth-service - OAuth2 
* svca-service - service A
* svcb-service - service B

![architecture](/screenshots/architecture.jpg)
## components
![components](/screenshots/components.jpg)

# Start Project

* Docker to start
    1. config Docker env
    2. `mvn clean package` to package project and docker image
    3. execute `docker-compose up -d` to start all services

# preview

## registry center
visit http://localhost:8761/ account user，pw password

![registry](/screenshots/registry.jpg)
## monitoring
access http://localhost:8040/ account admin，pw admin
### control panel
![monitor](/screenshots/monitor1.jpg)
### registry history
![monitor](/screenshots/monitor2.jpg)
### Turbine Hystrix panel
![monitor](/screenshots/monitor3.jpg)
### health conditions and garbage collections
![monitor](/screenshots/monitor4.jpg)
### counter
![monitor](/screenshots/monitor5.jpg)
### Logback level
![monitor](/screenshots/monitor7.jpg)
### use JMX
![monitor](/screenshots/monitor8.jpg)
### inspect threads
![monitor](/screenshots/monitor9.jpg)
### auth history
![monitor](/screenshots/monitor10.jpg)
### inspect Http requests trace
![monitor](/screenshots/monitor11.jpg)
### Hystrix panel
![monitor](/screenshots/monitor12.jpg)
## tracing
access http://localhost:9411/ account admin，pw admin
### control panel of zipkin
![zipkin](/screenshots/zipkin1.jpg)
### tracing details
![zipkin](/screenshots/zipkin2.jpg)
### service dependency
![zipkin](/screenshots/zipkin3.jpg)
## RabbitMQ monitor
Docker start http://localhost:15673/ account guest，pw guest（local: rabbit default port:15672）

![rabbit](/screenshots/rabbit.jpg)
# Testing
1. get Token
```
curl -X POST -vu client:secret http://localhost:8060/uaa/oauth/token -H "Accept: application/json" -d "password=password&username=anil&grant_type=password&scope=read%20write"
```
returned：
```
{
  "access_token": "eac56504-c4f0-4706-b72e-3dc3acdf45e9",
  "token_type": "bearer",
  "refresh_token": "da1007dc-683c-4309-965d-370b15aa4aeb",
  "expires_in": 3599,
  "scope": "read write"
}
```
2. access token to access service a 
```
curl -i -H "Authorization: Bearer eac56504-c4f0-4706-b72e-3dc3acdf45e9" http://localhost:8060/svca
```
returned：
```
svca-service (172.18.0.8:8080)===>name:d9du
svcb-service (172.18.0.2:8070)===>Say Hello
```
3. access token to access service b 
```
curl -i -H "Authorization: Bearer eac56504-c4f0-4706-b72e-3dc3acdf45e9" http://localhost:8060/svcb
```
returned：
```
svcb-service (172.18.0.2:8070)===>Say Hello
```
4. use refresh token 
```
curl -X POST -vu client:secret http://localhost:8060/uaa/oauth/token -H "Accept: application/json" -d "grant_type=refresh_token&refresh_token=da1007dc-683c-4309-965d-370b15aa4aeb"
```
return new Token：
```
{
  "access_token": "63ff57ce-f140-482e-ba7e-b6f29df35c88",
  "token_type": "bearer",
  "refresh_token": "da1007dc-683c-4309-965d-370b15aa4aeb",
  "expires_in": 3599,
  "scope": "read write"
}
```
5. refresh config
```
curl -X POST -vu user:password http://localhost:8888/bus/refresh
```