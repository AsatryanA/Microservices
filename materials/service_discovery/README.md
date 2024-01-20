# Table of Content

 - [Service Discovery in a Microservices Architecture](#service-discovery-in-a-microservices-architecture)
 - [Service Registration and Discovery](#service-registration-and-discovery)
 - [Client-Side Load-Balancing with Spring Cloud LoadBalancer](#client-side-load-balancing-with-spring-cloud-loadbalancer)
 - [Spring Cloud Gateway](#spring-cloud-gateway)
 - [Pattern: Client-side service discovery](#pattern-client-side-service-discovery)
 - [Pattern: Server-side service discovery](#pattern-server-side-service-discovery)
 - [Backends for Frontends pattern](#backends-for-frontends-pattern)
 - [Microservices — Centralized Configuration with Spring Cloud](#microservices--centralized-configuration-with-spring-cloud)
 - [Related reading](#related-reading)
 - [Questions](#questions)
 
# Service Discovery in a Microservices Architecture

## Why Use Service Discovery?

Let’s imagine that you are writing some code that invokes a service that has a REST API or Thrift API. In order to make a request, your code needs to know the network location (IP address and port) of a service instance. In a traditional application running on physical hardware, the network locations of service instances are relatively static. For example, your code can read the network locations from a configuration file that is occasionally updated.

In a modern, cloud‑based microservices application, however, this is a much more difficult problem to solve as shown in the following diagram.

![](images/Richardson-microservices-part4-1_difficult-service-discovery.png)

Service instances have dynamically assigned network locations. Moreover, the set of service instances changes dynamically because of autoscaling, failures, and upgrades. Consequently, your client code needs to use a more elaborate service discovery mechanism.

There are two main service discovery patterns: client‑side discovery and server‑side discovery. Let’s first look at client‑side discovery.

## The Client‑Side Discovery Pattern

When using client‑side discovery, the client is responsible for determining the network locations of available service instances and load balancing requests across them. The client queries a service registry, which is a database of available service instances. The client then uses a load‑balancing algorithm to select one of the available service instances and makes a request.

The following diagram shows the structure of this pattern.

![](images/Richardson-microservices-part4-2_client-side-pattern.png)

The network location of a service instance is registered with the service registry when it starts up. It is removed from the service registry when the instance terminates. The service instance’s registration is typically refreshed periodically using a heartbeat mechanism.

[Netflix OSS](https://netflix.github.io/) provides a great example of the client‑side discovery pattern. [Netflix Eureka](https://github.com/Netflix/eureka) is a service registry. It provides a REST API for managing service‑instance registration and for querying available instances. [Netflix Ribbon](https://github.com/Netflix/ribbon) is an IPC client that works with Eureka to load balance requests across the available service instances. We will discuss Eureka in more depth later in this article.

The client‑side discovery pattern has a variety of benefits and drawbacks. This pattern is relatively straightforward and, except for the service registry, there are no other moving parts. Also, since the client knows about the available services instances, it can make intelligent, application‑specific load‑balancing decisions such as using hashing consistently. One significant drawback of this pattern is that it couples the client with the service registry. You must implement client‑side service discovery logic for each programming language and framework used by your service clients.

Now that we have looked at client‑side discovery, let’s take a look at server‑side discovery.

## The Server‑Side Discovery Pattern

The other approach to service discovery is the [server-side discovery pattern](https://microservices.io/patterns/server-side-discovery.html). The following diagram shows the structure of this pattern.

![](images/Richardson-microservices-part4-3_server-side-pattern.png)

The client makes a request to a service via a load balancer. The load balancer queries the service registry and routes each request to an available service instance. As with client‑side discovery, service instances are registered and deregistered with the service registry.

The [AWS Elastic Load Balancer](https://aws.amazon.com/ru/elasticloadbalancing/) (ELB) is an example of a server-side discovery router. An ELB is commonly used to load balance external traffic from the Internet. However, you can also use an ELB to load balance traffic that is internal to a virtual private cloud (VPC). A client makes requests (HTTP or TCP) via the ELB using its DNS name. The ELB load balances the traffic among a set of registered Elastic Compute Cloud (EC2) instances or EC2 Container Service (ECS) containers. There isn’t a separate service registry. Instead, EC2 instances and ECS containers are registered with the ELB itself.

HTTP servers and load balancers such as [NGINX Plus](https://www.nginx.com/products/) and NGINX can also be used as a server-side discovery load balancer. For example, this [blog post](https://www.airpair.com/scalable-architecture-with-docker-consul-and-nginx) describes using [Consul Template](https://github.com/hashicorp/consul-template) to dynamically reconfigure NGINX reverse proxying. Consul Template is a tool that periodically regenerates arbitrary configuration files from configuration data stored in the [Consul service registry](https://www.consul.io/). It runs an arbitrary shell command whenever the files change. In the example described by the blog post, Consul Template generates an `nginx.conf` file, which configures the reverse proxying, and then runs a command that tells NGINX to reload the configuration. A more sophisticated implementation could dynamically reconfigure NGINX Plus using either [its HTTP API or DNS](https://www.nginx.com/products/nginx/load-balancing/#load-balancing-api).

Some deployment environments such as [Kubernetes](https://github.com/kubernetes/kubernetes/) and [Marathon](https://mesosphere.github.io/marathon/docs/service-discovery-load-balancing.html) run a proxy on each host in the cluster. The proxy plays the role of a server‑side discovery load balancer. In order to make a request to a service, a client routes the request via the proxy using the host’s IP address and the service’s assigned port. The proxy then transparently forwards the request to an available service instance running somewhere in the cluster.

The server‑side discovery pattern has several benefits and drawbacks. One great benefit of this pattern is that details of discovery are abstracted away from the client. Clients simply make requests to the load balancer. This eliminates the need to implement discovery logic for each programming language and framework used by your service clients. Also, as mentioned above, some deployment environments provide this functionality for free. This pattern also has some drawbacks, however. Unless the load balancer is provided by the deployment environment, it is yet another highly available system component that you need to set up and manage.

## The Service Registry

The [service registry](https://microservices.io/patterns/service-registry.html) is a key part of service discovery. It is a database containing the network locations of service instances. A service registry needs to be highly available and up to date. Clients can cache network locations obtained from the service registry. However, that information eventually becomes out of date and clients become unable to discover service instances. Consequently, a service registry consists of a cluster of servers that use a replication protocol to maintain consistency.

As mentioned earlier, [Netflix Eureka](https://github.com/Netflix/eureka) is good example of a service registry. It provides a REST API for registering and querying service instances. A service instance registers its network location using a POST request. Every 30 seconds it must refresh its registration using a PUT request. A registration is removed by either using an HTTP DELETE request or by the instance registration timing out. As you might expect, a client can retrieve the registered service instances by using an HTTP GET request.

[Netflix achieves high availability](https://github.com/Netflix/eureka/wiki/) by running one or more Eureka servers in each Amazon EC2 availability zone. Each Eureka server runs on an EC2 instance that has an Elastic IP address. DNS TEXT records are used to store the Eureka cluster configuration, which is a map from availability zones to a list of the network locations of Eureka servers. When a Eureka server starts up, it queries DNS to retrieve the Eureka cluster configuration, locates its peers, and assigns itself an unused Elastic IP address.

Eureka clients – services and service clients – query DNS to discover the network locations of Eureka servers. Clients prefer to use a Eureka server in the same availability zone. However, if none is available, the client uses a Eureka server in another availability zone.

Other examples of service registries include:

 - [etcd](https://github.com/etcd-io/etcd) – A highly available, distributed, consistent, key‑value store that is used for shared configuration and service discovery. Two notable projects that use etcd are Kubernetes and [Cloud Foundry](https://pivotal.io/platform).
 - [consul](https://www.consul.io/) – A tool for discovering and configuring services. It provides an API that allows clients to register and discover services. Consul can perform health checks to determine service availability.
 - [Apache Zookeeper](https://zookeeper.apache.org/) – A widely used, high‑performance coordination service for distributed applications. Apache Zookeeper was originally a subproject of Hadoop but is now a top‑level project.

Also, as noted previously, some systems such as Kubernetes, Marathon, and AWS do not have an explicit service registry. Instead, the service registry is just a built‑in part of the infrastructure.

Now that we have looked at the concept of a service registry, let’s look at how service instances are registered with the service registry.

## Service Registration Options
    
As previously mentioned, service instances must be registered with and deregistered from the service registry. There are a couple of different ways to handle the registration and deregistration. One option is for service instances to register themselves, the [self‑registration pattern](https://microservices.io/patterns/self-registration.html). The other option is for some other system component to manage the registration of service instances, the [third‑party registration pattern](https://microservices.io/patterns/3rd-party-registration.html). Let’s first look at the self‑registration pattern.

## The Self‑Registration Pattern

When using the [self‑registration pattern](https://microservices.io/patterns/self-registration.html), a service instance is responsible for registering and deregistering itself with the service registry. Also, if required, a service instance sends heartbeat requests to prevent its registration from expiring. The following diagram shows the structure of this pattern.

![](images/Richardson-microservices-part4-4_self-registration-pattern.png)

A good example of this approach is the [Netflix OSS Eureka client](https://github.com/Netflix/eureka). The Eureka client handles all aspects of service instance registration and deregistration. The [Spring Cloud project](https://spring.io/projects/spring-cloud), which implements various patterns including service discovery, makes it easy to automatically register a service instance with Eureka. You simply annotate your Java Configuration class with an @EnableEurekaClient annotation.

The self‑registration pattern has various benefits and drawbacks. One benefit is that it is relatively simple and doesn’t require any other system components. However, a major drawback is that it couples the service instances to the service registry. You must implement the registration code in each programming language and framework used by your services.

The alternative approach, which decouples services from the service registry, is the third‑party registration pattern.

## The Third‑Party Registration Pattern

When using the [third-party registration pattern](https://microservices.io/patterns/3rd-party-registration.html), service instances aren’t responsible for registering themselves with the service registry. Instead, another system component known as the service registrar handles the registration. The service registrar tracks changes to the set of running instances by either polling the deployment environment or subscribing to events. When it notices a newly available service instance it registers the instance with the service registry. The service registrar also deregisters terminated service instances. The following diagram shows the structure of this pattern.

![](images/Richardson-microservices-part4-5_third-party-pattern.png)

One example of a service registrar is the open source [Registrator](https://github.com/gliderlabs/registrator) project. It automatically registers and deregisters service instances that are deployed as Docker containers. Registrator supports several service registries, including etcd and Consul.

Another example of a service registrar is [NetflixOSS Prana](https://github.com/netflix/Prana). Primarily intended for services written in non‑JVM languages, it is a sidecar application that runs side by side with a service instance. Prana registers and deregisters the service instance with Netflix Eureka.

The service registrar is a built‑in component of deployment environments. The EC2 instances created by an Autoscaling Group can be automatically registered with an ELB. Kubernetes services are automatically registered and made available for discovery.

The third‑party registration pattern has various benefits and drawbacks. A major benefit is that services are decoupled from the service registry. You don’t need to implement service‑registration logic for each programming language and framework used by your developers. Instead, service instance registration is handled in a centralized manner within a dedicated service.

One drawback of this pattern is that unless it’s built into the deployment environment, it is yet another highly available system component that you need to set up and manage.

## Summary

In a microservices application, the set of running service instances changes dynamically. Instances have dynamically assigned network locations. Consequently, in order for a client to make a request to a service it must use a service‑discovery mechanism.

A key part of service discovery is the [service registry](https://microservices.io/patterns/service-registry.html). The service registry is a database of available service instances. The service registry provides a management API and a query API. Service instances are registered with and deregistered from the service registry using the management API. The query API is used by system components to discover available service instances.

There are two main service‑discovery patterns: client-side discovery and service-side discovery. In systems that use [client‑side service discovery](https://microservices.io/patterns/client-side-discovery.html), clients query the service registry, select an available instance, and make a request. In systems that use [server‑side discovery](https://microservices.io/patterns/server-side-discovery.html), clients make requests via a router, which queries the service registry and forwards the request to an available instance.

There are two main ways that service instances are registered with and deregistered from the service registry. One option is for service instances to register themselves with the service registry, the [self‑registration pattern](https://microservices.io/patterns/self-registration.html). The other option is for some other system component to handle the registration and deregistration on behalf of the service, the [third‑party registration pattern](https://microservices.io/patterns/3rd-party-registration.html).

In some deployment environments you need to set up your own service‑discovery infrastructure using a service registry such as [Netflix Eureka](https://github.com/Netflix/eureka), [etcd](https://github.com/etcd-io/etcd), or [Apache Zookeeper](https://zookeeper.apache.org/). In other deployment environments, service discovery is built in. For example, Kubernetes and [Marathon](https://mesosphere.github.io/marathon/docs/service-discovery-load-balancing.html) handle service instance registration and deregistration. They also run a proxy on each cluster host that plays the role of [server‑side discovery](https://microservices.io/patterns/server-side-discovery.html) router.

An HTTP reverse proxy and load balancer such as NGINX can also be used as a server‑side discovery load balancer. The service registry can push the routing information to NGINX and invoke a graceful configuration update; for example, you can use [Consul Template](https://hashicorp.com/blog/introducing-consul-template.html). NGINX Plus supports [additional dynamic reconfiguration mechanisms](https://www.nginx.com/products/nginx/load-balancing/#load-balancing-api) – it can pull information about service instances from the registry using DNS, and it provides an API for remote reconfiguration.

# Service Registration and Discovery

This guide walks you through the process of starting and using the Netflix Eureka service registry.

## What You Will Build

You will set up a [Netflix Eureka service registry](https://github.com/spring-cloud/spring-cloud-netflix) and then build a client that both registers itself with the registry and uses it to resolve its own host. A service registry is useful because it enables client-side load-balancing and decouples service providers from consumers without the need for DNS.

## What You Need

 - About 15 minutes
 - A favorite text editor or IDE
 - [JDK 1.8](https://www.oracle.com/java/technologies/downloads/) or later
 - [Gradle 4+](https://gradle.org/install/) or [Maven 3.2+](https://maven.apache.org/download.cgi)
 - You can also import the code straight into your IDE:
   - [Spring Tool Suite (STS)](https://spring.io/guides/gs/sts/)
   - [IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea/)

## How to complete this guide

Like most Spring [Getting Started guides](https://spring.io/guides), you can start from scratch and complete each step or you can bypass basic setup steps that are already familiar to you. Either way, you end up with working code.

To **start from scratch**, move on to [Starting with Spring Initializr](https://spring.io/guides/gs/service-registration-and-discovery/#scratch).
To **skip the basics**, do the following:

 - [Download](https://github.com/spring-guides/gs-service-registration-and-discovery/archive/main.zip) and unzip the source repository for this guide, or clone it using Git: `git clone https://github.com/spring-guides/gs-service-registration-and-discovery.git`
 - cd into `gs-service-registration-and-discovery/initial`
 - Jump ahead to [Start a Eureka Service Registry](#start-a-eureka-service-registry)

When you finish, you can check your results against the code in `gs-service-registration-and-discovery/complete`.

## Starting with Spring Initializr

For all Spring applications, you should start with the [Spring Initializr](https://start.spring.io/). The Initializr offers a fast way to pull in all the dependencies you need for an application and does a lot of the set up for you.

This guide needs two applications. The first application (the service application) needs only the Eureka Server dependency.

The second application (the client application) needs the Eureka Server and Eureka Discovery Client dependencies.

_For convenience, we have provided build files (a `pom.xml` file and a `build.gradle` file) at the top of the project (one directory above the `service` and `client` directories) that you can use to build both projects at once. We also added the Maven and Gradle wrappers there._

You can use this [pre-initialized project](https://start.spring.io/) (for the service application) or this [pre-initialized project](https://start.spring.io/) (for the client application) and click Generate to download a ZIP file. This project is configured to fit the examples in this tutorial.

To manually initialize the project:

 - Navigate to [https://start.spring.io](https://start.spring.io/). This service pulls in all the dependencies you need for an application and does most of the setup for you.
 - Choose either Gradle or Maven and the language you want to use. This guide assumes that you chose Java.
 - Click **Dependencies** and select **Eureka Server** for the service application and **Eureka Server** and **Eureka Discovery Client** for the client application.
 - Click **Generate**.
 - Download the resulting ZIP file, which is an archive of a web application that is configured with your choices.

_If your IDE has the Spring Initializr integration, you can complete this process from your IDE._

_You can also fork the project from Github and open it in your IDE or other editor._

## Start a Eureka Service Registry

You first need a Eureka Service registry. You can use Spring Cloud’s `@EnableEurekaServer` to stand up a registry with which other applications can communicate. This is a regular Spring Boot application with one annotation (`@EnableEurekaServer`) added to enable the service registry. The following listing (from `eureka-service/src/main/java/com.example.serviceregistrationanddiscoveryservice/ServiceRegistrationAndDiscoveryServiceApplication.java`) shows the service application:

```java
package com.example.serviceregistrationanddiscoveryservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@EnableEurekaServer
@SpringBootApplication
public class ServiceRegistrationAndDiscoveryServiceApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceRegistrationAndDiscoveryServiceApplication.class, args);
	}
}
```

When the registry starts, it will complain (with a stacktrace) that there are no replica nodes to which the registry can connect. In a production environment, you will want more than one instance of the registry. For our simple purposes, however, it suffices to disable the relevant logging.

By default, the registry also tries to register itself, so you need to disable that behavior as well.

It is a good convention to put this registry on a separate port when using it locally.

Add some properties to `eureka-service/src/main/resources/application.properties` to handle all of these requirements, as the following listing shows:

```yaml
server.port=8761

eureka.client.register-with-eureka=false
eureka.client.fetch-registry=false

logging.level.com.netflix.eureka=OFF
logging.level.com.netflix.discovery=OFF
```

## Talking to the Registry

Now that you have started a service registry, you can stand up a client that both registers itself with the registry and uses the Spring Cloud `DiscoveryClient` abstraction to interrogate the registry for its own host and port. The `@EnableDiscoveryClient` activates the Netflix Eureka `DiscoveryClient` implementation. (There are other implementations for other service registries, such as [Hashicorp’s Consul](https://www.consul.io/) or [Apache Zookeeper](https://zookeeper.apache.org/)). The following listing (from `eureka-client/src/main/java/example/serviceregistrationanddiscoveryclient/ServiceRegistrationAndDiscoveryClientApplication.java`) shows the client application:

```java
package com.example.serviceregistrationanddiscoveryclient;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class ServiceRegistrationAndDiscoveryClientApplication {

	public static void main(String[] args) {
		SpringApplication.run(ServiceRegistrationAndDiscoveryClientApplication.class, args);
	}
}

@RestController
class ServiceInstanceRestController {

	@Autowired
	private DiscoveryClient discoveryClient;

	@RequestMapping("/service-instances/{applicationName}")
	public List<ServiceInstance> serviceInstancesByApplicationName(
			@PathVariable String applicationName) {
		return this.discoveryClient.getInstances(applicationName);
	}
}
```

Whatever implementation you choose, you should soon see `eureka-client` registered under whatever name you specify in the `spring.application.name` property. This property is used a lot in Spring Cloud, often in the earliest phases of a service’s configuration. This property is used in service bootstrap and, so, by convention lives in `eureka-client/src/main/resources/bootstrap.properties` where it is found before `src/main/resources/application.properties`. The following listing shows the `bootstrap.properties` file:

```
Unresolved directive in <stdin> - include::complete/eureka-client/src/main/resources/bootstrap.properties[]
```

The `eureka-client` defines a Spring MVC REST endpoint (`ServiceInstanceRestController`) that returns an enumeration of all the registered `ServiceInstance` instances at `http://localhost:8080/service-instances/a-bootiful-client`. See the [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/) guide to learn more about building REST services with Spring MVC and Spring Boot.

## Test the Application

Test the end-to-end result by starting the `eureka-service` first and then, once that has loaded, starting the `eureka-client`.

To run the Eureka service with Maven, run the following command in a terminal window (in the `/complete` directory):

```
./mvnw spring-boot:run -pl eureka-service
```

To run the Eureka client with Maven, run the following command in a terminal window (in the `/complete` directory):

```
./mvnw spring-boot:run -pl eureka-client
```

To run the Eureka service with Gradle, run the following command in a terminal window (in the `/complete` directory):

```
./gradlew :eureka-service:bootRun
```

To run the Eureka client with Gradle, run the following command in a terminal window (in the `/complete` directory):

```
./gradlew :eureka-client:bootRun
```

The `eureka-client` will take about a minute to register itself in the registry and to refresh its own list of registered instances from the registry. Visit the `eureka-client` in the browser, at `http://localhost:8080/service-instances/a-bootiful-client`. There, you should see the `ServiceInstance` for the `eureka-client` reflected in the response. If you see an empty `<List>` element, wait a bit and refresh the page.

## Summary

Congratulations! You have just used Spring to stand up a Netflix Eureka service registry and to use that registry in a client application.

## See Also

The following guides may also be helpful:

 - [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service/)
 - [Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
 - [Building an Application with Spring Boot](https://spring.io/guides/gs/spring-boot/)

# Client-Side Load-Balancing with Spring Cloud LoadBalancer

This guide walks you through the process of creating load-balanced microservices.

## What You Will Build

You will build a microservice application that uses Spring Cloud LoadBalancer to provide client-side load-balancing in calls to another microservice.

## What You Will Need

 - About 15 minutes
 - A favorite text editor or IDE
 - JDK 1.8 or later
 - Gradle 6+ or Maven 3.5+
 - You can also import the code straight into your IDE:
   - Spring Tool Suite (STS) or IntelliJ IDEA

## Create a Root Project

This guide walks through building two projects, one of which is a dependency to the other. Consequently, you need to create two child projects under a root project. First, create the build configuration at the top level. For Maven, you need a `pom.xml` with `<modules>` that list the subdirectories:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>org.springframework</groupId>
    <artifactId>gs-spring-cloud-loadbalancer</artifactId>
    <version>0.1.0</version>
    <packaging>pom</packaging>

    <modules>
      <module>say-hello</module>
      <module>user</module>
    </modules>
</project>
```

For Gradle, you need want a `settings.gradle` that includes the same directories:

```properties
rootProject.name = 'gs-spring-cloud-loadbalancer'

include 'say-hello'
include 'user'
```

Optionally, you can include an empty `build.gradle` (to help IDEs identify the root directory).

## Create the Directory Structure

In the directory that you want to be your root directory, create the following subdirectory structure (for example, with `mkdir say-hello user` on *nix systems):

```
└── say-hello
└── user
```

In the root of the project, you need to set up a build system, and this guide shows you how to use Maven or Gradle.

## Starting with Spring Initializr

If you use Maven for the `Say Hello` project, visit the [Spring Initializr](https://start.spring.io/) to generate a new project with the required dependency (Spring Web).

The following listing shows the `pom.xml` file that is created when you choose Maven:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>spring-cloud-loadbalancer-say-hello</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>spring-cloud-loadbalancer-say-hello</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<spring-boot.repackage.skip>true</spring-boot.repackage.skip>
		<java.version>1.8</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>

</project>
```

If you use Gradle for the `Say Hello` project, visit the [Spring Initializr](https://start.spring.io/) to generate a new project with the required dependency (Spring Web).

The following listing shows the `build.gradle` file that is created when you choose Gradle:

```groovy
plugins {
	id 'org.springframework.boot' version '2.6.3'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-web'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
}

test {
	useJUnitPlatform()
}

bootJar {
	enabled = false
}
```

If you use Maven for the `User` project, visit the [Spring Initializr](https://start.spring.io/) to generate a new project with the required dependencies (Cloud Loadbalancer and Spring Reactive Web).

The following listing shows the `pom.xml` file that is created when you choose Maven:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.6.3</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>spring-cloud-loadbalancer-user</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>spring-cloud-loadbalancer-user</name>
	<description>Demo project for Spring Boot</description>
	<properties>
		<spring-boot.repackage.skip>true</spring-boot.repackage.skip>
		<java.version>1.8</java.version>
		<spring-cloud.version>2021.0.0</spring-cloud.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-webflux</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-loadbalancer</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>io.projectreactor</groupId>
			<artifactId>reactor-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
	<repositories>
		<repository>
			<id>spring-milestones</id>
			<name>Spring Milestones</name>
			<url>https://repo.spring.io/milestone</url>
		</repository>
	</repositories>

</project>
```

If you use Gradle for the `User` project, visit the [Spring Initializr](https://start.spring.io/) to generate a new project with the required dependencies (Cloud Loadbalancer and Spring Reactive Web).

The following listing shows the `build.gradle` file that is created when you choose Gradle:

```groovy
plugins {
	id 'org.springframework.boot' version '2.6.3'
	id 'io.spring.dependency-management' version '1.0.11.RELEASE'
	id 'java'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = '1.8'

repositories {
	mavenCentral()
	maven { url 'https://repo.spring.io/milestone' }
}

ext {
	set('springCloudVersion', "2021.0.0")
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-webflux'
	implementation 'org.springframework.cloud:spring-cloud-starter-loadbalancer'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testImplementation 'io.projectreactor:reactor-test'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

test {
	useJUnitPlatform()
}

bootJar {
	enabled = false
}
```

## Manual Initialization (optional)

If you want to initialize the project manually rather than use the links shown earlier, follow the steps given below:

 - Navigate to [https://start.spring.io](https://start.spring.io/). This service pulls in all the dependencies you need for an application and does most of the setup for you. 
 - Choose either Gradle or Maven and the language you want to use. This guide assumes that you chose Java.
 - Click **Dependencies** and select **Spring Web** (for the `Say Hello` project) or **Cloud Loadbalancer** and **Spring Reactive Web** (for the `User` project).
 - Click **Generate**.
 - Download the resulting ZIP file, which is an archive of a web application that is configured with your choices.

_If your IDE has the Spring Initializr integration, you can complete this process from your IDE._

## Implement the "Say Hello" service

Our “server” service is called `Say Hello`. It returns a random greeting (picked out of a static list of three) from an endpoint that is accessible at `/greeting`.

In `src/main/java/hello`, create the file `SayHelloApplication.java`.

The following listing shows the contents of `say-hello/src/main/java/hello/SayHelloApplication.java`:

```java
package hello;

import java.util.Arrays;
import java.util.List;
import java.util.Random;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@SpringBootApplication
public class SayHelloApplication {

  private static Logger log = LoggerFactory.getLogger(SayHelloApplication.class);

  public static void main(String[] args) {
    SpringApplication.run(SayHelloApplication.class, args);
  }

  @GetMapping("/greeting")
  public String greet() {
  log.info("Access /greeting");

  List<String> greetings = Arrays.asList("Hi there", "Greetings", "Salutations");
  Random rand = new Random();

  int randomNum = rand.nextInt(greetings.size());
  return greetings.get(randomNum);
  }

  @GetMapping("/")
  public String home() {
  log.info("Access /");
  return "Hi!";
  }
}
```

It is a simple `@RestController`, where we have one `@RequestMapping method` for the `/greeting` and another for the root path `/`.

We are going to run multiple instances of this application locally alongside a client service application. To get started:

 - Create a `src/main/resources` directory.
 - Create a `application.yml` file within the directory.
 - In that file, set a default value for `server.port`.

(We will instruct the other instances of the application to run on other ports so that none of the `Say Hello` instances conflict with the client when we get that running). While we are in this file, we can set the `spring.application.name` for our service too.

The following listing shows the contents of `say-hello/src/main/resources/application.yml`:

```yaml
spring:
  application:
    name: say-hello

server:
  port: 8090
```

## Access from a Client Service

Our users see the `User` application. It makes a call to the `Say Hello` application to get a greeting and then sends that greeting to our user when the user visits the endpoints at `/hi` and `/hello`.

In the User application directory, under `src/main/java/hello`, add the `UserApplication.java` file:

The following listing shows the contents of `user/src/main/java/hello/UserApplication.java`

```java
package hello;

import reactor.core.publisher.Mono;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.reactive.ReactorLoadBalancerExchangeFilterFunction;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.reactive.function.client.WebClient;

/**
 * @author Olga Maciaszek-Sharma
 */
@SpringBootApplication
@RestController
public class UserApplication {

  private final WebClient.Builder loadBalancedWebClientBuilder;
  private final ReactorLoadBalancerExchangeFilterFunction lbFunction;

  public UserApplication(WebClient.Builder webClientBuilder,
      ReactorLoadBalancerExchangeFilterFunction lbFunction) {
    this.loadBalancedWebClientBuilder = webClientBuilder;
    this.lbFunction = lbFunction;
  }

  public static void main(String[] args) {
    SpringApplication.run(UserApplication.class, args);
  }

  @RequestMapping("/hi")
  public Mono<String> hi(@RequestParam(value = "name", defaultValue = "Mary") String name) {
    return loadBalancedWebClientBuilder.build().get().uri("http://say-hello/greeting")
        .retrieve().bodyToMono(String.class)
        .map(greeting -> String.format("%s, %s!", greeting, name));
  }

  @RequestMapping("/hello")
  public Mono<String> hello(@RequestParam(value = "name", defaultValue = "John") String name) {
    return WebClient.builder()
        .filter(lbFunction)
        .build().get().uri("http://say-hello/greeting")
        .retrieve().bodyToMono(String.class)
        .map(greeting -> String.format("%s, %s!", greeting, name));
  }
}
```

We also need a `@Configuration` class where we set up a load-balanced `WebClient.Builder` instance:

The following listing shows the contents of `user/src/main/java/hello/WebClientConfig.java`:

```java
package hello;

import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.loadbalancer.annotation.LoadBalancerClient;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.reactive.function.client.WebClient;

@Configuration
@LoadBalancerClient(name = "say-hello", configuration = SayHelloConfiguration.class)
public class WebClientConfig {

  @LoadBalanced
  @Bean
  WebClient.Builder webClientBuilder() {
    return WebClient.builder();
  }

}
```

The configuration provides a `@LoadBalanced WebClient.Builder` instance, which we use when someone hits the `hi` endpoint of `UserApplication.java`. Once the `hi` endpoint is hit, we use this builder to create a `WebClient` instance, which makes an HTTP `GET` request to the `Say Hello` service’s URL and gives us the result as a `String`.

In `UserApplication.java`, we have also added a `/hello` endpoint that does the same action. However, rather than use the `@LoadBalanced` annotation, we use an `@Autowired` load-balancer exchange filter function (`lbFunction`), which we pass by using the `filter()` method to a `WebClient` instance that we programmatically build.

_Even though we set up the load-balanced `WebClient` instance slightly differently for the two endpoints, the end behavior for both is exactly the same. Spring Cloud LoadBalancer is used to select an appropriate instance of the `Say Hello` service._

Add the `spring.application.name` and `server.port` properties to `src/main/resources/application.properties` or `src/main/resources/application.yml`:

The following listing shows the contents of `user/src/main/resources/application.yml`

```yaml
spring:
  application:
    name: user

server:
  port: 8888
```

## Loadbalance Across Server Instances

Now we can access `/hi` or `hello` on the User service and see a friendly greeting:

```
$ curl http://localhost:8888/hi
Greetings, Mary!

$ curl http://localhost:8888/hi?name=Orontes
Salutations, Orontes!
```

In `WebClientConfig.java`, we pass a custom configuration for the LoadBalancer by using the `@LoadBalancerClient` annotation:

```java
@LoadBalancerClient(name = "say-hello", configuration = SayHelloConfiguration.class)
```

This means that, whenever a service named `say-hello` is contacted, instead of running with the default setup, Spring Cloud LoadBalancer uses the configuration provided in `SayHelloConfiguration.java`.

The following listing shows the contents of `user/src/main/java/hello/SayHelloConfiguration.java`:

```java
package hello;

import java.util.Arrays;
import java.util.List;

import reactor.core.publisher.Flux;

import org.springframework.cloud.client.DefaultServiceInstance;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.loadbalancer.core.ServiceInstanceListSupplier;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Primary;

/**
 * @author Olga Maciaszek-Sharma
 */
public class SayHelloConfiguration {

  @Bean
  @Primary
  ServiceInstanceListSupplier serviceInstanceListSupplier() {
    return new DemoServiceInstanceListSuppler("say-hello");
  }

}

class DemoServiceInstanceListSuppler implements ServiceInstanceListSupplier {

  private final String serviceId;

  DemoServiceInstanceListSuppler(String serviceId) {
    this.serviceId = serviceId;
  }

  @Override
  public String getServiceId() {
    return serviceId;
  }

  @Override
  public Flux<List<ServiceInstance>> get() {
    return Flux.just(Arrays
        .asList(new DefaultServiceInstance(serviceId + "1", serviceId, "localhost", 8090, false),
            new DefaultServiceInstance(serviceId + "2", serviceId, "localhost", 9092, false),
            new DefaultServiceInstance(serviceId + "3", serviceId, "localhost", 9999, false)));
  }
}
```

In that class, we provide a custom `ServiceInstanceListSupplier` with three hard-coded instances that Spring Cloud LoadBalancer chooses from while making the calls to the `Say Hello` service.

This step has been added to explain how you can pass your own custom configuration to the Spring Cloud LoadBalancer. However, you need not use the `@LoadBalancerClient` annotation and create your own configuration for the LoadBalancer. The most typical way is to use Spring Cloud LoadBalancer with service discovery. If you have any `DiscoveryClient` on your classpath, the default Spring Cloud LoadBalancer configuration uses it to check for service instances. As a result, you only choose from instances that are up and running. You can learn how to use `ServiceDiscovery` with this [guide](https://spring.io/guides/gs/service-registration-and-discovery/).

We also add an `application.yml` file with default `server.port` and `spring.application.name`.

The following listing shows the contents of `user/src/main/resources/application.yml`:

```yaml
spring:
  application:
    name: user

server:
  port: 8888
```

## Testing the Loadbalancer

The following listing shows how to run the `Say Hello` service with Gradle:

```
$ ./gradlew bootRun
```

The following listing shows how to run the `Say Hello` service with Maven:

```
$ mvn spring-boot:run
```

You can run other instances on ports 9092 and 9999 To do so with Gradle, run the following command:

```
$ SERVER_PORT=9092 ./gradlew bootRun
```

To do so with Maven, run the following command:

```
$ SERVER_PORT=9999 mvn spring-boot:run
```

Then you can start the `User` service. To do so, access `localhost:8888/hi` and watch the `Say Hello` service instances.

Your requests to the `User` service should result in calls to `Say Hello` being spread across the running instances in round-robin fashion:

```
2016-03-09 21:15:28.915  INFO 90046 --- [nio-8090-exec-7] hello.SayHelloApplication                : Access /greeting
```

# Spring Cloud Gateway

## Overview

This project provides a library for building an API Gateway on top of Spring WebFlux. Spring Cloud Gateway aims to provide a simple, yet effective way to route to APIs and provide cross cutting concerns to them such as: security, monitoring/metrics, and resiliency.

## Features

Spring Cloud Gateway features:

 - Built on Spring Framework 5, Project Reactor and Spring Boot 2.0
 - Able to match routes on any request attribute.
 - Predicates and filters are specific to routes.
 - Circuit Breaker integration.
 - Spring Cloud DiscoveryClient integration
 - Easy to write Predicates and Filters
 - Request Rate Limiting
 - Path Rewriting

## Getting Started

```java
@SpringBootApplication
public class DemogatewayApplication {
	@Bean
	public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
		return builder.routes()
			.route("path_route", r -> r.path("/get")
				.uri("http://httpbin.org"))
			.route("host_route", r -> r.host("*.myhost.org")
				.uri("http://httpbin.org"))
			.route("rewrite_route", r -> r.host("*.rewrite.org")
				.filters(f -> f.rewritePath("/foo/(?<segment>.*)", "/${segment}"))
				.uri("http://httpbin.org"))
			.route("hystrix_route", r -> r.host("*.hystrix.org")
				.filters(f -> f.hystrix(c -> c.setName("slowcmd")))
				.uri("http://httpbin.org"))
			.route("hystrix_fallback_route", r -> r.host("*.hystrixfallback.org")
				.filters(f -> f.hystrix(c -> c.setName("slowcmd").setFallbackUri("forward:/hystrixfallback")))
				.uri("http://httpbin.org"))
			.route("limit_route", r -> r
				.host("*.limited.org").and().path("/anything/**")
				.filters(f -> f.requestRateLimiter(c -> c.setRateLimiter(redisRateLimiter())))
				.uri("http://httpbin.org"))
			.build();
	}
}
```

To run your own gateway use the `spring-cloud-starter-gateway` dependency.

# Pattern: Client-side service discovery

## Context

Services typically need to call one another. In a monolithic application, services invoke one another through language-level method or procedure calls. In a traditional distributed system deployment, services run at fixed, well known locations (hosts and ports) and so can easily call one another using HTTP/REST or some RPC mechanism. However, a modern [microservice-based](https://microservices.io/patterns/microservices.html) application typically runs in a virtualized or containerized environments where the number of instances of a service and their locations changes dynamically.

![](images/discovery-problem.png)

Consequently, you must implement a mechanism for that enables the clients of service to make requests to a dynamically changing set of ephemeral service instances.

## Problem

How does the client of a service - the API gateway or another service - discover the location of a service instance?

## Forces

 - Each instance of a service exposes a remote API such as HTTP/REST, or Thrift etc. at a particular location (host and port)
 - The number of services instances and their locations changes dynamically. 
 - Virtual machines and containers are usually assigned dynamic IP addresses. 
 - The number of services instances might vary dynamically. For example, an EC2 Autoscaling Group adjusts the number of instances based on load.

## Solution

When making a request to a service, the client obtains the location of a service instance by querying a [Service Registry](https://microservices.io/patterns/service-registry.html), which knows the locations of all service instances.

The following diagram shows the structure of this pattern.

![](images/client-side-discovery.png)

This is typically handled by a [Microservice chassis framework](https://microservices.io/patterns/microservice-chassis.html)

## Examples

The [Microservices Example application](https://github.com/cer/microservices-examples) is an example of an application that uses client-side service discovery. It is written in Scala and uses Spring Boot and Spring Cloud as the [Microservice chassis](https://microservices.io/patterns/microservice-chassis.html). They provide various capabilities including client-side discovery.

`RegistrationServiceProxy` is a component of that application. In order to register a user, it invokes another service using the Spring Framework’s `RestTemplate`:

```java
@Component
class RegistrationServiceProxy @Autowired()(restTemplate: RestTemplate) extends RegistrationService {

  @Value("${user_registration_url}")
  var userRegistrationUrl: String = _

  override def registerUser(emailAddress: String, password: String): Either[RegistrationError, String] = {

      val response = restTemplate.postForEntity(userRegistrationUrl,
        RegistrationBackendRequest(emailAddress, password),
        classOf[RegistrationBackendResponse])
       ...
}
```

It is injected with the RestTemplate and the `user_registration_url`, which specifies the REST endpoint.

When the application is deployed `user_registration_url` is set to this URL [http://REGISTRATION-SERVICE/user](http://REGISTRATION-SERVICE/user) - see the `docker-compose.yml` file. `REGISTRATION-SERVICE` is the logical service name that is resolved to a network location using client-side service discovery. The service discovery is implemented using [Netflix OSS](https://netflix.github.io/) components. It provides [Eureka](https://github.com/Netflix/eureka/wiki/Eureka-at-a-glance), which is a [Service Registry](https://microservices.io/patterns/service-registry.html), and [Ribbon](https://github.com/Netflix/ribbon), which is an HTTP client that queries Eureka in order to route HTTP requests to an available service instance.

Client-side service discovery is configured using various Spring Cloud annotations:

```java
@Configuration
@EnableEurekaClient
@Profile(Array("enableEureka"))
class EurekaClientConfiguration {

  @Bean
  @LoadBalanced
  def restTemplate(scalaObjectMapper : ScalaObjectMapper) : RestTemplate = {
    val restTemplate = new RestTemplate()
    restTemplate.getMessageConverters foreach {
      case mc: MappingJackson2HttpMessageConverter =>
        mc.setObjectMapper(scalaObjectMapper)
      case _ =>
    }
    restTemplate
  }
```

The `@EnableEurekaClient` annotation enables the Eureka client. The `@LoadBalanced` annotation configures the `RestTemplate` to use Ribbon, which has been configured to use the Eureka client to do service discovery. As a result, the `RestTemplate` will handle requests to the [http://REGISTRATION-SERVICE/user](http://REGISTRATION-SERVICE/user) endpoint by querying Eureka to find the network locations of available service instances.

## Resulting context

Client-side discovery has the following benefits:

 - Fewer moving parts and network hops compared to [Server-side Discovery](https://microservices.io/patterns/server-side-discovery.html)

Client-side discovery also has the following drawbacks:

 - This pattern couples the client to the [Service Registry](https://microservices.io/patterns/service-registry.html) 
 - You need to implement client-side service discovery logic for each programming language/framework used by your application, e.g Java/Scala, JavaScript/NodeJS. For example, [Netflix Prana](https://github.com/Netflix/Prana) provides an HTTP proxy-based approach to service discovery for non-JVM clients.

# Pattern: Server-side service discovery

## Context

Services typically need to call one another. In a monolithic application, services invoke one another through language-level method or procedure calls. In a traditional distributed system deployment, services run at fixed, well known locations (hosts and ports) and so can easily call one another using HTTP/REST or some RPC mechanism. However, a modern [microservice-based](https://microservices.io/patterns/microservices.html) application typically runs in a virtualized or containerized environments where the number of instances of a service and their locations changes dynamically.

![](images/discovery-problem1.png)

Consequently, you must implement a mechanism for that enables the clients of service to make requests to a dynamically changing set of ephemeral service instances.

## Problem

How does the client of a service - the API gateway or another service - discover the location of a service instance?

## Forces

 - Each instance of a service exposes a remote API such as HTTP/REST, or Thrift etc. at a particular location (host and port
 - The number of services instances and their locations changes dynamically.
 - Virtual machines and containers are usually assigned dynamic IP addresses.
 - The number of services instances might vary dynamically. For example, an EC2 Autoscaling Group adjusts the number of instances based on load.

## Solution

When making a request to a service, the client makes a request via a router (a.k.a load balancer) that runs at a well known location. The router queries a [service registry](https://microservices.io/patterns/service-registry.html), which might be built into the router, and forwards the request to an available service instance.

The following diagram shows the structure of this pattern.

![](images/server-side-discovery.jpg)

## Examples

An AWS Elastic Load Balancer (ELB) is an example of a server-side discovery router. A client makes HTTP(s) requests (or opens TCP connections) to the ELB, which load balances the traffic amongst a set of EC2 instances. An ELB can load balance either external traffic from the Internet or, when deployed in a VPC, load balance internal traffic. An ELB also functions as a [Service Registry](https://microservices.io/patterns/service-registry.html). EC2 instances are registered with the ELB either explicitly via an API call or automatically as part of an auto-scaling group.

Some clustering solutions such as Kubernetes and [Marathon](https://mesosphere.github.io/marathon/docs/service-discovery-load-balancing.html) run a proxy on each host that functions as a server-side discovery router. In order to access a service, a client connects to the local proxy using the port assigned to that service. The proxy then forwards the request to a service instance running somewhere in the cluster.

## Resulting context

Server-side service discovery has a number of benefits:

 - Compared to [client-side discovery](https://microservices.io/patterns/client-side-discovery.html), the client code is simpler since it does not have to deal with discovery. Instead, a client simply makes a request to the router
 - Some cloud environments provide this functionality, e.g. AWS Elastic Load Balancer

It also has the following drawbacks:

 - Unless it’s part of the cloud environment, the router must is another system component that must be installed and configured. It will also need to be replicated for availability and capacity. 
 - The router must support the necessary communication protocols (e.g HTTP, gRPC, Thrift, etc) unless it is TCP-based router 
 - More network hops are required than when using [Client Side Discovery](https://microservices.io/patterns/client-side-discovery.html)

# Backends for Frontends pattern

Create separate backend services to be consumed by specific frontend applications or interfaces. This pattern is useful when you want to avoid customizing a single backend for multiple interfaces. This pattern was first described by Sam Newman.

## Context and problem

An application may initially be targeted at a desktop web UI. Typically, a backend service is developed in parallel that provides the features needed for that UI. As the application's user base grows, a mobile application is developed that must interact with the same backend. The backend service becomes a general-purpose backend, serving the requirements of both the desktop and mobile interfaces.

But the capabilities of a mobile device differ significantly from a desktop browser, in terms of screen size, performance, and display limitations. As a result, the requirements for a mobile application backend differ from the desktop web UI.

These differences result in competing requirements for the backend. The backend requires regular and significant changes to serve both the desktop web UI and the mobile application. Often, separate interface teams work on each frontend, causing the backend to become a bottleneck in the development process. Conflicting update requirements, and the need to keep the service working for both frontends, can result in spending a lot of effort on a single deployable resource.

![](images/backend-for-frontend.png)

As the development activity focuses on the backend service, a separate team may be created to manage and maintain the backend. Ultimately, this results in a disconnect between the interface and backend development teams, placing a burden on the backend team to balance the competing requirements of the different UI teams. When one interface team requires changes to the backend, those changes must be validated with other interface teams before they can be integrated into the backend.

## Solution

Create one backend per user interface. Fine-tune the behavior and performance of each backend to best match the needs of the frontend environment, without worrying about affecting other frontend experiences.

![](images/backend-for-frontend-example.png)

Because each backend is specific to one interface, it can be optimized for that interface. As a result, it will be smaller, less complex, and likely faster than a generic backend that tries to satisfy the requirements for all interfaces. Each interface team has autonomy to control their own backend and doesn't rely on a centralized backend development team. This gives the interface team flexibility in language selection, release cadence, prioritization of workload, and feature integration in their backend.

For more information, see [Pattern: Backends For Frontends](https://samnewman.io/patterns/architectural/bff/).

## Issues and considerations

 - Consider how many backends to deploy. 
 - If different interfaces (such as mobile clients) will make the same requests, consider whether it is necessary to implement a backend for each interface, or if a single backend will suffice. 
 - Code duplication across services is highly likely when implementing this pattern. 
 - Frontend-focused backend services should only contain client-specific logic and behavior. General business logic and other global features should be managed elsewhere in your application. 
 - Think about how this pattern might be reflected in the responsibilities of a development team. 
 - Consider how long it will take to implement this pattern. Will the effort of building the new backends incur technical debt, while you continue to support the existing generic backend?

## When to use this pattern

Use this pattern when:

 - A shared or general purpose backend service must be maintained with significant development overhead. 
 - You want to optimize the backend for the requirements of specific client interfaces. 
 - Customizations are made to a general-purpose backend to accommodate multiple interfaces. 
 - An alternative language is better suited for the backend of a different user interface.

This pattern may not be suitable:

 - When interfaces make the same or similar requests to the backend. 
 - When only one interface is used to interact with the backend.

# Microservices — Centralized Configuration with Spring Cloud

In microservice world, managing configurations of each service separately is a tedious and time-consuming task. In other words, if there are many number of modules, and managing properties for each module with the traditional approach is very difficult.

Central configuration server provides configurations (properties) to each microservice connected. As mentioned in the above diagram, Spring Cloud Config Server can be used as a central cloud config server by integrating to several environments.

**Environment Repository** — Spring uses environment repositories to store the configuration data. it supports various of authentication mechanisms to protect the configuration data when retrieving.

## Implementation

**1) Create the config server project**

Create a spring boot application including the below changes to the `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.ijayakantha.demo</groupId>
	<artifactId>spring-config-server</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>spring-config-server</name>
	<description>Demo project for Spring Boot Config Server</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.1.3.RELEASE</version>
		<relativePath /> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Greenwich.RELEASE</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.3</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

**2) Add spring cloud configuration support**

Spring Cloud Config Server provides external configuration as in HTTP resource-based API. How to achieve it is by using `@EnableConfigServer` annotation. Then spring config server is embedded to our spring boot application.

```java
package com.ijayakantha.springconfigserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;

@EnableConfigServer
@SpringBootApplication
public class SpringConfigServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringConfigServerApplication.class, args);
    }
}
```

**3) Create configuration repository**

Sample configuration repository has already created in github. check below link to explorer the content.

_NOTE : file names should be `<application-name>-<profile>.yml/properties`_

```yaml
server.url = 172.0.0.1:8383/development
```

**4) Configure application.properties / application.yml**

Update the below configurations in the server application

```yaml
spring:
  application:
   # name of the application
    name: spring-config-server
   # available profiles of the application 
  profiles:
    active: local,development,production
  cloud:
    config:
      server:
        git:
          # git url where the configurations are stored
          uri: https://github.com/ijayakantha/spring-config-server
          # if configurations are in sub folders search the subfolder by below name
          search-paths: server-config/
server:
  # config server port
  port: 8888
management:
  security:
  # disable security of the config server
    enabled: false
```

**5) Start the configuration server**

Start the spring boot application.

**6) Create the config server client project**

Create a spring boot application including the below changes to the `pom.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.jayakantha.demo</groupId>
	<artifactId>spring-config-client</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>spring-config-client</name>
	<description>Demo project for Spring Boot</description>

	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
		<spring-cloud.version>Dalston.SR1</spring-cloud.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-actuator</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-config</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-rest</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>${spring-cloud.version}</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>3.3</version>
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
				</configuration>
			</plugin>
		</plugins>
	</build>
</project>
```

**7) Create rest endpoint to retrieve configuration from server**

`server.url` is default to “Unable to connect to config server” if the value is unable to populate. if the value is populated which means we are able to connect to config server, then it will be responded back to via the web service.

```java
package com.ijayakantha.springconfigclient;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
public class SpringConfigClientApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringConfigClientApplication.class, args);
	}
}

@RefreshScope
@RestController
class MessageRestController {

	@Value("${server.url:Unable to connect to config server}")
	private String url;

	@RequestMapping("/server/url")
	String getURL() {
		return this.url;
	}
}
```

**8) Configure bootstrap.properties / bootstrap.yml**

Update the below configurations in the client application

```yaml
# name of the config server
spring.application.name=spring-config-server
# name of the profile required to be active in the client
spring.profiles.active=development
# ip and port of the config server
spring.cloud.config.uri=http://localhost:8888
# disable security when connecting to server
management.security.enabled=false
# expose actuator endpoints
management.endpoints.web.exposure.include=*
```

**9) Start the configuration client**

Start the spring boot application.

_Note : Client applications can control policy of how to handle missing config server._

```yaml
spring.cloud.config.fail-fast=true
```

_Client applications will not run if the above is set to true when the configuration server is not responding._

## Further Testing

Update the configuration in github and config server will automatically refresh the content. but client application wont refresh its configuration automatically without triggering the application to reload configurations.

**Step 1:** Use the below endpoint to force the client application to reload configuration from server and updated attributes are shown in the response.

![](images/1_YB7igwWx950zWScx9PCKVw.png)

**Step 2:** Updated values are being displayed upon refresh

![](images/1_N4RB8sQAgi0V11fQWFABvg.png)

# Related reading

 - [Service Discovery in a Microservices Architecture](https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture/)
 - [Service Registration and Discovery](https://spring.io/guides/gs/service-registration-and-discovery/)
 - [Client-Side Load-Balancing with Spring Cloud LoadBalancer](https://spring.io/guides/gs/spring-cloud-loadbalancer/)
 - [Spring Cloud Gateway](https://spring.io/projects/spring-cloud-gateway)
 - [Pattern: Client-side service discovery](https://microservices.io/patterns/client-side-discovery.html)
 - [Pattern: Server-side service discovery](https://microservices.io/patterns/server-side-discovery.html)
 - [Microservices — Centralized Configuration with Spring Cloud](https://medium.com/@ijayakantha/microservices-centralized-configuration-with-spring-cloud-f2a1f7b78cc2)
 - [Spring Cloud Config for Shared Microservice Configuration](https://developer.okta.com/blog/2020/12/07/spring-cloud-config)
 - [GIT backed Spring Cloud config server](https://www.youtube.com/watch?v=VzskpJEBtPw) [Video]
 - [Microservices configuration best practices](https://www.youtube.com/watch?v=AiGCx0raQfs) [Video]
 - [Spring Cloud](https://www.linkedin.com/learning/spring-spring-cloud-2/spring-to-the-cloud) [Video course]
 - [Cloud Architecture: Core Concepts](https://www.linkedin.com/learning/cloud-architecture-core-concepts-15687584/what-is-cloud-architecture) [Video course]
 - [What is service discovery really all about?](https://www.youtube.com/watch?v=GboiMJm6WlA&t=258s) [Video]

# Questions

 - What is Service Discovery? What is a purpose of Service Registry?
 - What is the difference between Server-Side and Client-Side Balancing? 
 - What is API Gateway? Name API Gateway technologies. Does it take part in service communication?
 - Define Backends for frontends pattern.
 - What are benefits of Service-Side load-balancing?
 - What provides Spring Cloud Gateway aims?
 - What is the purpose of Service Configuration in microservices architecture?
 - Why use Service Configuration?
 - What are best practices using Service Configuration? 