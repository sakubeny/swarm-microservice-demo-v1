FROM maven:3.8.4-jdk-7

WORKDIR /code

COPY vote-worker/pom.xml /code/pom.xml
RUN mvn dependency:go-offline

COPY vote-worker/src /code/src
RUN mvn package

CMD ["java", "-jar", "target/worker-jar-with-dependencies.jar"]
