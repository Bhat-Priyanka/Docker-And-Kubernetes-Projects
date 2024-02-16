## How to create and deploy containerized application using Docker?

### Overview

Docker is a software platform that allows you to build, test, and deploy applications quickly. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime.

The goal of this project is to show how Docker can be used in development, CI/CD and deployment of an application.

### Prerequisites:

1.	Docker and node js installed on local machine. 
2.	AWS CLI installed and configured.
3.	Source code for the project downloaded from <a href="https://gitlab.com/nanuchi/techworld-js-docker-demo-app/-/tree/master" target="_blank">link.</a>
  
### Architecture diagram:

<img src="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Images/Docker.png" width="800">

### The main steps involved are as below:

1. Run Mongo containers on local machine:
   * In this step, Mongo and Mongo-Express iamges are pulled from Docker Hub and run on local machine.
2. Build and run Javascript application locally:
   * In this step, the JS application will be built and run.
3. Create a new Docker image:
   * In this step, Docker file is built and new image is created.
4. Push Docker image to AWS ECR:
   * In this step, a new private repository will be created on AWS ECR and the image will be pushed.
5. Deploy containerized image locally::
   * In this step, the image will be pulled from ECR and container is created locally.

### Steps to follow:

#### 1. Run Mongo containers on local machine:
1.	Pull MongoDB and MongoDB express from Docker Hub:
    *	Use below commands in the terminal:
        </br><code>docker pull mongo</code>
        </br><code>docker pull mongo-express</code>
    *	Make sure you have the above two images by entering the command:
        </br><code>docker images</code>
    * Run Mongo and Mongo Express containers:
        * Run Mongo on port 27017 on both host and Docker and Mongo Express on port 8081 on both host and Docker by using the file <a href="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/docker-compose.yaml" target="_blank">docker-compose.yaml</a>.
        * Run the following command from the directory where docker-compose-yml is located.
            </br><code>docker compose up</code>
    *	Once done, go to Docker desktop and verify if the containers are running.
  	* <img src="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Images/Containers.png" width="800">
  	*	Open the browser and go to http://localhost:8081. Login using “admin:password” and you should be able to see Mongo-Express page.  	
    * <img src="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Images/Express.png" width="800">  
  	*	Create a new database:
        * Go to Mongo-Express page and create a new database by the name “user-account”.
        * Create a collection called ‘users’ inside “user-account” database.

#### 2. Build and run Javascript application locally:
1.	Start the application locally:
    *	Go to the directory where the source code is present and run the following commands.
        </br><code>npm install</code>
        </br><code>node server.js</code>
    * Go to the browser and check if the app is running -> http://localhost:3000
2.	Create new entries in the database:
    * Using the app -> Edit profile -> create new entries in the database.
    * Check the Docker logs and Mongo-Express UI that new entries have been received.
  	* <img src="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Images/Users.png" width="800">

#### 3. Create a new Docker image:
1.	Build docker file:
    a.	In the same directory as <a href="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Dockerfile" target="_blank">Dockerfile.txt</a>, run the following command:
        </br><code>docker build -t my-app:1.0 .</code>
2.	By running the following command, check that image has been created for my-app.
        </br><code>docker ps</code>
3.	Run the newly created image:
    </br><code>docker run my-app:1.0</code>

#### 4. Push Docker image to AWS ECR
1.	Create private repository on Docker on AWS:
    a.	Go to AWS Console -> ECR -> “Create repository” -> name it as “my-app”.
   <img src="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Images/ECR.png" width="800">

2.	Push the image to AWS ECR repository:
    i.	Click on “View push commands”.
    ii.	Run the first command to authenticate yourself.
    iii.	Run the third command to add the tag to the image.
    iv.	Run the fourth command to push the image to the ECR repository.
3.	Go back to the repository and check that the image was pushed.
    <img src="https://github.com/Bhat-Priyanka/Docker-Projects/blob/main/Create%20and%20deploy%20containerized%20application%20using%20Docker/Images/Push.png" width="800">

#### 5.	Deploy containerized image locally:
1.	Open docker-compose.yaml file and uncomment the lines under “services” to pull the container image from AWS ECR repository.
2.	Run the container using:
    </br><code>docker compose up</code>
3.	Verify that the app is running by visiting http://localhost:3000.

### Conclusion:
With this project, an application is created and deployed using Docker and its usage in development, CI/CD and deployment stages are demonstrated.
