# docker-various-examples

A repository with some interesting examples of cool docker things

# Generate a Java project, build and run it

First go to the java directory in this project:

`cd java`

Now build the docker image:

`docker build . -t maven-build`

(not sure why I am using maven-build as a name but too lazy to go change it now)

The Dockerfile uses dockers [multistage build](https://docs.docker.com/develop/develop-images/multistage-build/) feature to pull a generated project from [start.spring.io](https://start.spring.io/), unpacks it and then copies the code into anther container that has maven and java installed. The project will then build and be run.

Now you can run the container and mount a volume to edit the code on your machine.

Let's say you want to store your code in the java project of this repo under another folder called app.

From the java directory:

`mkdir -p app && cd "$_"`

Create a named volume for the current directory (using mavendev here but feel free to call it something else):

`docker volume create -d local -o type=none -o o=bind -o device=$(pwd) mavendev`

Take a second to look at the volume you just created:

```
$ docker volume ls
DRIVER              VOLUME NAME
local               mavendev
```

You can inspect it to make sure you are happy with the directory setup. Device is the directory we want to bind into our container soon. Basically we want to share a directory between our machine and the container. Make sure this directory is empty on your machine.

```
$ docker volume inspect mavendev
[
{
"CreatedAt": "2019-02-06T19:18:44Z",
"Driver": "local",
"Labels": {},
"Mountpoint": "/var/lib/docker/volumes/mavendev/_data",
"Name": "mavendev",
"Options": {
"device": "/Users/ruairi/Dev/docker-various-examples/java/app",
"o": "bind",
"type": "none"
},
"Scope": "local"
}
]
```

Now we can run the container and use the volume we created:

`docker run --rm -it -v mavendev:/usr/app -p 8080:8080 maven-build sh`

This opens a shell in the container.
You can run the app with:

`./mvnw spring-boot:run`

You should now be able to access the running application at <http://localhost:8080>

You will just see a Whitelabel Error Page since we haven't setup any routes yet but that still shows the app is running and accessible.

Notice the java code from the docker image is now in java/app.

**Next steps**

Check out [this post on using docker for local development](http://www.realgorithm.io/2018/04/simple-dev-environment-with-docker-compose/)

[Play with docker](https://labs.play-with-docker.com/)

# Generate a React project, build and run it

First go to the javascript directory in this repository:

`cd javascript`

Build the docker image:

`docker build . -t react-build`

This will generate a react app for you.

You may have to ctrl+c to exit after generation is complete.

Let's create a directory for our app code.

From the javascript directory:

`mkdir -p app && cd "$_"`

Create a named volume for the current directory (using reactdev here but feel free to call it something else):

`docker volume create -d local -o type=none -o o=bind -o device=$(pwd) reactdev`

Take a second to look at the volume you just created:

```
$ docker volume ls
DRIVER              VOLUME NAME
local               reactdev
```

You can inspect it to make sure you are happy with the directory setup. Device is the directory we want to bind into our container soon. Basically we want to share a directory between our machine and the container. Make sure this directory is empty on your machine.

```
$ docker volume inspect reactdev
[
{
"CreatedAt": "2019-02-06T19:22:50Z",
"Driver": "local",
"Labels": {},
"Mountpoint": "/var/lib/docker/volumes/reactdev/_data",
"Name": "mavendev",
"Options": {
"device": "/Users/ruairi/Dev/docker-various-examples/react/app",
"o": "bind",
"type": "none"
},
"Scope": "local"
}
]
```

Now we can run the container and use the volume we created:

`docker run --rm -it -v reactdev:/usr/app/my-app -w /usr/app/my-app -p 3000:3000 react-build sh`

Now you can run `yarn start`

And access the app on <http://localhost:3000>
