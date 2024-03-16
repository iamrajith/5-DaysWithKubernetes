# Exercise Dockerfile

## Table of Contents

1. [Introduction](#introduction)
2. [Exercise 1: Apache Server on Ubuntu](#exercise-1-apache-server-on-ubuntu)
   - [Dockerfile](#dockerfile-apache)
   - [Apache Configuration File](#apache-config)
   - [Docker Command](#docker-command-apache)
3. [Exercise 2: Nginx Server on CentOS](#exercise-2-nginx-server-on-centos)
   - [Dockerfile](#dockerfile-nginx)
   - [Nginx Configuration File](#nginx-config)
   - [Docker Command](#docker-command-nginx)
4. [Dockerfile vs Docker Commit](#dockerfile-vs-docker-commit)

## Introduction

This lab will take you through the Docker image creation with "Dockerfiles"; Let us create two Docker images:
- One for an Apache server running on Ubuntu
- Another is for an Nginx server running on CentOS

## Exercise 1: Apache Server on Ubuntu

First, create a new directory for the Apache server setup:

```bash
mkdir apache-server
cd apache-server
```

Then, create a Dockerfile:

```bash
vi Dockerfile
```

Insert the following content into the Dockerfile:

```Dockerfile
# Use an official Ubuntu runtime as a parent image
FROM ubuntu:latest

# Update the system with the latest patches
RUN apt-get update -y

# Install Apache
RUN apt-get install -y apache2

# Copy local configuration file into the Docker image
COPY ./my-httpd.conf /etc/apache2/sites-enabled/000-default.conf

# Expose port 80
EXPOSE 80

# Start Apache service
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
```

Next, create an Apache configuration file (`my-httpd.conf`):

```bash
vi my-httpd.conf
```

Insert the following content into the `my-httpd.conf` file:

```apache
<VirtualHost *:80>
    DocumentRoot /var/www/html
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

Finally, build and run the Docker container using the following commands:

```bash
docker build -t my-apache-app .
docker run -p 8080:80 my-apache-app
```

To stop and remove the Docker container, use the following commands:

```bash
docker stop my-apache-app
docker rm my-apache-app
```

Go back to the parent directory to get ready for the Exercise 2:

```bash
cd ..
```

## Exercise 2: Nginx Server on CentOS

First, create a new directory for the Nginx server setup:

```bash
mkdir nginx-server
cd nginx-server
```

Then, create a Dockerfile:

```bash
vi Dockerfile
```

Insert the following content into the Dockerfile:

```Dockerfile
# Use an official CentOS runtime as a parent image
FROM centos:latest

# Update the system with the latest patches
RUN yum -y update

# Install EPEL Release
RUN yum install -y epel-release

# Install Nginx
RUN yum install -y nginx

# Copy local configuration file into the Docker image
COPY ./my-nginx.conf /etc/nginx/nginx.conf

# Expose port 80
EXPOSE 80

# Start Nginx service
CMD ["nginx", "-g", "daemon off;"]
```

Next, create an Nginx configuration file (`my-nginx.conf`) using the `vi` command:

```bash
vi my-nginx.conf
```

Insert the following content into the `my-nginx.conf` file:

```nginx
worker_processes 1;

events { worker_connections 1024; }

http {
    server {
        listen 80;
        location / {
            root /usr/share/nginx/html;
            index index.html index.htm;
        }
    }
}
```

Finally, build and run the Docker container using the following commands:

```bash
docker build -t my-nginx-app .
docker run -p 8080:80 my-nginx-app
```

To stop and remove the Docker container, use the following commands:

```bash
docker stop my-nginx-app
docker rm my-nginx-app
```

## Dockerfile vs Docker Commit

While `docker commit` can be useful for creating Docker images, using a Dockerfile is generally considered a better practice for several reasons:

1. **Reproducibility**: Dockerfiles are structured ways to build an image. We can reproduce the image anywhere provided that has Docker installed.
2. **Version Control**: Due to its structured format, the Dockerfile enables easy code review and version control, whereas docker commit does not.
3. **Documentation**: The Dockerfile is self-documented and easy to follow, making it simple to reference in the future.
4. **Automation and Integration**: Dockerfiles can be easily integrated with automated build systems, such as CI/CD pipelines, to create a new image whenever the Dockerfile is updated.

In contrast, `docker commit` doesn't provide insight into the steps required to create the image. It makes it harder to reproduce the image.
`docker commit` is helpful for quick changes. But not for building consistent and reproducible Docker images. For that, Dockerfiles are a more reliable and flexible solution.

## References

1. [Dockerfile reference](https://docs.docker.com/engine/reference/builder/)
2. [Best practices for writing Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)
3. [Docker commit reference](https://docs.docker.com/engine/reference/commandline/commit/)

