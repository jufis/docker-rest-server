#Sample docker rest server
This is a sample server app inheriting from spring boot to create a REST service in order to be used from the docker-rest-client repository.

It uses the rhuss excelenet [docker-maven-plugin](https://github.com/rhuss/docker-maven-plugin "rhuss docker-maven-plugin") to build/start/stop/push/check logs a docker image. 

It assumes you run a local docker-registry running on port 5000.

When packaging using maven the docker container extracted is TAGed as follows:

> ${project.artifactId}-${project.version}-${git.buildnumber}

where git.buildnumber corresponds to the following git evaluation:

> tag + "_" + branch + "_" + shortRevision + "_" + commitsCount

#Requirements
You need to have docker installed on your linux for this to run (https://docs.docker.com/installation/fedora/)

Quick setup

>$ sudo yum -y remove docker

>$ sudo yum -y install docker-io

>$ sudo yum -y update docker-io

>$ sudo systemctl start docker

>$ sudo systemctl enable docker

>$ sudo groupadd docker

>$ sudo chown root:docker /var/run/docker.sock

>$ sudo usermod -a -G docker $USERNAME

You need to have docker listening to a server socket:

>vi /etc/sysconfig/docker

and alter the OPTIONS var as follows:

OPTIONS='--selinux-enabled -H tcp://localhost:2375'

and restart docker:

>systemctl stop docker

>systemctl start docker

and check that 2375 listens:

>netstat -an |grep 2375

and export docker host env var for your docker client cmds:

>vi /etc/bashrc

at the end put:

>export DOCKER_HOST=tcp://localhost:2375 

You need to have a docker registry running:

>docker run -p 5000:5000 registry

Finally:

You need to have a jdk7 installed from oracle and set JAVA_HOME to the jdk location.
You need to have maven installed and M2_HOME variable pointing to your maven installation.
You need to have git installed on linux.

#General Usage Instructions
First clone from github the project:

>git clone https://github.com/jufis/docker-rest-server.git

>cd docker-rest-server

Run the following cmd to clean the project:

>mvn clean

Run the following cmd to build the project:

>mvn package

Run the following cmd to build the project and build the docker container:

>mvn package docker:build

Check the the docker container image is ok locally:

>docker images

Run the following cmd to start the container:

>mvn prepare-package docker:start

Check that the docker container is started:

>docker ps

Run the following cmd to stop the container:

>mvn prepare-package docker:stop

Check that the docker container stopped:

>docker ps

Run the following cmd to push container to our local private docker registry:

>mvn prepare-package docker:push

Finally run the following cmd to tail logs from all your running containers:

>mvn docker:logs -Ddocker.follow -Ddocker.logDate=DEFAULT -Ddocker.logAll=true

#Running the server in docker container manually
Enter the following commands to run the server container manually:

> docker run -i -t --rm --name docker-server net.jufis/docker-rest-server:GIT_TAG

You can find the GIT_TAG if you list the docker images with:

> docker images

Explanation of the last command:

* docker: our linux container
* run   : instructs to run the image 
* --name: the name (ie. docker-server) of our container for linking purposes between the client container. Allows network comms between containers using ssh.
* docker-rest-server-container: the name of our docker imager.

For more on linking containers see the following links:

<a href=https://docs.docker.com/userguide/dockerlinks/>https://docs.docker.com/userguide/dockerlinks/</a>
<br/>
<a href=https://docs.oracle.com/cd/E52668_01/E54669/html/section_rsr_p2z_fp.html>https://docs.oracle.com/cd/E52668_01/E54669/html/section_rsr_p2z_fp.html</a>

#Cleaning up all docker containers

To reset docker from all processes/images run this:

> docker stop $(docker ps -a -q)

>docker rm -f $(docker ps -a -q)

>docker rmi -f $(docker images -q)