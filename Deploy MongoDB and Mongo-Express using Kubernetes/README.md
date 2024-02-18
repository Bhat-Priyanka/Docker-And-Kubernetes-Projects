## How to deploy MongoDB and Mongo-Express using Kubernetes?

### Overview

Kubernetes is an open-source container orchestration tool developed by Google. It helps you to manage containerized applications in different deployment environments.

The goal of this project is to show how Kubernetes can be used in deployment of an application.

### Prerequisites on Windows:

Docker, kubectl, and minikube installed.

### Kubernetes components involved with this project:
1. 2 Deployments / Pods
2. 2 Services
3. 1 ConfigMap
4. 1 Secret
  
### Browser request flow diagram:

<img src="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20MongoDB%20and%20Mongo-Express%20using%20Kubernetes/Images/K8.png" width="200">

### The main steps involved are as below:
1.	Create secret:
    * Run the following command in the same location as <a href="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20MongoDB%20and%20Mongo-Express%20using%20Kubernetes/mongo-secret.yaml" target="_blank">mongo-secret.yaml.</a>
    </br><code>kubectl apply -f mongo-secret.yaml</code>
    * Verify secret has been created by running the following command:
    </br><code>kubectl get secret</code>

2.	Create deployment and service for MongoDB:
    * Run the following command in the same location as <a href="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20MongoDB%20and%20Mongo-Express%20using%20Kubernetes/mongo.yaml" target="_blank">mongo.yaml.</a>
    </br><code>kubectl apply -f mongo.yaml</code>
    * Verify the deployment and service have been created by running the following command:
    </br><code>kubectl get all</code>

3.	Create ConfigMap and link data base URL to mongo-service:
    * Run the following command in the same location as <a href="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20MongoDB%20and%20Mongo-Express%20using%20Kubernetes/mongo-configmap.yaml" target="_blank">mongo-configmap.yaml.</a>
    </br><code>Kubectl apply -f mongo-configmap.yaml</code>
    * Verify the deployment and service have been created by running the following command:
    </br><code>Kubectl get all</code>

4.	Create deployment and external service for Mongo-Express:
    * Run the following command in the same location as <a href="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20MongoDB%20and%20Mongo-Express%20using%20Kubernetes/mongo-express.yaml" target="_blank">mongo-express.yaml.</a>
    </br><code>Kubectl apply -f mongo-express.yaml</code>
    * Verify the deployment and service have been created by running the following command:
    </br><code>Kubectl get all</code>
    </br><code>Kubectl logs mongo-express-[hash]</code>

5.	Start Mongo-express service by command:
    </br><code>minikube service mongo-express-service</code>
    * It opens Mongo-Express browser page.

### Conclusion:
If any changes have been made to MongoDB using Mongo-Express web page, the request will be forwarded to Mongo-Express external service and it will be forwarded to Mongo Express Pod and then it will be connected to MongoDB internal service and then to MongoDB Pod.
