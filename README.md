#Sample docker rest server
This is a sample app inheriting from spring boot to create a REST service in order to be used from the docker-rest-client repository.

It uses the alexecollins [docker-maven-plugin](https://github.com/alexec/docker-maven-plugin "docker-maven-plugin") to create a docker image on package mvn target. 

It assumes a local docker-registry running on port 5000.

When packaging using maven the docker container extracted is TAGed as follows:

> ${project.artifactId}-${project.version}-${git.buildnumber}

where git.buildnumber corresponds to the following git evaluation:

> tag + "_" + branch + "_" + shortRevision + "_" + commitsCount

#Requirements
You need to have docker install on your linux for this to run (https://docs.docker.com/installation/fedora/)

Quick setup

>$ sudo yum -y remove docker

>$ sudo yum -y install docker-io

>$ sudo yum -y update docker-io

>$ sudo systemctl start docker

>$ sudo systemctl enable docker

>$ sudo groupadd docker

>$ sudo chown root:docker /var/run/docker.sock

>$ sudo usermod -a -G docker $USERNAME

#Build
Run the following cmd to build the server and the docker container all together:

> mvn clean package

List docker image:

>docker images

#Running the server in docker container
Enter the following commands to run the server container:

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

#Clean up all docker containers

To reset docker from all processes/images run this:

> docker stop $(docker ps -a -q)

>docker rm $(docker ps -a -q)

>docker rmi $(docker images -q)