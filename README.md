#Sample docker rest server
This is a sample app inheriting from spring boot to create a REST service in order to be used from the docker-rest-client repository.

It uses the spotify [docker-maven-plugin](https://github.com/spotify/docker-maven-plugin "docker-maven-plugin") to create a docker image on package mvn target.Currently it doesn't push to a remote repository but this can be easily done by updating the pom.xml.

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
Enter the following command to run the server container:

> docker rm -f docker-server
> docker run --name docker-server docker-rest-server-container

Explanation of the last command:

* docker: our linux container
* run   : instructs to run the image 
* --name: the name (ie. docker-server) of our container for linking purposes between the client container. Allows network comms between containers using ssh.
* docker-rest-server-container: the name of our docker imager.

For more on linking containers see the following links:

<a href=https://docs.docker.com/userguide/dockerlinks/>https://docs.docker.com/userguide/dockerlinks/</a>
<br/>
<a href=https://docs.oracle.com/cd/E52668_01/E54669/html/section_rsr_p2z_fp.html>https://docs.oracle.com/cd/E52668_01/E54669/html/section_rsr_p2z_fp.html</a>