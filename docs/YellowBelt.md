
# Dockerfile | Yellow Belt

## Dockerfiles and building your own image

A Dockerfile is a source controllable way of defining what you want you container to be and do. Lets make a Hello World image of our own.

In a new folder (eg `mkdir fun-with-docker`) create a file called `Dockerfile` (ie `cd fun-with-docker && touch Dockerfile`).
Open the file and add the following to it:

```docker
FROM ubuntu:latest

CMD echo hello brave new world
```

Save that file.

Lets build that file to make an image locally.
`docker build -f Dockerfile -t hellobravenewworld:latest .`

We should see that each line in the file has a corresponding output line and an ID associated to it (the layer) and some summary output showing the the image is built and tagged.
We can now run that image as a container (a container is a running versions of an image, like in OO code where an `object` is an instance of a `class`). Let us do that now:
`docker run hellobravenewworld:latest`
You will see that is is very fast to spin up the instance and produce the output. Think about what is happening here, we just defined a working environment based on Ubuntu*, spun up an instance of this image (complete with a fully fledged OS) and printed out some text and then closed down the the whole thing, in a matter about about a second. To do this with a VM would possible take minutes. Unfortunately what we have achieved is not actually very useful other than show casing what docker does.

[Previous - White Belt](./WhiteBelt)  
[Next - Orange Belt](./OrangeBelt)  
[About the Belt System](./BeltSystem)  
[Home](./)  
