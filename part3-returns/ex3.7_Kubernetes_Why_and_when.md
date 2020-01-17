
# Why and when to use Kubernetes
## Exercise 3.7 a) 

### General
Kubernetes provide a framework for orchestrating containers in distributed systems e.g. different machines or cloud environment. Google open-sourced Kubernetes project 2014 and there is over decade and half experience that Google has with running production workloads at scale. There exists also many popular alternatives for Kubernetes and container orchestration: Apache Mesos, Docker Swarm, AWS EC2 Container Service (ECS) and HashiCorp’s Nomad. However in this article I will concentrate in Kubernetes only. 

Docker provides an standard for packaging and distributing containerized applications and services but there is additional challenges on managing containers as large deployment which Kubernetes can help greatly. Kubernetes deployment creates a cluster for getting more information from components see link: https://kubernetes.io/docs/concepts/overview/components/.

### Kubernetes Benefits: 
* Containers allow applications to be decomposed and modular but it is common use case that an application can be distributed over different physical machines or cloud nodes however Docker doesn't have support for seamless and efficent management for this use case. Kubernetes instead are proving means that provide support for container application distributed deployment. Kubernetes also makes possible an application to be decomposed high amount of nodes and same time up - down scalable and controlled way. 

###  Why it is beneficial use Kubernetes for managing distributed containerized application
* it enables controlled scaling of application i.e. service discovery and DNS name based load balancing
* it offers configuration management, managing secrets and makes possible storage orchestration
* modularity together with scalability i.e. Kubernetes Pods concept makes possible form a collection of containers that are controlled as single application. 
* it has lifecycle management which includes scheduling and coordinating containers 
* it increases efficiency by providing automatic bin packing i.e. Kubernetes fits containers in nodes.
* failure management i.e. self-healing that includes monitoring health of an application containers, detect failure and restart container  
* seamless upgrading an application without service interruptions and updating software at scale (version control) and an automated rollouts and rollbacks. Operations include horizontal scaling of deployment, rolling updates and Canary deployments. 
* Development benefits getting more flexibility for cloud based apps development; Kubernetes allows you to deploy cloud-native applications anywhere and manage them exactly as you like everywhere

*(CNCF Documentation, 2020, kubernetes.io)*

```
Refereces: 
Cloud Native Computing Foundation (CNCF). (2020) https://kubernetes.io/docs/concepts/overview/
```
