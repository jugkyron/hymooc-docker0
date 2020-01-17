
# Why and when to use Kubernetes
## Exercise 3.7 

### General
Kubernetes provide a framework for orchestrating containers in distributed systems e.g. different machines or cloud environment. Google open-sourced Kubernetes project 2014 and there is over decade and half experience that Google has with running production workloads at scale.There is also other popular alternatives also for container orchestration: Apache Mesos, Docker Swarm, AWS EC2 Container Service (ECS) and HashiCorp’s Nomad. In this rticle I concentrate in Kubernetes only. 

Docker provides an standard for packaging and distributing containerized applications and services but there is multiple additional challenges on managing containers as large deployment which Kubernetes provides a solution. Kubernetes deployment creates a cluster which components are presented in here: https://kubernetes.io/docs/concepts/overview/components/.

### Kubernetes Benefits: 
* Containers allow applications to be decomposed and modular e.g. an application can be distributed over different physical machines Kubernetes are needed for manage this.
* Scaling of application i.e. service discovery and DNS name based load balancing
* Storage orchestration. 
* Modularity of containers are managed in Kubernetes by Pods - typically collection of containers that are controlled as single application. 
* Lifecycle management including scheduling and coordinating containers 
* Automatic bin packing i.e. Kubernetes fits containers in nodes.
* Self-healing including monitoring health of an application containers, detect failure and restart container  
* Configuration management and secrets
* Seamless upgrading an application without service interruptions i.e. including automated rollouts and rollbacks. 
* Deploying and updating software at scale. Kubernetes deployment controller simplifies multiple complex management tasks i.e. in or out scaling at any time (Pods), visibility (monitor), time savings (pause/resume deployment) and version control / roll back. 
* Deployment operations include horizontal scaling, rolling updates and Canary deployments. 
* Kubernetes bring more flexibility for cloud based apps development; Kubernetes allows you to deploy cloud-native applications anywhere and manage them exactly as you like everywhere

*(CNCF Documentation, 2020, kubernetes.io)*

```
Refereces: 

Cloud Native Computing Foundation (CNCF). (2020) https://kubernetes.io/docs/concepts/overview/
```
