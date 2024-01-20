# Table of Content

 - [Virtualization vs Containerization: 5 Main Differences](#virtualization-vs-containerization-5-main-differences)
 - [Containerized Microservices](#containerized-microservices)
 - [3 reasons why you should always run microservices apps in containers](#3-reasons-why-you-should-always-run-microservices-apps-in-containers)
 - [Docker overview](#docker-overview)
 - [Container Networking](#container-networking)
 - [Docker network containers](#docker-network-containers)
 - [Related reading](#related-reading)
 - [Questions](#questions)
 
# Virtualization vs Containerization: 5 Main Differences

Virtual machines and containers are both prominent tools within the hosting world. Both are a means for storing data within hosting platforms. And although both terms are becoming increasingly referenced, they are often confused.

Which is the better option? That topic is frequently up for debate and is unfortunately not easily answered.

The truth is that the right option depends on each user’s needs. This article will first provide a rundown of both technologies to answer this question. It discusses their uses, the situations where they perform best, and compares the advantages and disadvantages of virtualization vs containerization.

## Virtualization

To better understand how virtual machines function, there needs to be an understanding of [what is a virtual machine](https://www.liquidweb.com/kb/what-is-a-virtual-machine/) and what is [VMware](https://www.liquidweb.com/blog/what-is-vmware/).

As bare-metal servers improved in processing power and capacity, applications and services designed for those servers failed to utilize all available resources effectively. As a result, those resources are essentially wasted.

Virtualization is a design that takes advantage of all the available reserves on a single server. Consequently, multiple virtual machines can now use the same resources more efficiently.

## What is Virtualization?

Virtualization provides for the existence of multiple, smaller virtual servers within the framework of a larger server's physical environment. The virtualization software which runs on the parent allocates resources to each virtual server. The virtual servers also receive their own:

 - Operating system (OS). 
 - Drivers. 
 - Binaries. 
 - Libraries. 
 - Applications.

These virtual servers are isolated from each other. Each smaller server exists within a virtualized platform consisting of other servers. They also participate in a pool of resources shared with other virtual machines.

![](images/Hardware_Virtualization.png)

The software that makes all [types of virtualization](https://www.liquidweb.com/blog/what-is-virtualization/) possible is called a hypervisor. A [hypervisor](https://www.liquidweb.com/blog/what-is-a-hypervisor/) can be hardware, software, or firmware-based.

The hypervisor acts as a virtual layer that separates the physical server from its smaller virtual machines (VMs) regardless of its configuration. The hypervisor's separation allows multiple guest operating systems to run side by side within the overall physical server.

The hypervisor ensures the VMs access a defined portion of the central server’s resources. It’s also responsible for ensuring the VMs remain separated and are portioned to each virtual machine as configured. VMs can also update and modify the applications within their own space without affecting the applications on another virtual machine.

Increased security is an advantage of the VMs created by the virtualization process. The isolation of each “mini-server” allows for more control over the applications contained within each VM. If one VM becomes infected or corrupted, it will be kept separate from the other virtual machines and the host server. The VM is not affected by malicious activity on other portions of the physical server.

Each VM is essentially a virtual copy of the host server on its own operating system, causing some VMs to be resource-heavy and slow. Although they are only a smaller partition of the overall server, they can still consume a significant amount of memory and processing power.

Size is another challenge affecting the use of virtual machines. VMs tend to be significant in size. Because they are so large, the shareability and portability of VMs become a challenge.

## Containerization

**What is a Container?**

When comparing virtualization vs containerization, you can view containerization as a more modern solution. [The benefits of containerization](https://www.liquidweb.com/kb/the-benefits-of-containerization/) aim to solve many of the problems associated with virtualization.

The purpose of the containers is to encapsulate an application and its dependencies within its environment. This encapsulation allows them to run in isolation while using the same system resources and operating system as other containers within the server. Since there are no wasted resources on running separate operating system tasks, containerization allows for a much quicker, much more efficient deployment of applications.

Another difference between containerization vs virtualization is the size of the vessel. Each container image could be only a few megabytes in size, making it easier to share, migrate, and move.

![](images/vm-vs-containers.png)

A container engine can easily manage large numbers of containers to create, add, and remove containers as needed. The maintenance of containers is also simplified since we only need to update, patch, and fix bugs on one operating system.

Additionally, containers can decrease hardware costs since they help improve hardware utilization efficiency. Containers make this possible by allowing multiple applications to run on the same hardware.

If you look at virtualization vs containerization, containerization is most certainly an improvement. But, it’s not without its disadvantages. The most critical of these is data security.

Partitioning a server via containers makes the server more susceptible to a breach. Any vulnerability in the host kernel will also affect all of the containers. Furthermore, in the case of malware, the compromised container can’t be terminated and replaced with a new clean image.

Vital security features are lacking when it comes to a container vs virtualization. All applications within the server are running on the same operating system. Since one operating system powers all containers on the server, adaptability and diversity of applications become limited. This limitation results in more difficulty keeping the system secure.

| Virtualization | Containerization |
|----------------|------------------|
| More secure and fully isolated. | Less secure and isolated at the process level. |
| Heavyweight, high resource usage.	| Lightweight, less resource usage. |
| Hardware-level virtualization. | Operating system virtualization. |
| Each virtual machine runs in its own operating system. | All containers share the host operating system. |
| Startup time in minutes and slow provisioning. | Startup time in milliseconds and quicker provisioning. |

## Virtualization vs Containerization

After reviewing virtualization vs containers, you might be wondering which one is best. Several factors come into play when attempting to find the answer.

Each business and application will have different needs for their server. Choosing between virtualization and containerization will come down to the way you run your business. You’ll need to consider your plan for business development and operations. The way your team writes and produces applications will also play a role.

Virtualization and containerization are both data storage methods that create self-contained virtual packages. But, when comparing virtualization vs containerization, it will help to consider the following factors before deciding which one is right for you.

 - **Speed**: When it comes to speed, containers are the clear winner. They are designed to reduce loading and runtime for software applications significantly. Since the operating system is already up and running, the application will start without noticeable delay. This lack of delay is an excellent solution for software development teams because it will save time during the application testing cycle. Conversely, virtual machines need ample time for a complete boot process of the entire operating system, causing VM startup time to take much longer than a container.
 - **Resources**: Since virtual servers run separate operating systems and every system call has to go through the virtualization layer, they tend to be resource-intensive. This is particularly true for memory, as virtual machines consume memory even when not processing user requests. Since CPU virtualization is relatively inexpensive, the processor costs of virtual machines tend to be less. When it comes to containers, they start up quickly, keeping their memory consumption low compared to VMs. Containers also cut down on overhead because they can function without using a hypervisor.
 - **Security and isolation**: Virtualization wins when it comes to security and isolation. By their very nature, VMs remain separate and isolated from each other. One infected virtual machine will not affect another, and each virtual machine can implement its own security protocols. Since containers only isolate data and applications at the process level, they provide a less secure environment and depend on the security protocols of the host system.
 - **Portability and application sharing**: Since container images are much smaller than VMs, they are easier to transfer and save space on the host’s filing system. On the other hand, virtual machines need to copy the entire operating system, the host kernel, system libraries, configuration files, and any necessary file directories. This dramatically increases the image size and makes VMs challenging to share or transfer. Container images can be shared in several ways, and there are various application sharing hubs on the Internet. Virtual machine images can’t leverage these centralized hubs. Transferring them requires uploading them to another server instead.
 - **Operating system requirements**: A virtual machine is best when a business runs multiple applications that require a dedicated operating system. But, if most of the applications have the same operating system requirements, containerization would be a much more practical solution.
 - **Application lifecycle**: Containers work well for short-term application needs. They are quickly set up, portable, and have much faster startup times than VMs. Their limits stem from the lack of a dedicated operating system, processing, and storage resources. Use containers when your primary goal is to optimize your server resources' efficiency. But, virtual machines are a better choice if you plan to run applications that need to stay running for an extended period. VMs are better suited to handle extended runtimes since they operate in a virtualized environment that is more robust and versatile.

## Hybrid Solutions?

Today, hosting technology has evolved to the point where providers can begin to offer the best of both worlds. There are several ways of combining virtualization and containerization to leverage the power of these technologies to further their IT and business growth. This type of platform is called a hybrid container architecture and is formatted in various ways. The platform can be structured by putting a virtual machine inside a container, a single container inside a virtual machine, or multiple containers inside a virtual machine. This structure offers the security and isolation of a virtual machine with the fast and lightweight setup of a container.

## Which Is Better: Virtualization or Containerization?

In comparing virtualization vs containerization, we see that each technology serves a different purpose. Determining the better option relies heavily on the user’s application needs and required server capacity.

Choosing one method over the other is a big decision. IT managers should consider all of the significant differences before taking the plunge. To help you decide more efficiently, we’ve created a quick overview in the table below.

## Wrapping up

Interested in maximizing the power of your hosting platform? Liquid Web can help you leverage the benefits of containers and VMs through our managed private cloud. We can even layer your storage images internally on multiple types of servers.

Need to run a container? Require a private parent to implement a hypervisor? We have dedicated server options to meet your needs!

Contact our sales team to learn how you can take advantage of these technologies for your business.

# Containerized Microservices

Developing client-server applications has resulted in a focus on building tiered applications that use specific technologies in each tier. Such applications are often referred to as monolithic applications, and are packaged onto hardware pre-scaled for peak loads. The main drawbacks of this development approach are the tight coupling between components within each tier, that individual components can't be easily scaled, and the cost of testing. A simple update can have unforeseen effects on the rest of the tier, and so a change to an application component requires its entire tier to be retested and redeployed.

Particularly concerning in the age of the cloud, is that individual components can't be easily scaled. A monolithic application contains domain-specific functionality, and is typically divided by functional layers such as front end, business logic, and data storage. A monolithic application is scaled by cloning the entire application onto multiple machines, as illustrated in the next image.

![](images/monolithicapp.png)

## Microservices

Microservices offer a different approach to application development and deployment, an approach that's suited to the agility, scale, and reliability requirements of modern cloud applications. A microservices application is decomposed into independent components that work together to deliver the application's overall functionality. The term microservice emphasizes that applications should be composed of services small enough to reflect independent concerns, so that each microservice implements a single function. In addition, each microservice has well-defined contracts so that other microservices can communicate and share data with it. Typical examples of microservices include shopping carts, inventory processing, purchase subsystems, and payment processing.

Microservices can scale-out independently, as compared to giant monolithic applications that scale together. This means that a specific functional area, that requires more processing power or network bandwidth to support demand, can be scaled rather than unnecessarily scaling-out other areas of the application. Next image illustrates this approach, where microservices are deployed and scaled independently, creating instances of services across machines.

![](images/microservicesapp.png)

Microservice scale-out can be nearly instantaneous, allowing an application to adapt to changing loads. For example, a single microservice in the web-facing functionality of an application might be the only microservice in the application that needs to scale out to handle additional incoming traffic.

The classic model for application scalability is to have a load-balanced, stateless tier with a shared external datastore to store persistent data. Stateful microservices manage their own persistent data, usually storing it locally on the servers on which they are placed, to avoid the overhead of network access and complexity of cross-service operations. This enables the fastest possible processing of data and can eliminate the need for caching systems. In addition, scalable stateful microservices usually partition data among their instances, to manage data size and transfer throughput beyond which a single server can support.

Microservices also support independent updates. This loose coupling between microservices provides a rapid and reliable application evolution. Their independent, distributed nature supports rolling updates, where only a subset of instances of a single microservice will update at any given time. Therefore, if a problem is detected, a buggy update can be rolled back, before all instances update with the faulty code or configuration. Similarly, microservices typically use schema versioning, so that clients see a consistent version when updates are being applied, regardless of which microservice instance is being communicated with.

Therefore, microservice applications have many benefits over monolithic applications:

 - Each microservice is relatively small, easy to manage and evolve. 
 - Each microservice can be developed and deployed independently of other services. 
 - Each microservice can be scaled-out independently. For example, a catalog service or shopping basket service might need to be scaled-out more than an ordering service. Therefore, the resulting infrastructure will more efficiently consume resources when scaling out. 
 - Each microservice isolates any issues. For example, if there is an issue in a service it only impacts that service. The other services can continue to handle requests. 
 - Each microservice can use the latest technologies. Because microservices are autonomous and run side-by-side, the latest technologies and frameworks can be used, rather than being forced to use an older framework that might be used by a monolithic application.

However, a microservice based solution also has potential drawbacks:

 - Choosing how to partition an application into microservices can be challenging, as each microservice has to be completely autonomous, end-to-end, including responsibility for its data sources. 
 - Developers must implement inter-service communication, which adds complexity and latency to the application. 
 - Atomic transactions between multiple microservices usually aren't possible. Therefore, business requirements must embrace eventual consistency between microservices. 
 - In production, there is an operational complexity in deploying and managing a system compromised of many independent services. 
 - Direct client-to-microservice communication can make it difficult to refactor the contracts of microservices. For example, over time how the system is partitioned into services might need to change. A single service might split into two or more services, and two services might merge. When clients communicate directly with microservices, this refactoring work can break compatibility with client apps.

## Containerization

Containerization is an approach to software development in which an application and its versioned set of dependencies, plus its environment configuration abstracted as deployment manifest files, are packaged together as a container image, tested as a unit, and deployed to a host operating system.

A container is an isolated, resource controlled, and portable operating environment, where an application can run without touching the resources of other containers, or the host. Therefore, a container looks and acts like a newly installed physical computer or a virtual machine.

There are many similarities between containers and virtual machines, as illustrated in the next image.

![](images/containersvsvirtualmachines.png)

A container runs an operating system, has a file system, and can be accessed over a network as if it were a physical or virtual machine. However, the technology and concepts used by containers are very different from virtual machines. Virtual machines include the applications, the required dependencies, and a full guest operating system. Containers include the application and its dependencies, but share the operating system with other containers, running as isolated processes on the host operating system (aside from Hyper-V containers which run inside of a special virtual machine per container). Therefore, containers share resources and typically require fewer resources than virtual machines.

The advantage of a container-oriented development and deployment approach is that it eliminates most of the issues that arise from inconsistent environment setups and the problems that come with them. In addition, containers permit fast application scale-up functionality by instancing new containers as required.

The key concepts when creating and working with containers are:

 - Container Host: The physical or virtual machine configured to host containers. The container host will run one or more containers.
 - Container Image: An image consists of a union of layered filesystems stacked on top of each other, and is the basis of a container. An image does not have state and it never changes as it's deployed to different environments.
 - Container: A container is a runtime instance of an image.
 - Container OS Image: Containers are deployed from images. The container operating system image is the first layer in potentially many image layers that make up a container. A container operating system is immutable, and can't be modified.
 - Container Repository: Each time a container image is created, the image and its dependencies are stored in a local repository. These images can be reused many times on the container host. The container images can also be stored in a public or private registry, such as [Docker Hub](https://hub.docker.com/), so that they can be used across different container hosts.

Enterprises are increasingly adopting containers when implementing microservice based applications, and Docker has become the standard container implementation that has been adopted by most software platforms and cloud vendors.

The eShopOnContainers reference application uses Docker to host four containerized back-end microservices, as illustrated in the next image.

![](images/microservicesarchitecture.png)

The architecture of the back-end services in the reference application is decomposed into multiple autonomous sub-systems in the form of collaborating microservices and containers. Each microservice provides a single area of functionality: an identity service, a catalog service, an ordering service, and a basket service.

Each microservice has its own database, allowing it to be fully decoupled from the other microservices. Where necessary, consistency between databases from different microservices is achieved using application-level events. For more information, see [Communication Between Microservices](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/containerized-microservices#communication-between-microservices).

For more information about the reference application, see [.NET Microservices: Architecture for Containerized .NET Applications](https://dotnet.microsoft.com/en-us/download/e-book/microservices-architecture/pdf).


## Summary

Microservices offer an approach to application development and deployment that's suited to the agility, scale, and reliability requirements of modern cloud applications. One of the main advantages of microservices is that they can be scaled-out independently, which means that a specific functional area can be scaled that requires more processing power or network bandwidth to support demand, without unnecessarily scaling areas of the application that are not experiencing increased demand.

A container is an isolated, resource controlled, and portable operating environment, where an application can run without touching the resources of other containers, or the host. Enterprises are increasingly adopting containers when implementing microservice based applications, and Docker has become the standard container implementation that has been adopted by most software platforms and cloud vendors.

# 3 reasons why you should always run microservices apps in containers

Microservices are the emerging application platform: It is the architecture that will serve as the basis for many applications over the next 10 years. There's good reason for this: The advantages associated with microservices, such as their allowance for [agile development and artifacts](https://techbeacon.com/enterprise-it/how-reengineer-your-it-organization-cloud) and an architecture that enables businesses to [develop and roll out new digital offerings faster](https://techbeacon.com/app-dev-testing/using-devops-app-agility-rethink-how-you-engage-customers), make it the obvious choice.

Moving to a new application architecture means making some changes. You'll need to change existing practices, as well as many of the surrounding capabilities needed to operate a microservices-based application, such as monitoring, moving state off of the execution environment, and so on. But the biggest unanswered question is this: What execution environment should your microservices applications use? That is, in what kind of environment should they run?

## Runtime options

Fifteen years ago, your only option would have been to install and run microservices on a physical server running an operating system. But that approach would be incredibly wasteful today, given the enormous processing power servers now offer. To get around this, you might consider running multiple services on a single operating system instance, but that runs the risk of having conflicting library versions and application components, never mind the fact that one microservice failure could affect the availability of others. Running your microservice on bare metal is not an attractive option.

The next obvious choice is to divide up a physical server into many virtual servers (a.k.a. virtual machines, or VMs), allowing multiple execution environments to reside on a single server. Virtualization is a mature, well-established technology, most enterprises have already invested in virtual infrastructure, and most cloud providers use VMs as the basis for their infrastructure-as-a-service (IaaS) offerings. But it has serious limitations when it comes to running microservices.

The best choice for running a microservices application architecture is application containers. Containers encapsulate a lightweight runtime environment for your application, presenting a consistent software environment that can follow the application from the developer's desktop to testing to final production deployment, and you can run containers on physical or virtual machines.

Here's what you get when you move to containers as your foundation:

**Finer-grained execution environments**

While VMs make it easy to partition execution environments, using individual VMs for each microservice exacts a heavy cost, because each VM requires its own operating system. To be clear, no application component can be executed without placing it in its own VM.

While it is technically possible to run multiple application components within a single VM, this introduces the risk that components might conflict with one another, leading to application problems. Loading multiple services in a single VM raises the same problem IT might experience when running multiple apps on a single physical server. Avoiding conflicting library or application components and failure cascades is the reason organizations adopted server virtualization in the first place.

Using VMs also imposes a large performance penalty. Every virtual machine, which must run its own execution environment and copy of the operating system, uses up server processing cycles that you otherwise could use to run the applications.

Containers, by contrast, perform execution isolation at the operating system level. Here, a single operating system instance can support multiple containers, each running within its own, separate execution environment. By running multiple components on a single operating system you reduce overhead, freeing up processing power for your application components.

Just from an efficiency perspective, containers are a far better choice for a microservices architecture than are VMs.

**Better isolation allows for component cohabitation**

So, because containers enable multiple execution environments to exist on a single operating system instance, multiple application components can coexist in a single VM environment. In addition, with Linux, you can use [control groups](https://en.wikipedia.org/wiki/Cgroups) (cgroups) to isolate the complete execution environment for a particular application code set, ensuring that each has a private environment and so cannot affect the operation of other applications.

This ability to isolate frees developers from the need to segregate application code into separate VMs, retrieves the processing power previously devoted to those VMs, and offers it to the application code.

The net result: You get more application processing from a given piece of hardware. The implications of this can be subtle, because application characteristics vary. For example, some require lots of processing power, while others generate lots of network traffic. By being clever with workload placement, container users can maximize utilization levels for all of a server's resources, rather than just loading it up with several processor-hogging applications that leave some network capacity unused.

Google engineers did exactly this and recently described [how the company’s BORG container scheduler places workload to extract maximum use from its servers](https://queue.acm.org/detail.cfm?id=2898444).

With this type of isolation, it's now possible to place multiple microservices on a single server. The cgroup functionality ensures that no service can interfere with another, while container efficiency allows for higher server utilization rates.

There is, however, one caveat: You must run microservices in a redundant configuration to increase resiliency, and make sure they do not end up in side-by-side containers on the same physical server, because that defeats the purpose of redundancy. While it’s possible to manage container placement by hand to prevent colocation, it's much better to use a container management system such as [Kubernetes](https://kubernetes.io/), which lets you use policies to dictate container placement.

**Faster initialization and execution**

Containers enable finer-grained execution environments and permit application isolation. Both are great enablers for microservices applications, but what really makes containers a natural fit is their lightweight nature.

While virtualization provides clear benefits, there’s no denying that VMs, at 4GB or more in size, are large. I’ve already discussed the penalty this exacts on utilization, but it also means that VMs take a long time to get up and running. The time to bring all those bits off of disk and format them into an execution environment can be measured in minutes.

By their very nature, microservices-based applications tend to experience highly erratic workloads, and a virtualization-based microservices application can take 10 minutes or more to react to a traffic spike. During that time, users may find the application slow or completely unavailable. That's definitely not a desirable situation.

You can address this issue by pre-initializing VMs and having them standing at the ready. While not a bad strategy from a performance standpoint, it does waste resources as your standby VMs sit idly, using computing resources but not doing any useful work. You'll also need to have good insight into likely traffic patterns so that the right number of standby VMs are available. Unfortunately, this approach fails in the face of unexpected heavy volumes that can occur in today’s Internet world.

Containers, by contrast, are much smaller — perhaps one tenth or one hundredth the size of a virtual machine. And, because they do not require the operating system spin-up time associated with a virtual machine, containers are more efficient at initialization. Overall, containers start in seconds, or even milliseconds in some cases. That's much faster than VMs.

That's why, from a performance perspective, containers are a much better execution foundation for microservices architectures. Their quick instantiation maps much better to the erratic workload characteristics associated with microservices. It also makes them a better match for emerging policy-based microservices operations environments, since application topology decisions driven by policy (e.g., always have three instantiations of each microservice) can be more easily implemented via quick-starting, container-based application components.

**Best choice overall**

A microservices architecture does not dictate the use of containers. Netflix, for example, runs its entire microservices-based offering on Amazon Web Services, using AWS instances. But most organizations that move to microservices architectures will find containers a more congenial way to implement their applications.

Containers' finer-grained execution environments and ability to accommodate colocated application components in the same operating system instance will help you achieve better server utilization rates. And if your organization is running microservices applications in cloud environments, these characteristics will reduce your bill.

Finally, container-based microservices applications in production environments can better respond to erratic workloads. As companies begin to move more of the way they do business to digital offerings, shorter container initiation times can help increase user satisfaction and improve the financial performance of revenue-generating applications.

# Docker overview

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. With Docker, you can manage your infrastructure in the same ways you manage your applications. By taking advantage of Docker’s methodologies for shipping, testing, and deploying code quickly, you can significantly reduce the delay between writing code and running it in production.

## The Docker platform

Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allows you to run many containers simultaneously on a given host. Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host. You can easily share containers while you work, and be sure that everyone you share with gets the same container that works in the same way.

Docker provides tooling and a platform to manage the lifecycle of your containers:

 - Develop your application and its supporting components using containers. 
 - The container becomes the unit for distributing and testing your application. 
 - When you’re ready, deploy your application into your production environment, as a container or an orchestrated service. This works the same whether your production environment is a local data center, a cloud provider, or a hybrid of the two.

## What can I use Docker for?

**Fast, consistent delivery of your applications**

Docker streamlines the development lifecycle by allowing developers to work in standardized environments using local containers which provide your applications and services. Containers are great for continuous integration and continuous delivery (CI/CD) workflows.

Consider the following example scenario:

 - Your developers write code locally and share their work with their colleagues using Docker containers.
 - They use Docker to push their applications into a test environment and execute automated and manual tests.
 - When developers find bugs, they can fix them in the development environment and redeploy them to the test environment for testing and validation.
 - When testing is complete, getting the fix to the customer is as simple as pushing the updated image to the production environment.

**Responsive deployment and scaling**

Docker’s container-based platform allows for highly portable workloads. Docker containers can run on a developer’s local laptop, on physical or virtual machines in a data center, on cloud providers, or in a mixture of environments.

Docker’s portability and lightweight nature also make it easy to dynamically manage workloads, scaling up or tearing down applications and services as business needs dictate, in near real time.

**Running more workloads on the same hardware**

Docker is lightweight and fast. It provides a viable, cost-effective alternative to hypervisor-based virtual machines, so you can use more of your compute capacity to achieve your business goals. Docker is perfect for high density environments and for small and medium deployments where you need to do more with fewer resources.

## Docker architecture

Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers. The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface. Another Docker client is Docker Compose, that lets you work with applications consisting of a set of containers.

![](images/architecture.svg)

**The Docker daemon**

The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.

**The Docker client**

The Docker client (`docker`) is the primary way that many Docker users interact with Docker. When you use commands such as `docker run`, the client sends these commands to `dockerd`, which carries them out. The `docker` command uses the Docker API. The Docker client can communicate with more than one daemon.

**Docker Desktop**

Docker Desktop is an easy-to-install application for your Mac or Windows environment that enables you to build and share containerized applications and microservices. Docker Desktop includes the Docker daemon (`dockerd`), the Docker client (`docker`), Docker Compose, Docker Content Trust, Kubernetes, and Credential Helper. For more information, see [Docker Desktop](https://docs.docker.com/desktop/).

**Docker registries**

A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is configured to look for images on Docker Hub by default. You can even run your own private registry.

When you use the `docker pull` or `docker run` commands, the required images are pulled from your configured registry. When you use the `docker push` command, your image is pushed to your configured registry.

**Docker objects**

When you use Docker, you are creating and using images, containers, networks, volumes, plugins, and other objects. This section is a brief overview of some of those objects.

_Images_

An image is a read-only template with instructions for creating a Docker container. Often, an image is based on another image, with some additional customization. For example, you may build an image which is based on the `ubuntu` image, but installs the Apache web server and your application, as well as the configuration details needed to make your application run.

You might create your own images or you might only use those created by others and published in a registry. To build your own image, you create a Dockerfile with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates a layer in the image. When you change the Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what makes images so lightweight, small, and fast, when compared to other virtualization technologies.

_Containers_

A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

By default, a container is relatively well isolated from other containers and its host machine. You can control how isolated a container’s network, storage, or other underlying subsystems are from other containers or from the host machine.

A container is defined by its image as well as any configuration options you provide to it when you create or start it. When a container is removed, any changes to its state that are not stored in persistent storage disappear.

**Example `docker run` command**

The following command runs an ubuntu container, attaches interactively to your local command-line session, and runs `/bin/bash`.

```dockerfile
docker run -i -t ubuntu /bin/bash
```

When you run this command, the following happens (assuming you are using the default registry configuration):

1) If you do not have the `ubuntu` image locally, Docker pulls it from your configured registry, as though you had run `docker pull ubuntu` manually.
2) Docker creates a new container, as though you had run a `docker container create` command manually.
3) Docker allocates a read-write filesystem to the container, as its final layer. This allows a running container to create or modify files and directories in its local filesystem.
4) Docker creates a network interface to connect the container to the default network, since you did not specify any networking options. This includes assigning an IP address to the container. By default, containers can connect to external networks using the host machine’s network connection.
5) Docker starts the container and executes `/bin/bash`. Because the container is running interactively and attached to your terminal (due to the `-i` and `-t` flags), you can provide input using your keyboard while the output is logged to your terminal.
6) When you type `exit` to terminate the `/bin/bash` command, the container stops but is not removed. You can start it again or remove it.

## The underlying technology

Docker is written in the [Go programming language](https://go.dev/) and takes advantage of several features of the Linux kernel to deliver its functionality. Docker uses a technology called `namespaces` to provide the isolated workspace called the _container_. When you run a container, Docker creates a set of namespaces for that container.

These namespaces provide a layer of isolation. Each aspect of a container runs in a separate namespace and its access is limited to that namespace.

## Next steps

 - Read about [installing Docker](https://docs.docker.com/get-docker/).
 - Get hands-on experience with the [Getting started with Docker](https://docs.docker.com/get-started/) tutorial.

# Container Networking

Container Networking is an emerging application sandboxing mechanism used in home desktops and web-scale [enterprise networking](https://www.vmware.com/topics/glossary/content/enterprise-networking.html) solutions similar in concept to a virtual machine. Isolated inside the container from the host and all other containers are a full-featured Linux environment with its own users, file system, processes, and network stack. All applications inside the container are permitted to access or modify files or resources available inside the container only.

It is possible to run multiple containers at the same time, each with their own installations and dependencies. This is particularly useful in instances when newer versions of an application may require a dependency upgraded that may cause conflicts with other application dependencies running on the server. Unlike virtual machines, containers share host resources rather than fully simulating all hardware on the computer, making containers smaller and faster than virtual machines and reducing overhead. Particularly in the context of web-scale applications, containers were designed as a replacement to VMs as a deployment platform for microservice architectures.

Containers also have the characteristic of portability, for example, Docker, a container engine, allows developers to package a container and all its dependencies together. That container package can then be made available to download. Once downloaded, the container can immediately be run on a host.

## How Does Container Networking Work?

A container network is a form of virtualization similar to virtual machines (VM) in concept but with distinguishing differences. Primarily, the container method is a form of operating system virtualization as compared to VMs, which are a form of hardware virtualization.

Each virtual machine running on a hypervisor has their own operating system, applications, and libraries, and are able to encapsulate persistent data, install a new OS, use a different filesystem than the host, or use a different kernel version.

Conversely, containers are a “running instance” of an image, ephemeral operating system virtualization that spins up to perform some tasks then is deleted and forgotten. Because of the ephemeral nature of containers, system users run many more instances of containers than compared to virtual machines requiring a larger address space.

To create isolation, a container relies on two Linux Kernel features: namespace and cgroups. To give the container its own view of the system isolating it from other resources, a namespace is created for each of the resources and unshared from the remaining system. Control groups (Cgroups) are then used to monitor and limit system resources like CPU, memory, disk I/O, network, etc.

## Benefits of Container Networking

Containers are becoming rapidly adopted, replacing VMs as a platform for microservices.

Containers have several key benefits:

 - Run Containerized Apps Alongside Existing Workloads: Machines can run containerized apps alongside traditional VMs on the same infrastructure, granting flexibility and speed. 
 - Combine Portability with Security, Visibility, and Management: Because of the inherent design of containers it allows for greater security through sandboxing, resource transparency with the host, task management, and execution environment portability. 
 - Leverage Your Existing Infrastructure and Scale Easily: Use your existing SDDC to avoid costly and time-consuming re-architecture of your infrastructure that results in silos - silos occur when distinct departments maintain their own IT infrastructure within the same organization. This “silo effect” creates problems when rolling out organization-wide IT policies and upgrades due to the differences in technical configurations in each department. Reintegrating silos is a costly and time-consuming process that can be avoided through container networking. 
 - Provide Developers with a Docker-Compatible Interface: Developers already familiar with Docker can develop applications in containers through a Docker-compatible interface and then provision them through the self-service management portal or UI.

## Use of Container Networking in Web-Scale Application Deployments

Containers are deployed as part of the microservices architecture in enterprise environments to help encapsulate individual tasks common for large web applications. Each task may have its own container, the external-facing containers like APIs and GUIs are opened to the public internet, the others would reside on the private network.

The microservices model brings advantages:

 - Ease of Deployment: Host configurations can be embedded in containers making them ready to go once deployed. 
 - Disposable: Containers are designed for quick startup and disposal. If the host fails, bringing applications back online is as simple as bringing in a spare server. 
 - Fault-Tolerant: Containers create easy redundancy for databases and web servers. Copying the same container over several nodes provides for high-availability and [fault-tolerance](https://www.vmware.com/products/vsphere/fault-tolerance.html).

## Types of Container Networking

There are five types of container networking used today; their characteristics center around IP-per-container versus IP-per-pod models and the requirement of network address translation (NAT) versus no translation required.

 - **None**: The container receives a network stack; however, it lacks an external connection. This mode is useful for testing containers, staging a container for a later network connection, and assigning to containers not requiring external communications. 
 - **Bridge**: Containers that are bridged on an internal host network and allowed to communicate with other containers on the same host. Containers cannot be accessed from outside the host. Bridge network is the default for Docker containers. 
 - **Host**: This configuration allows a created container to share the host’s network namespace, granting the container access to all the host’s network interfaces. The least complex of the external networking configurations, this type is prone to port conflicts due to the shared use of the networking interfaces. 
 - **Underlay**: Underlays open the host interfaces directly to containers running on the host and remove the need for port-mapping, making them more efficient than bridges. 
 - **Overlay**: Overlays use networking tunnels to communicate across hosts, allowing containers to act like they are on the same machine when they are hosted on different hosts.

# Docker network containers

## Launch a container on the default network

Docker includes support for networking containers through the use of network drivers. By default, Docker provides two network drivers for you, the `bridge` and the `overlay` drivers. You can also write a network driver plugin so that you can create your own drivers but that is an advanced task.

Every installation of the Docker Engine automatically includes three default networks. You can list them:

```
docker network ls

NETWORK ID          NAME                DRIVER
18a2866682b8        none                null
c288470c46f6        host                host
7b369448dccb        bridge              bridge
```

The network named `bridge` is a special network. Unless you tell it otherwise, Docker always launches your containers in this network. Try this now:

```
docker run -itd --name=networktest ubuntu

74695c9cea6d9810718fddadc01a727a5dd3ce6a69d09752239736c030599741
```

![](images/bridge1.png)

Inspecting the network is an easy way to find out the container’s IP address.

```
docker network inspect bridge
```

You can remove a container from a network by disconnecting the container. To do this, you supply both the network name and the container name. You can also use the container ID. In this example, though, the name is faster.

```
docker network disconnect bridge networktest
```

While you can disconnect a container from a network, you cannot remove the builtin `bridge` network named `bridge`. Networks are natural ways to isolate containers from other containers or other networks. So, as you get more experienced with Docker, create your own networks.

## Create your own bridge network

Docker Engine natively supports both bridge networks and overlay networks. A bridge network is limited to a single host running Docker Engine. An overlay network can include multiple hosts and is a more advanced topic. For this example, create a bridge network:

```
docker network create -d bridge my_bridge
```

The `-d` flag tells Docker to use the `bridge` driver for the new network. You could have left this flag off as `bridge` is the default value for this flag. Go ahead and list the networks on your machine:

```
docker network ls

NETWORK ID          NAME                DRIVER
7b369448dccb        bridge              bridge
615d565d498c        my_bridge           bridge
18a2866682b8        none                null
c288470c46f6        host                host
```

If you inspect the network, it has nothing in it.

```
docker network inspect my_bridge

[
    {
        "Name": "my_bridge",
        "Id": "5a8afc6364bccb199540e133e63adb76a557906dd9ff82b94183fc48c40857ac",
        "Scope": "local",
        "Driver": "bridge",
        "IPAM": {
            "Driver": "default",
            "Config": [
                {
                    "Subnet": "10.0.0.0/24",
                    "Gateway": "10.0.0.1"
                }
            ]
        },
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

## Add containers to a network

To build web applications that act in concert but do so securely, create a network. Networks, by definition, provide complete isolation for containers. You can add containers to a network when you first run a container.

Launch a container running a PostgreSQL database and pass it the `--net=my_bridge` flag to connect it to your new network:

```
docker run -d --net=my_bridge --name db training/postgres
```

If you inspect your `my_bridge` you can see it has a container attached. You can also inspect your container to see where it is connected:

```
docker inspect --format='{{json .NetworkSettings.Networks}}'  db

{"my_bridge":{"NetworkID":"7d86d31b1478e7cca9ebed7e73aa0fdeec46c5ca29497431d3007d2d9e15ed99",
"EndpointID":"508b170d56b2ac9e4ef86694b0a76a22dd3df1983404f7321da5649645bf7043","Gateway":"10.0.0.1","IPAddress":"10.0.0.254","IPPrefixLen":24,"IPv6Gateway":"","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"MacAddress":"02:42:ac:11:00:02"}}
```

Now, go ahead and start your by now familiar web application. This time don’t specify a network.

```
docker run -d --name web training/webapp python app.py
```

![](images/bridge2.png)

Which network is your `web` application running under? Inspect the application to verify that it is running in the default `bridge` network.

```
docker inspect --format='{{json .NetworkSettings.Networks}}'  web

{"bridge":{"NetworkID":"7ea29fc1412292a2d7bba362f9253545fecdfa8ce9a6e37dd10ba8bee7129812",
"EndpointID":"508b170d56b2ac9e4ef86694b0a76a22dd3df1983404f7321da5649645bf7043","Gateway":"172.17.0.1","IPAddress":"10.0.0.2","IPPrefixLen":24,"IPv6Gateway":"","GlobalIPv6Address":"","GlobalIPv6PrefixLen":0,"MacAddress":"02:42:ac:11:00:02"}}
```

Then, get the IP address of your `web`

```
docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' web

172.17.0.2
```

Now, open a shell to your running `db` container:

```
docker container exec -it db bash

root@a205f0dd33b2:/# ping 172.17.0.2
ping 172.17.0.2
PING 172.17.0.2 (172.17.0.2) 56(84) bytes of data.
^C
--- 172.17.0.2 ping statistics ---
44 packets transmitted, 0 received, 100% packet loss, time 43185ms
```

After a bit, use `CTRL-C` to end the ping and notice that the `ping` failed. That is because the two containers are running on different networks. You can fix that. Then, use the `exit` command to close the container.

Docker networking allows you to attach a container to as many networks as you like. You can also attach an already running container. Go ahead and attach your running `web` app to the `my_bridge`.

```
docker network connect my_bridge web
```

![](images/bridge3.png)

Open a shell into the `db` application again and try the ping command. This time just use the container name `web` rather than the IP address.

```
docker container exec -it db bash

root@a205f0dd33b2:/# ping web
PING web (10.0.0.2) 56(84) bytes of data.
64 bytes from web (10.0.0.2): icmp_seq=1 ttl=64 time=0.095 ms
64 bytes from web (10.0.0.2): icmp_seq=2 ttl=64 time=0.060 ms
64 bytes from web (10.0.0.2): icmp_seq=3 ttl=64 time=0.066 ms
^C
--- web ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2000ms
rtt min/avg/max/mdev = 0.060/0.073/0.095/0.018 ms
```

The `ping` shows it is contacting a different IP address, the address on the `my_bridge` which is different from its address on the `bridge` network.

# Related reading

 - [Virtualization vs Containerization: 5 Main Differences](https://www.liquidweb.com/kb/virtualization-vs-containerization/)
 - [What is VMware Fusion?](https://www.liquidweb.com/kb/what-is-vmware-fusion/)
 - [What is VMware HA and DRS?](https://www.liquidweb.com/kb/what-is-vmware-ha-and-drs/)
 - [What is VMware vSphere?](https://www.liquidweb.com/kb/what-is-vmware-vsphere/)
 - [How to Use VMware to Set Up a Virtual Machine](https://www.liquidweb.com/kb/how-to-use-vmware/)
 - [What is VMware Infrastructure?](https://www.liquidweb.com/kb/vmware-infrastructure/)
 - [What is a VMware Template?](https://www.liquidweb.com/kb/what-is-a-vmware-template/)
 - [Containerized Microservices](https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/containerized-microservices)
 - [Enterprise-Application-Patterns-using-XamarinForms.pdf](https://dotnet.microsoft.com/en-us/download/e-book/xamarin/pdf)
 - [eShopOnContainers example](https://github.com/dotnet-architecture/eShopOnContainers)
 - [3 reasons why you should always run microservices apps in containers](https://techbeacon.com/app-dev-testing/3-reasons-why-you-should-always-run-microservices-apps-containers)
 - [Docker overview](https://docs.docker.com/get-started/overview/)
 - [Virtual Machines vs Docker Containers - Dive Into Docker](https://www.youtube.com/watch?v=TvnZTi_gaNc)
 - [Microservices Docker Example](https://www.youtube.com/watch?v=UWl7X2fUWTM)
 - [Docker for Developers ](https://www.linkedin.com/learning/docker-for-developers-2/leverage-the-power-of-docker?autoAdvance=true&autoSkip=false&autoplay=true&resume=false&u=2113185)
 - [Networking in Compose](https://docs.docker.com/compose/networking/)
 - [The 3 Biggest Wins When Using Alpine as a Base Docker Image](https://nickjanetakis.com/blog/the-3-biggest-wins-when-using-alpine-as-a-base-docker-image)
 - [Docker Base Image | How to decide](https://naiveskill.com/docker-base-image/)
 - [Create the smallest and secured golang docker image based on scratch](https://chemidy.medium.com/create-the-smallest-and-secured-golang-docker-image-based-on-scratch-4752223b7324)
 - [Runtime options with Memory, CPUs, and GPUs](https://docs.docker.com/config/containers/resource_constraints/)
 - [Container Networking](https://www.vmware.com/topics/glossary/content/container-networking.html#:~:text=A%20container%20network%20is%20a,a%20form%20of%20hardware%20virtualization.)
 - [Docker network containers](https://docs.docker.com/engine/tutorials/networkingcontainers/)

# Questions

 - What is Containerization in microservices?
 - What are the advantages that Containerization provides over Virtualization?
 - What is the difference between Virtualization and Containerization?
 - How many microservices are in a container? 