# Getting started | White Belt

We know docker containers are like VMs, in that Docker allows us run code in a consistent environment with a known set up allowing us to have predictable results wherever it is run. The plus side is it is much faster and more light weight than a VM.  
To start, lets do the "step one" of any tutorial and kick off "hello world" with Docker.  
In a terminal enter the following:

`docker run ubuntu:latest echo Hello, world!`

If this is the first time running this or pulling down the latest version of Ubuntu in Docker you will see some output stating that it can not find the image locally (`Unable to find image 'ubuntu:latest' locally`) and then a series of progress statement where it pulls down layers with some summary lines. Finally we should see the statement `Hello, world!` printed to the output.  
What has happened? We ask `docker` to `run` a known image called `ubuntu` and we wanted the image that has been tagged with `latest` (Ubuntu likely has many versions, we are asking for the latest, which by convention is tagged with the name "latest").  
We then are passing in to the container the command to execute `echo Hello, world!`.
Once the Ubuntu image has been downloaded (by default from https://hub.docker.com/) in a series of layers (we will get to that soon) we fire up the image as a container and pass in the final text as a command.

Clear as mud? Thats ok for now hopefully as we move forward steps will be come clearer.

[Next - Yellow Belt](./YellowBelt)  
[About the Belt System](./BeltSystem)  
[Home](./)  
