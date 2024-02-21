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

#### 1. Create a Kubernetes cluster on EKS:

* Create a role for EKS cluster:
   * Go to AWS Console -> IAM -> Roles -> “Create role” -> select “EKS- cluster” as use case and name it as “myEKSClusterRole”.

    <img src="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20a%20cloud-based%20voting%20application%20using%20Amazon%20EKS/Images/Role.png" width="200">
    
* Create a role for EKS node group:
   * Go to AWS Console -> IAM -> Roles -> “Create role” -> select “EC2” as use case and name it as “myEKSNodeRole” and select the permissions as shown in the below screenshot.
     
   <img src="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20a%20cloud-based%20voting%20application%20using%20Amazon%20EKS/Images/Role3.png" width="200">
   
* Create an EKS cluster:
   * Go to AWS Console -> EKS -> “Add cluster” -> “Create”.
   * Name it as “Cluster-1” and note that role will be selected by default. Click on “Next”. Select default security group and choose “public” for “Cluster endpoint access”.
   * Keep default settings for the rest and create the cluster.
   * To add persistent volume, go to Add-ons for the cluster and select “Amazon EBS CSI Driver” and add it to the cluster.
 
     <img src="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20a%20cloud-based%20voting%20application%20using%20Amazon%20EKS/Images/Addon.png" width="200">

   * Once the cluster is active add 2 node groups:
   * Go to Cluster -> Compute -> Add node group -> name it as NodeGroup and select the role for the node group click on next.
   * For instance size select t2.medium and keep the rest of the default settings and create the node group.

#### 2. Launch EC2 instance:

* In this step, we will create EC2 instances to create nodes.
* Create policy for EKS access:
   * Go to IAM -> Policies -> and create a policy with below actions:
      * DescribeCluster
      * ListClusters
      * DescribeNodegroup
      * ListNodegroups
      * ListUpdates
      * AccessKubernetesApi
* Create role for EKS NodeGroup of EC2 type:
   * Go to AWS Console -> IAM -> Roles -> “Create role” -> select  “EC2” as usecase and add AmazonEBSCSIDriverPolicy policy and newly created policy for EKS access.

     <img src="https://github.com/Bhat-Priyanka/Docker-And-Kubernetes-Projects/blob/main/Deploy%20a%20cloud-based%20voting%20application%20using%20Amazon%20EKS/Images/Role2.png" width="200">
     
* Go to AWS Console -> EC2 -> Launch instance -> name it as “ProjectInstance” and create a key pair if you don’t have any.
* Under “Advanced details” -> IAM instance profile -> Select newly created role and keep the default settings for the rest -> “Launch instance”.
* Select the instance -> Actions -> Connect and click on “Connect”.

3.	Install Kubectl and AWS CLI on EC2 server:
a.	In the CloudShell of the instance, run the following command to install kubectl:
curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.24.11/2023-03-17/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo cp ./kubectl /usr/local/bin
export PATH=/usr/local/bin:$PATH
•	run the following command to install AWS  CLI:
•	curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
•	unzip awscliv2.zip
•	sudo ./aws/install

•	Once the Cluster is ready run the command to set context:
aws eks update-kubeconfig --name EKS_CLUSTER_NAME --region regionname
•	Install Git:
•	Sudo yum install -y
•	Get the code repository from Github:
o	git clone https://github.com/N4si/K8s-voting-app.git
4.	Deploy 3 MongoDB pods with 3 persistent volumes:
a.	Create namespace for MongoDB:
i.	Kubectl create namespace cloudchamp
b.	In CloudShell of EC2, run the following commands:
Cd cd K8s-voting-app
Cd manifests
kubectl apply -f mongo-statefulset.yaml
kubectl get pods -n cloudchamp
kubectl get pv
5.	Create headless service for MongoDB:
kubectl apply -f mongo-service.yaml
kubectl get svc -n cloudchamp

6.	Set up replicaset on MongoDB:
Set mongo-0 as primary and mongo-1 and mongo-2 as secondary:
config set-context --current --namespace cloudchamp
cat << EOF | kubectl exec -it mongo-0 -- mongo
rs.initiate();
sleep(2000);
rs.add("mongo-1.mongo:27017");
sleep(2000);
rs.add("mongo-2.mongo:27017");
sleep(2000);
cfg = rs.conf();
cfg.members[0].host = "mongo-0.mongo:27017";
rs.reconfig(cfg, {force: true});
sleep(5000);
EOF
Confirm this by running the command:
kubectl exec -it mongo-0 -- mongo --eval "rs.status()" | grep "PRIMARY\|SECONDARY"

7.	Create database and add data:
cat << EOF | kubectl exec -it mongo-0 -- mongo
use langdb;
db.languages.insert({"name" : "csharp", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 5, "compiled" : false, "homepage" : "https://dotnet.microsoft.com/learn/csharp", "download" : "https://dotnet.microsoft.com/download/", "votes" : 0}});
db.languages.insert({"name" : "python", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 3, "script" : false, "homepage" : "https://www.python.org/", "download" : "https://www.python.org/downloads/", "votes" : 0}});
db.languages.insert({"name" : "javascript", "codedetail" : { "usecase" : "web, client-side", "rank" : 7, "script" : false, "homepage" : "https://en.wikipedia.org/wiki/JavaScript", "download" : "n/a", "votes" : 0}});
db.languages.insert({"name" : "go", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 12, "compiled" : true, "homepage" : "https://golang.org", "download" : "https://golang.org/dl/", "votes" : 0}});
db.languages.insert({"name" : "java", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 1, "compiled" : true, "homepage" : "https://www.java.com/en/", "download" : "https://www.java.com/en/download/", "votes" : 0}});
db.languages.insert({"name" : "nodejs", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 20, "script" : false, "homepage" : "https://nodejs.org/en/", "download" : "https://nodejs.org/en/download/", "votes" : 0}});

db.languages.find().pretty();
EOF

8.	Set Golang API for deployment:
a.	Create secret for MongoDB which holds credentials:
        kubectl apply -f mongo-secret.yaml
b.	Deploy 2 pods of api:
  kubectl apply -f api-deployment.yaml
kubectl get pods
9.	Expose API deployment through load balancer service:
kubectl expose deploy api \
 --name=api \
 --type=LoadBalancer \
 --port=80 \
 --target-port=8080

kubectl get svc
Go to EC2-> Load balancer and check that new load balancer have been created. Add pic
Set environment variable:
{
API_ELB_PUBLIC_FQDN=$(kubectl get svc api -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
until nslookup $API_ELB_PUBLIC_FQDN >/dev/null 2>&1; do sleep 2 && echo waiting for DNS to propagate...; done
curl $API_ELB_PUBLIC_FQDN/ok
echo
}
Test it on the browser -> external_IP_of_api_loadBalancer/ok and response should be ok!.
external_IP_of_api_loadBalancer/languages and confirm that data is displayed on the browser

10.	Deploy 2  pods for frontend:
kubectl apply -f frontend-deployment.yaml
kubectl get pods

11.	Expose frontend deployment through load balancer service:
kubectl expose deploy frontend \
 --name=frontend \
 --type=LoadBalancer \
 --port=80 \
 --target-port=8080
Kubectl get svc
Confirm that the Frontend ELB is ready to recieve HTTP traffic
{
FRONTEND_ELB_PUBLIC_FQDN=$(kubectl get svc frontend -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
until nslookup $FRONTEND_ELB_PUBLIC_FQDN >/dev/null 2>&1; do sleep 2 && echo waiting for DNS to propagate...; done
curl -I $FRONTEND_ELB_PUBLIC_FQDN
}

Generate the Frontend URL for browsing. 
echo http://$FRONTEND_ELB_PUBLIC_FQDN
Open the browser and verify that votes can be modified.
After modifying them, verify that MongoDB has updated data by enter the following command:
kubectl exec -it mongo-0 -- mongo langdb --eval "db.languages.find().pretty()"

Conclusion:
With this project, deployment of a cloud based application into AWS EKS is demonstrated. The application generated data will be captured and saved in MongoDB replicaset within the Kubernetes cluster.
