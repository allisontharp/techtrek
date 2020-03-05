---
title: 'An Introduction to Containers: Part 2 - Containerizing Python and MySQL'
tags:
  - containers
  - kubernetes
  - MySQL
  - Python
url: 1977.html
id: 1977
categories:
  - How To
date: 2018-12-29 11:00:57
---

In the [previous post](http://www.techtrek.io/an-introduction-to-containers-part-1/), we reviewed what containers are and why they are useful.  In this post, we will look at containerizing my [board game geek scraper](http://www.techtrek.io/board-game-play-tracking-using-sql-part-3-python-automation/).

### Initial Plan

The general goal will be to use Python for the actual scripting, Docker for creating the containers, and Kubernetes for container deployment.  We will first need to install Docker and then create a container for our Python script.  After testing on my local machine, we'll look at deploying using Kubernetes to Azure.  These are the steps I plan to take:

1.  Install Docker
2.  Create a Docker image with my current Python script
3.  Install Minikube
4.  Use Kubernetes to deploy the containers

### 1\. Install Docker

You can download the Docker installer [here](https://hub.docker.com/editions/community/docker-ce-desktop-windows) (you'll need to first create a Docker account).  Prior to the installation, it will ask you if you'd like to use Windows containers instead of Linux containers.  I left this unchecked. Once the installation is complete, restart the computer (this will allow you to use the Docker environment variable).  After restart, open the command prompt and check the version of docker:

docker version

Client: Docker Engine - Community
 Version:           18.09.0
 API version:       1.39
 Go version:        go1.10.4
 Git commit:        4d60db4
 Built:             Wed Nov  7 00:47:51 2018
 OS/Arch:           windows/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.0
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.4
  Git commit:       4d60db4
  Built:            Wed Nov  7 00:55:00 2018
  OS/Arch:          linux/amd64
  Experimental:     false

### 2\. Create a Docker Image with my current Python Script

The first step in creating a Docker Image is to create a Dockerfile.  The Dockerfile is essentially a batch file -- it is an extensionless file that gives Docker the instructions on how to build the image. First, create a folder to hold your Python scripts and move the files to the folder.  Then, create a file named Dockerfile that has no extension and edit the file (I used Notepad++ but you can use whatever text editor you want).  My Dockerfile has the following lines:

FROM python:2
WORKDIR /usr/src/app
ADD monthlyPull.py /usr/src/app
ADD funcs.py /usr/src/app
ADD scrapeFuncs.py /usr/src/app
ADD settings.py /usr/src/app
RUN chmod 777 monthlyPull.py
RUN pip install beautifulsoup4
RUN pip install mysql-connector-python
RUN pip install pika
CMD \["python", "-u", "./monthlyPull.py"\]

The **FROM** command sets the basic Docker image for your container and it needs to be the first command in your Dockerfile.  You can use any image in Docker Hub (which is a public repository).  In this case, I searched python and found the Docker Official Image for Python [here](https://hub.docker.com/_/python?tab=description).  Because my script is currently written for Python 2, I use FROM python:2.  If I needed to use Python 3, it is as simple as using FROM python:3.  By default this creates a Debian flavored Linux image, though that can be changed by adding further parameters to the FROM command. Next, we set the working directory with the **WORKDIR** command.  This isn't strictly necessary, but I did it so I know where things are going for future reference. The next several lines use the **ADD **command.  This simply adds the Python scripts to our working directory for use later.  Because they are imported in the Python script, we need to make sure they are all in the same directory. The **RUN** command runs Linux commands.  In this case, we are changing the permissions to our main file (monthlyPull.py) and installing the module dependencies. Finally, we run the MonthlyPull.py python script with the **CMD** command.  The "-u" will allow anything we print to be written to the Kubernetes log, which will help for debugging. To build the image, run the following in the command prompt (in the same directory as your other files):

docker build --no-cache -t python-bgg:main .

The first time you run this, it will create an image named python-bgg.  If it already exists, it will just update it.  To run the image:

docker run python-bgg:main

### 2(b) Create a Docker Image for the MySQL Database

The previous image will work on my current machine because both the image and the database are on the same host.  However, when I move it to a Kubernetes Pod, it will no longer work because it won't be able to find the database.  To fix this, we'll create an image for the MySQL database and later link them together in Kubernetes. Create an image for the mysql database, we will create another Dockerfile with the following contents:

FROM mysql:5.6
WORKDIR /usr/src/app
ENV MYSQL_DATABASE boardgamecollection
ENV MYSQL\_ROOT\_PASSWORD hunter2
ENV MYSQL_USER vb
ENV MYSQL_PASSWORD hunter2
COPY bgg2.sql /docker-entrypoint-initdb.d/bgg2.sql
CMD \["mysqld"\]

The big key here is the COPY command.  I created a MySQL dump of my database using:

mysqldump -u root -p boardgamecollection -r bgg2.sql

The COPY command copies that file to the location specified.  The mysql image will execute all of the sql files in that location to create your database. Then, build the image just like the previous:

docker build --no-cache -t mysql-bgg:main .

### 3\. Install Minikube

Minikube is a local environment to create single-node Kubernetes clusters on your local machine (inside a virtual machine).  Installation instructions for this are [here](https://kubernetes.io/docs/user-journeys/users/application-developer/foundational/).  The steps I followed were:

*   Install kubectl via PowerShell with the following commands:

Install-Script -Name install-kubectl -Scope CurrentUser -Force
install-kubectl.ps1 "C:\\Users\\Allison\\Downloads\\"

*   [Install Minikube](https://github.com/kubernetes/minikube/releases) (I used the minikube-installer.exe on my Windows machine)

The minikube-installer was supposed to add minikube to my environment variables but did not, so I had to do that manually and then restart my machine.  Also, The Minikube installation directions say to install a hypervisor, either VirtualBox or Hyper-V for Windows.  This step can be skipped if you are on Windows, as Hyper-V comes with it. After installing and updating my Path variable, I ran the following in the command prompt:

minikube start --vm-driver hyperv

It took a few minutes to start up the cluster and download all of the dependencies.  It had the following output:

C:\\Users\\Allison>minikube start --vm-driver hyperv
Starting local Kubernetes v1.12.4 cluster...
Starting VM...
Getting VM IP address...
E1226 12:10:46.575681    9688 start.go:211\] Error parsing version semver:  Version string empty
Moving files into cluster...
Downloading kubeadm v1.12.4
Downloading kubelet v1.12.4
Finished Downloading kubeadm v1.12.4
Finished Downloading kubelet v1.12.4
Setting up certs...
Connecting to cluster...
Setting up kubeconfig...
Stopping extra container runtimes...
Starting cluster components...
Verifying kubelet health ...
Verifying apiserver health ...Kubectl is now configured to use the cluster.
Loading cached images from config file.


Everything looks great. Please enjoy minikube!

To interact with the clusters, start the dashboard:

minikube dashboard

I had an issue where I was getting Temporary Error: unexpected response code: 503.  Enabling the dashboard resolved the issue:

minikube addons enable dashboard

### 4\. Create a Kubernetes Pod

A Pod can hold and run one or more containers.  In our case, we will create a single pod to hold both our Python container as well as our MySQL container.  To create our pod, we need to first create a YAML file with the information on which containers to put in the pod.  I put the following in a file named private-reg-pod.yaml:

apiVersion: v1
kind: Pod
metadata:
  name: private-reg
spec:    
  containers:
  \- name: mysql-container
    image: mysql-bgg:main
    imagePullPolicy: Never 
    
  \- name: python-container
    image: python-bgg:main
    imagePullPolicy: Never 
      
  restartPolicy: Never

YAML is similar to Python in that white space matters.  It can sometimes be tough to know why your code isn't correct, so [this](https://codebeautify.org/yaml-validator) is a good source for validating it.  I used it several times during this project. Here, we are naming our containers mysql-container and python-container and telling it which Docker images to use for each.  Because both images are on my local machine and not on the DockerHub, I set **imagePullPolicy** to Never.  This tells Kubernetes to never look for the images online.  The **restartPolicy** is set to Never so that the pods don't try to restart.  If it fails, I'm fine with it not trying again. To create the pod, run the following command:

kubectl create -f private-reg-pod.yaml

You can then check your Kubernetes Dashboard to see if it is running.  You can check the logs in the dashboard or through the command line.  To do it via the command line:

 kubectl logs private-reg

Also, you can remote in to your pods to do some debugging:

kubectl exec -it private-reg -- /bin/bash

### 5\. Debugging, Debugging, Debugging

Using the logs and bash, I found that the MySQL database took a while to get up and running and the Python script was erroring out because it couldn't connect.  I believe there is a way to tell the pod to not start the Python container until the MySQL container is ready, but I wasn't able to get that working.  Instead, I updated the Python script to not error out if it can't immediately connect to the database.  I let it try every 30 seconds for 10 minutes to connect before completely erroring out.  To do this, I updated my dbConnect function to be this:

def dbConnect():
	MinutesToAttempt = 10
	MinutesAttempted = 0
	while MinutesAttempted < MinutesToAttempt:
		try:
			conn = mysql.connector.connect(user = dbWrite, password = dbPW, host = dbHost, database = database)
			c = conn.cursor(buffered=True)
			MinutesAttempted = MinutesToAttempt
		except mysql.connector.Error as err:
			print("Couldn't connect.  Tried for {min} minutes and will continue for {y} minutes".format(min=MinutesAttempted, y = MinutesToAttempt-MinutesAttempted))
			print("Error: {}",format(err))
			time.sleep(30)
			MinutesAttempted += 0.5
			
	return conn, c

After updating this, I rebuilt the image and recreated the pod and it worked!

### Final Thoughts

I'm really amazed at how easy creating the container was.  It took only 11 lines to spin up a Linux environment on my own machine.  The majority of the commands (7 of the 11) are simply adding the files and dependencies.  I'm also pretty shocked that I didn't have to do **anything** to my Python script to get this to work.  I had assumed I would need to do _something_ but, I didn't.  Very cool!  Also, by using the following command while my Python script is running, I see that this is only taking up 1.3 GB!

docker system df
TYPE                TOTAL               ACTIVE              SIZE                RECLAIMABLE
Images              11                  9                   1.371GB             954.2MB (69%)
Containers          15                  2                   3.419MB             2.592MB (75%)
Local Volumes       0                   0                   0B                  0B
Build Cache         0                   0                   0B                  0B

Getting the Pod to work correctly was a lot more difficult.  I struggled with figuring out how to get the data in my database over to the container in the pod.  After figuring that out, I then struggle to figure out how to get the two containers to communicate with one another.  However, I learned a **ton** and I am sure next time will be much smoother. Though not directly related to containers, this project has me motivated to rewrite my Python script to be cleaner and in Python 3. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._