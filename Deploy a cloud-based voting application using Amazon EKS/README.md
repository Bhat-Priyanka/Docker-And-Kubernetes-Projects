## How to deploy a cloud-based voting application using Amazon EKS?

### Overview

Kubernetes is an open-source container orchestration tool developed by Google. It helps you to manage containerized applications in different deployment environments.

In this project an application will be deployed on Amazon EKS using React JS as frontend, API using Golang which is compiled in container image and Mongo DB as a backend database with replicaset for high availability. 

### Prerequisites:

AWS account

### Kubernetes components involved with this project:
1. 3 pods for MongoDB - statefulSet with persistent volume
2. 2 pods for API
3. 2 pods for frontend

### AWS services involved with this project:
1. ELB
2. EKS
3. EBS
   
### Architecture diagram:

<img src="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20a%20cloud-based%20voting%20application%20using%20Amazon%20EKS/Images/EKS.png" width="200">

### The main steps involved are as below:
