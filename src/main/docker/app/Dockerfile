FROM dockerfile/java:oracle-java7

WORKDIR /

EXPOSE 8080

ADD ${project.artifactId}-${project.version}-${git.buildnumber}.jar .

CMD java -jar /${project.artifactId}-${project.version}-${git.buildnumber}.jar