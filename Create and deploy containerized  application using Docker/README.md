## How to create and deploy containerized application using Docker?

### Overview

Docker is a software platform that allows you to build, test, and deploy applications quickly. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime.

The goal of this project is to show how Docker can be used in development, CI/CD and deployment of an application.

### Prerequisites:

1.	Docker and node js installed on local machine. 
2.	AWS CLI installed and configured.
3.	Source code for the project downloaded from <a href="https://gitlab.com/nanuchi/techworld-js-docker-demo-app/-/tree/master" target="_blank">link</a>
  
### Architecture diagram:

<img src="https://github.com/Bhat-Priyanka/AWS-Projects/blob/main/AWS%20serverless%20web%20application%201/Images/Arch3.png" width="800">

### The main steps involved are as below:

1. Copy the code from git repository provided by AWS to the CodeCommit repository 
   * In this step, a new repository is created in AWS CodeCommit by cloning the repository from GitHub. This will have all the necessary code for the project.


### Steps to follow:

#### 1. Copy the code from git repository provided by AWS to the CodeCommit repository:
