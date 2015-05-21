#Sample docker rest server
This is a sample server app inheriting from spring boot to create a REST service in order to be used from the docker-rest-client repository.

It uses the rhuss excellent [docker-maven-plugin](https://github.com/rhuss/docker-maven-plugin "rhuss docker-maven-plugin") to build/start/stop/push/check logs of a docker image. 

It assumes you run a local private docker-registry running on port 443 over SSL wihout basic auth.

When packaging using maven *ci or prod* profiles the docker container extracted is TAGed as follows:

> ${project.artifactId}-${project.version}-${git.buildnumber}

where git.buildnumber corresponds to the following git evaluation:

> tag + "_" + branch


##Requirements
Here we have configured our pom to depend on a secured docker client to "talk" to a secure docker daemon which in turn "talks" to a private docker registry in order push our docker images with maven.

If you have not setup docker follow this link:

>https://github.com/jufis/docker/blob/master/docs/docker-installation.md

If you have not configure secure docker daemon follow this link:

>https://github.com/jufis/docker/blob/master/docs/docker-security.md

If you have not setup a private docker registry follow this link:

>https://github.com/jufis/docker/blob/master/docs/docker-registry.md

Finally:

You need to have a jdk7 installed from oracle and set JAVA_HOME to the jdk location.

You need to have maven installed and M2_HOME variable pointing to your maven installation.

You need to have git installed on your linux.


##Profiles

This pom uses profile to seperate for the following environments:

1. dev  = local development environment
2. ci   = continous integration
3. prod = production live environment

The *dev* profile is enabled by default. That causes the docker image to be created as follows in the docker-registry:

>registry.jufis.net:443/USERNAME-dev/docker-rest-server:latest

That will allow the developer to use his own repository eg. jufis-dev inside the docker registry and not conflict his changes amongst other developers working on the same docker-registry on different features of the same image.

The *ci* profile can be enabled as follows:

>mvn ... -P ci

or

>mvn -Denvironment=ci ...

The *ci* profile guarantees that the docker image created is using GIT_TAG information so that the docker image gets created in the *ci* repository with the following format:

>registry.jufis.net:443/ci/docker-rest-server:GIT_TAG

<pre>
ATTENTION: if GIT nature is not found or GIT tag not exists package phase will fail on purpose with a characteristic msg.

If you create a TAG from master this tag will be used for building the image.
If after TAG creation on master you change a single file this TAG not exists anymore and the build will fail.
</pre>

The *prod* profile can be enabled as follows:

>mvn ... -P prod

or

>mvn -Denvironment=prod ...

The *prod* profile guarantees that the docker image created is using GIT_TAG information so that the docker image to be created in the *prod* repository as follows:

>registry.jufis.net:443/prod/docker-rest-server:GIT_TAG

<pre>
ATTENTION: if GIT nature is not found or GIT tag not exists package phase will fail on purpose with a characteristic msg.

If you create a TAG from master this tag will be used for building the image.
If after TAG creation on master you change a single file this TAG not exists anymore and the build will fail.
</pre>


##General Usage Instructions

First clone from github the project:

>git clone https://github.com/jufis/docker-rest-server.git

>cd docker-rest-server

Run the following cmd to clean-up the project:

>mvn clean

<pre>
NOTE: this phase also calls docker:remove with -Ddocker.removeAll in order to remove any pre-built docker image.

In cases that you have local dev/ci/prod mixture of images clean doesn't remove image due to dependencies; look at the very bottom of this readme in order to flush all docker images.
</pre>

Run the following cmd to complile the project:

>mvn compile

<pre>
NOTE: this phase doesn't build the docker image.
</pre>

Run the following cmd to build the project:
 
>mvn package

<pre>
NOTE: this phase also calls docker:build in order to build the docker image.
</pre>

Run the following cmd to deploy:

>mvn deploy

<pre>
NOTE: this phase also calls docker:push in order to push the image to the private docker registry automatically.
</pre>

Check the the docker container image is ok locally:

>docker images

Run the following cmd to start the container:

>mvn prepare-package docker:start

Check that the docker container is started:

>docker ps

Run the following cmd to tail logs from all your running containers:

>mvn docker:logs -Ddocker.follow -Ddocker.logDate=DEFAULT -Ddocker.logAll=true

Run the following cmd to stop the container:

>mvn prepare-package docker:stop


##Running the server in docker container manually
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


##Cleaning up all docker containers

To reset docker from all processes/images run this:

>docker stop $(docker ps -a -q)

>docker rm -f $(docker ps -a -q)

>docker rmi -f $(docker images -q)