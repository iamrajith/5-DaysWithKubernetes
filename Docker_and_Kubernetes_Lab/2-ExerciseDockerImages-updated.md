# Exercise: Docker Images (Apache & Nginx)

## Table of Contents
1. [Introduction](#introduction)
2. [Exercise 1: Apache Server on Ubuntu](#exercise-1-apache-server-on-ubuntu)
3. [Exercise 2: Nginx Server on Rocky Linux](#exercise-2-nginx-server-on-rocky-linux)
4. [Notes](#notes)
5. [References](#references)

---

## Introduction
This lab demonstrates Docker image creation using `docker commit`.  
We will create two images:
- Apache server on Ubuntu
- Nginx server on Rocky Linux (replacement for CentOS)

⚠️ **Important:** Docker containers do not run `systemd` or init scripts. Services must be started in the foreground as the container’s main process.

---

## Exercise 1: Apache Server on Ubuntu

1. Pull the Ubuntu image:
   ```bash
   docker pull ubuntu:latest
   ```

2. Run a container:
   ```bash
   docker run -it ubuntu:latest /bin/bash
   ```

3. Inside the container, install Apache:
   ```bash
   apt-get update -y
   apt-get install -y apache2
   ```

4. Exit the container:
   ```bash
   exit
   ```

5. Commit the container:
   ```bash
   docker commit <container_id> my-apache-app
   ```

6. Run Apache in the foreground:
   ```bash
   docker run -d -p 8080:80 my-apache-app /usr/sbin/apache2ctl -D FOREGROUND
   ```

---

## Exercise 2: Nginx Server on Rocky Linux

> CentOS images are deprecated. Use Rocky Linux as a drop-in replacement.

1. Pull Rocky Linux:
   ```bash
   docker pull rockylinux:9
   ```

2. Run a container:
   ```bash
   docker run -it rockylinux:9 /bin/bash
   ```

3. Inside the container, install Nginx:
   ```bash
   dnf install -y nginx
   ```

4. Exit the container:
   ```bash
   exit
   ```

5. Commit the container:
   ```bash
   docker commit <container_id> my-nginx-app
   ```

6. Run Nginx in the foreground:
   ```bash
   docker run -d -p 8081:80 my-nginx-app nginx -g "daemon off;"
   ```

---

## Notes
- Containers should run **one main process**.  
- Avoid `systemctl` or `service` inside containers.  
- Use foreground options:
  - Apache: `apache2ctl -D FOREGROUND`
  - Nginx: `nginx -g "daemon off;"`

---
