FROM jamesdbloom/docker-java7-maven

#RUN apt-get update -qq && apt-get install -y maven && apt-get clean

WORKDIR /code

ADD vote-worker/pom.xml /code/pom.xml
RUN ["mvn", "dependency:resolve"]
RUN ["mvn", "verify"]

# Adding source, compile and package into a fat jar
ADD vote-worker/src /code/src
RUN ["mvn", "package"]

CMD ["/usr/lib/jvm/java-7-openjdk-amd64/bin/java", "-jar", "target/worker-jar-with-dependencies.jar"]
