
# Exercise Docker Commit

## Table of Contents

1. [Introduction](#introduction)
2. [Exercise 1: Apache Server on Ubuntu](#exercise-1-apache-server-on-ubuntu)
   - [Docker Commit](#docker-commit-apache)
3. [Exercise 2: Nginx Server on CentOS](#exercise-2-nginx-server-on-centos)
   - [Docker Commit](#docker-commit-nginx)

## Introduction

This lab will take you through the Docker image creation by "Docker commit"; Let us create two Docker images:
- One for an Apache server running on Ubuntu
- Another is for an Nginx server running on CentOS

## Exercise 1: Apache Server on Ubuntu

First, pull the Ubuntu image from Docker Hub:

```bash
docker pull ubuntu:latest
```

Then, run a Docker container from the Ubuntu image:

```bash
docker run -it ubuntu:latest /bin/bash
```

Inside the Docker container, update the system with the latest patches and install Apache:

```bash
apt-get update -y
apt-get install -y apache2
```

Start the Apache service and enable it to autostart:

```bash
service apache2 start
update-rc.d apache2 defaults
```

Then, exit the Docker container and commit the changes to a new Docker image:

```bash
exit
docker commit <container_id> my-apache-app
```

Finally, run a Docker container from the new image:

```bash
docker run -p 8080:80 my-apache-app
```

## Exercise 2: Nginx Server on CentOS

First, pull the CentOS image from Docker Hub:

```bash
docker pull centos:latest
```

Then, run a Docker container from the CentOS image:

```bash
docker run -it centos:latest /bin/bash
```

Inside the Docker container, update the system, install EPEL Release, and install Nginx:

```bash
yum -y update
yum install -y epel-release
yum install -y nginx
```

Start the Nginx service and enable it to autostart:

```bash
systemctl start nginx
systemctl enable nginx
```

Then, exit the Docker container and commit the changes to a new Docker image:

```bash
exit
docker commit <container_id> my-nginx-app
```

Finally, run a Docker container from the new image:

```bash
docker run -p 8080:80 my-nginx-app
```

## Note

The above exercises show how to use `service` and `systemctl` commands in Docker commit. However, it's important to note that Docker containers are designed to run one process per container. Therefore, it's not recommended to use `systemctl` or `service` to manage services within a container as it goes against this principle. Instead, it's better to run the service directly in the foreground. For Apache and Nginx, this can be done by using the `-D FOREGROUND` option for Apache and the `-g "daemon off;"` option for Nginx.

## References

1. [Docker commit reference](https://docs.docker.com/engine/reference/commandline/commit/)
2. [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
3. [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)

---

Please replace `<container_id>` with the ID of your Docker container. You can get the ID of the running containers by using the `docker ps` command.
