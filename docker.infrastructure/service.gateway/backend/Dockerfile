FROM ubuntu as source_code

WORKDIR /source

ARG GITHUB_BRANCH

RUN apt-get update && apt-get install -y git && git clone https://github.com/sorface/gateway.git -b $GITHUB_BRANCH

FROM maven:3.9.9-eclipse-temurin-21 as build

COPY --from=source_code /source/gateway/ .

ARG MAVEN_PROFILE

RUN mvn clean package

FROM eclipse-temurin:21-alpine

ARG JAR_FILE=/target/*.jar

COPY --from=build ${JAR_FILE} app.jar

ENTRYPOINT ["java", "-jar", "/app.jar"]