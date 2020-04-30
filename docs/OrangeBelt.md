# Digging in | Orange Belt

Lets aim to make this exercise the bones of something useful that you can use or leverage at work in real life.  
Lets aim to host a web app in a docker container that is set up using good practices, that you will understand. Let try and do that in under 15 minutes. Cool? Cool.

The contents of the web application are not overly important, provided it is real enough to show what obstacles we will hit in the real world.  
Aims of this course is to have basics understanding of the following in the context of building a production ready docker image hosting a trivial Web application:  

1. The `docker run` command - See above
1. The `docker build` command - See above
1. What a `Dockerfile` is
1. What the basic instructions are in a Docker file including `FROM`, `LABEL`, `RUN`, `COPY`, `ENV`, `EXPOSE`, `WORKDIR`, `USER`, `ENTRYPOINT` and `CMD`
1. What a docker context is and how it affect our process
1. what a `.dockerignore` file is
1. What layering is and why it is such a powerful concept in Docker and how to consider layer when writing a Dockerfile
1. The Builder Pattern in docker files
1. Revisit the `docker run` command, now with more detail

## Create an App to host (not Docker related)

We will use `Node` to quickly create an app that we can host. We will use `express` as it is well understood and requires us to have managed local dependencies (i.e. `node_modules`).

Let us create a src folder (`mkdir src`) and start working in it (`cd src`).

Lets initialize a Node app by creating a `package.json` file using the command `npm init`. NB I will be using node 14 so if you are using nvm you may want to run `nvm install 14` and `nvm use 14`.
Accept the defaults from the npm init command and you should have a basic `package.json` file created in the `src` folder. Now lets install Express by running `npm install express --save`. You should see the Express dependency added to your `package.json` file.
Now create a file named `index.js` in the same `src` directory with the following contents:

```node
const express = require('express')
const app = express()
const port = 80

app.get('/', (req, res) => res.send('Hello brave new Node World!'))

app.listen(port, () => console.log(`Example app listening at http://localhost:${port}`))

```

Save the file and test that it works by running `node index.js`. We should see the terminal showing `Example app listening at http://localhost:80` and if we go to http://localhost:80 on a browser we should see "Hello brave new Node World!" displayed. If you have an error it is likely another process is using port 80. We now have a rudimentary Node app that runs. Now lets show how we can host this in docker.

## Hosting Web Apps in Docker

Now that we have our app, lets make sure that this app will run on any machine that has docker installed, even if Node is not!
First lets create a file in `src` called `Dockerfile`.
In this file lets add the following contents:

```docker
FROM node:latest

COPY ./ ./
RUN npm install

EXPOSE 80
CMD node index.js
```

Save that file then run:  
`docker build -f Dockerfile -t funwithdockernode:latest .`  
Quick note here: The `.` at the end of the command is important as it is the "context", more on that later. We now should have a built docker image containing our app.  
Lets now run it.  
`docker run --publish 8000:80 funwithdockernode:latest`

Now lets view the output on port 8000 i.e. go to your browser and enter the address `http://localhost:8000`. I have chosen a different port to show that this is in fact docker running not the original app we previously ran.

A lot just happened so lets walk thru what we did and some issues that have arisen here.

First lets look at the `build` command.  
The first argument we pass in is the file we are building, i.e. `-f Dockerfile`. Nothing too special here, however it can be noted that docker will look for a file in the current path call "Dockerfile" if a file argument is not supplied. Next we tag the image so we can refer to it later when we want to run it `-t funwithdockernode:latest`. Lastly we tell docker that the current path can be considered its context, i.e. the `.` at the end of the line. All files in this path will be passed to the build context. We will cover what this means soon.

Next lets look at the `run` command.  
The first argument we pass is the port we want to map from the local host to the container. I prefer the port used in code of my http apps to use port 80, as that's what that port is for by convention. However we can not have many apps doing that on the same machine. Docker nicely fixes that by allowing our apps to all be port 80 running *inside* their containers and mapping each of those containers at runtime to different port number on our host. Now we don't need magic port numbers within our Node Express (or any other http based) apps. In this instance we have mapped the port 8000 on our localhost to the port 80 on the container.  
We have also specified the name of an image to run, namely `funwithdockernode:latest`. This is the tag we applied in the build. Each time we build we define at least one tag, wiht the latest build overwriting any previous build with that same name.  

Next: lets go thru some key words in our `Dockerfile`:  

## Docker File Basic Instructions

`FROM` - this lets us start from a known state, in this case the well known image `node:latest` (https://hub.docker.com/_/node). The `FROM` instruction allows us to pick up from where someone else left off allowing us to build "layers" on top of it. Its a hugely powerful part of docker. Each instruction builds a "layer" on top of the the previous instruction and if those layers are all the same as previous builds it can be aggressively cached. With `FORM node:latest` I know I have a clean OS with Node installed ready to build my own layers on top of.  

`COPY` - allows us to bring files from the "docker context" into the image. You will commonly see `.` at the end of `docker build` commands.  That `.` means this directory (i.e. `pwd`) is to be used as the "context", yup, its a path. Don't leave it out in your build statements!  We have to explicitly bring in files from the build context and we can only operate within the build context, ie we cant traverse back in the file system using paths like `../../MyFileInAgreandparentFolder.txt`. If its not in the build context we can not copy it to the build image. Also remember every file you copy from the context into the image is baked into the image. So try to avoid secrets and unnecessary noise like `./.git/` folders etc. It is also important to note that changes to the files copied (i.e. between builds) affect the ability for docker to cache your layers. For this reason is it common to move the files that rarely change closer to the top of a Dockerfile and fast moving files (like application code) nearer the bottom. Any cache invalidation at one layer affect all the following instructions, as such poor organization of your docker file can lead to unnecessarily slow builds.

`RUN` - allows us to execute a command in the container that typically adjusts the state of the container i.e. installing something or configuring something.  For Node apps this is likely yarn/npm install, build, test, lint etc. It is also very common for people to use `apt-get` (or similar) to do mass updates of system level installs. I am not a huge fan of this unless you are building a versioned container that will be built upon, i.e. not a app container. For example, commands like below update the container to get functionality we want, but in a manner that puts us in an unknown state, i.e. we don't know what versions are installed

```docker
RUN apt-get update
RUN apt-get install -y curl
```

IMO - avoid this unless you are building a slow moving image that is serving as a *base* layer - Not a constantly changing application like we are building.

`EXPOSE` - allows us to expose a port. This makes it explicit that this port should be exposed and without doing this we can not actually get access to what the web application is serving us. Agina i prefer to expose port 80 here and map at runtime to something else on the host if need be.

`CMD` Finally we run the software contained in your image, along with any arguments by calling CMD. You should only have one CMD instruction per file and it should be at the end fo the file. Syntax for CMD has some quirks, I suggest reading DOcker docs for more info after completing this tutorial.
This instruction is the key part of the app and container and it allows us to just run the container and the app spins up by itself.



TODO




^^^^^^^^^ HERE ^^^^


Fist we have decide to base our our docker image on the default Node image. Which at the time of writing this is node 14 on the debian stretch os (Debian 9). The issue we have here is by the time you run this "latest" will not be what i had run. It could be node 16 on Debian 10.. wil my app even work on that?. Unfortunately we have code that over time will run and have a different effect. To me this defeats a significant benefit of hosting my apps in Docker is that i have predictable, immutable deployments.

Lets fix that. Change that line from
`FROM node:latest` to `FROM node:14.0.0-stretch`. We are now explicit about the version of node and the version of the OS

Next up we copy everything in the docker context (we will cover this soon) in to the image's working directory. At the moment that includes our `index.js`, `package.json`, the generated files `package-lock.json` and our `node_modules` folder and its contents.  
Sounds ok, but actually is not good. What we want to do is have a consistent build, but we just copied the node modules on our local machine, the things we want to be consistent with, into the new clean docker image. Even though that may sounds like a good idea it again breaks a tenant of _why_ we use docker. My machine could be subtlety different to yours. I may be running node 6 and you running node 14, but we run node 12 in production. When I run `npm install` on my node 6 machine and you run `npm install` on your node 14 machine for the same package file we will get different outputs. Not ideal. We want to avoid this by leaning on docker and its ability to define a well know environment. So lets change that.

Update your docker file by changing `COPY ./ ./` to:  

```docker
COPY package.json ./
COPY package-lock.json ./
COPY index.js
```
now when the `RUN npm install` line is hit we will pull down and/or the necessary packages based on this environment in a consistent manner, no matter what host machine is running.

So now our docker file should look something like this:
```docker
FROM node:14.0.0-stretch

COPY package.json ./
COPY package-lock.json ./
COPY index.js

RUN npm install

EXPOSE 80
CMD node index.js
```

Upside to what we have:

1. Simple
1. using well know "stuff" like Debian, node and port 80 for http
1. Will build a consistent output, not dependant on the host machine

Downsides:

1. Images are bigger than need to be
1. Not making great use of caching so builds are slower than necessary
1. Not super secure due to using root user
1. initial build may get slow over time as context grows
1. We run locally but cant get out... the terminal is stuck.





[Previous - Orange Belt](./OrangeBelt)  
[Next - Green Belt](./GreenBelt)  
[About the Belt System](./BeltSystem)  
[Home](./) 