FROM ubuntu as source_code

WORKDIR /source

RUN apt-get update && \
    apt-get install -y git && \
    git clone https://github.com/sorface/passport.git

FROM maven:3.8.7-eclipse-temurin-19 as build

COPY --from=source_code /source/passport/ .

ARG ACTIVE_PROFILE=docker

RUN mvn clean package -P run.context.$ACTIVE_PROFILE

FROM eclipse-temurin:19

ARG JAR_FILE=/web/target/*.jar

COPY --from=build ${JAR_FILE} app.jar

ENTRYPOINT ["java", "-jar", "/app.jar"]