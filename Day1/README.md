# Day 1 Lab



## Let us Start,
 - Install docker on amazon linux
 * Verify the docker version
 + Docker search
  Run a container
  Verify the container status with docker ps command with it's diffrent switches
  Stop and remove the container   

## 2. Installing Docker on Amazon Linux instructions on how to install Docker on Amazon Linux:

Update the installed packages and package cache on your instance
```bash
yum update -y
```

Install the most recent Docker Community Edition package
```bash
amazon-linux-extras install docker`
```
Start the Docker service
```bash
service docker start
```
Add the ec2-user to the docker group so you can execute Docker commands without using sudo
```bash
usermod -a -G docker ec2-user
```

## 3. Show the Docker Version, how to check the installed version of Docker:

Check Docker version
```bash
docker --version
```
## 4.Search for Docker images on Docker Hub using the docker search command. For example, to search for Ubuntu images:

Search for Ubuntu images
```bash
docker search ubuntu
```

## 5.How to run a container from an image:

Run a container
```bash
docker run -it ubuntu:latest /bin/bash
```
## 6. Listing Docker Containers; the docker ps command and its options -a and -q:

List all running containers
```bash
docker ps
```
List all containers, even those not running
```bash
docker ps -a
```
List all container IDs
```bash
docker ps -q
```
## 7. How to stop a Docker container:

Stop a container
```bash
docker stop <container_id>
```
## 8. How to remove a Docker container:

Remove a container
```bash
docker rm <container_id>
```
Remove all containers
```bash
docker rm $(docker ps -aq)
```