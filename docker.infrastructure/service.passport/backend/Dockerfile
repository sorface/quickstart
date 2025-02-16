FROM ubuntu as source_code

WORKDIR /source

ARG GITHUB_BRANCH

RUN apt-get update && \
    apt-get install -y git && \
    git clone https://github.com/sorface/passport.git -b $GITHUB_BRANCH

FROM maven:3.9.9-eclipse-temurin-21 as build

COPY --from=source_code /source/passport/ .

ARG MAVEN_PROFILE

RUN mvn clean package -P $MAVEN_PROFILE

FROM eclipse-temurin:21

ARG JAR_FILE=/web/target/*.jar

COPY --from=build ${JAR_FILE} app.jar

ENTRYPOINT ["java", "-jar", "/app.jar"]