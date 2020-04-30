# The Belt System

Here at *SenpaiSessions* we use a belt system to clearly show what our learning goals are for each course. The course are aimed to provide a generalist a solid understanding of a topic so they can be confident in daily use and have a foundation for further self managed learning. We will not get you to an expert level by doing this course, that is not the goal.

| Skill Level        | Belt Level|
| ------------- |-------------:|
|Heard of it                       |WHITE BELT   |
|Aware of it, can talk about it    |YELLOW BELT  |
|Could do it                       |ORANGE BELT  |
|Can do it well repeatedly         |GREEN BELT *  |
|Others come to for help           |BLUE BELT    |
|Active in the community           |PURPLE BELT  |
|Proactive in the community        |BROWN BELT   |
|Field leader                      |BLACK BELT   |

## Learning Docker - Goals

### WHITE BELT

- Be able to consume docker images using the `docker run` cli command

### YELLOW BELT

- Be able to write a basic `Dockerfile`, understanding the `FROM`, `RUN` and `CMD` instructions
- Run output of that build

### ORANGE BELT

- Be able to write a `Dockerfile` hosting a [Node](https://nodejs.org/en/) [Express](https://expressjs.com/) web application understanding the `COPY`, `ENV`, `ENTRYPOINT` and `EXPOSE` instructions
- Run output of that build with port mapping.
- Understand the docker build context and how to have a clean context byt limiting scope and using `.dockerignore`

### GREEN BELT

- Understanding Dockers layering and allow for effective caching on build and run
- Run many containers together using Docker Compose or networking

### BLUE, PURPLE, BROWN BLACK

You are on your own. Spread your wings and Fly! The best place to start is likely the [Docker docs](https://docs.docker.com/), specifically the [best practices](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/).
