---
layout: post
title: "Docker Cheat Sheet"
author: alfred
categories: [docker, cheat sheet]
featured: false
hidden: false
toc: true
---

Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly.

Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allow you to run many containers simultaneously on a given host. Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host.

<!--more-->

### References

[Docker Documentation](https://docs.docker.com/)

### Docker Commands

```sh
docker login <docker registry> --username <user email> --password <token>

docker build --rm  -t <application>:latest .
docker run --rm -d -p 4200:80/tcp <application>:latest

docker container ls
docker container stop <container id>

# Stop all containers
docker stop $(docker ps -a -q)

# Remove all stopped containers
docker rm $(docker ps -a -q)
```
