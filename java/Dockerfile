# Generator: a very basic docker image with just enough to pull down a generated java project and unpack it.
FROM alpine:3.10 as generator

WORKDIR /usr/gen/

RUN apk add --no-cache wget unzip \
    && wget -O demo.zip 'http://start.spring.io/starter.zip?type=gradle-project&language=java&bootVersion=2.1.2.RELEASE&baseDir=demo&groupId=com.example&artifactId=demo&name=demo&description=Demo+project+for+Spring+Boot&packageName=com.example.demo&packaging=jar&javaVersion=1.8&autocomplete=&generate-project=&style=devtools&style=web' \
    && unzip demo.zip \
    && rm demo.zip

# Builder: Comes with maven to allow us to build and run the application code copied over from generator
FROM openjdk:12-alpine as builder

RUN mkdir /usr/app
WORKDIR /usr/app

COPY --from=generator /usr/gen/demo .

RUN ./gradlew build

