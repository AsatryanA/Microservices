# Table of Content

 - [What to do](#what-to-do)
 - [Sub-task 1: Service registry](#sub-task-1-service-registry)
 - [Sub-task 2: API Gateway](#sub-task-2-api-gateway)
 - [Sub-task 3: Service Configuration (Optional)](#sub-task-3-service-configuration-optional)

## What to do

In this task it is needed to choose one of Service registry tool and inject it into your infrastructure.
Sample implementation: [Eureka Example](https://www.javainuse.com/spring/cloud-gateway-eureka)

![](images/task.png)


## Sub-task 1: Service registry

1) Use Eureka Service Registry ([Service Registration and Discovery](https://spring.io/guides/gs/service-registration-and-discovery/)).
2) Update infrastructure of configuration to make sure that Service Registry is launched along with existing services (create your Eureka Server application)
3) Update clients using Eureka so that they will be called by service name instead of ip:port.
4) Implement Client-Side Balancing.

## Sub-task 2: API Gateway

1) Use API Gateway implementation - Spring Cloud Gateway.
2) Update infrastructure configuration to make sure that API Gateway is launched, exposed and there is a single-entry point in application.
3) Make sure that all services receive traffic from external clients through the API Gateway.
4) Properly handle errors (e.g., if service is not found or route doesn't exist).

## Sub-task 3: Service Configuration (Optional)

1) Create Git repository for storing all needed configuration at one place.
2) Create a service which will play the role of Service Configuration for other services.
3) Update infrastructure configuration by changing one (or more) services so to be able to communicate with Service Configuration.
4) Make sure that all service config clients receive configuration from Service Config.
5) Update service config client to be able to refresh configuration in case it was changed.

**Note.** In case there are issues with running all services on your local machine, for example, not enough CPU range or RAM, here are the following options:

 - use [docker limits](https://docs.docker.com/config/containers/resource_constraints/)
 - use any cloud free tier system if it’s possible
 - use [EPAM Cloud](https://kb.epam.com/display/EPMCITFAQ/Personal+Projects)  
