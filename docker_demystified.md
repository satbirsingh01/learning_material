# Docker Demystified

## What is Docker?

Docker is a cross-platform application that creates and runs isolated environments known as containers.

### Containers

Containers are similar to virtual machines in that they run an isolated environment on a host machine. However, instead of having a complete operating system of their own, containers use the resources of the host machine and cannot function as a complete and independent computer. Docker containers do not have a hypervisor layer between them and the OS like VMs do.

### Why Use Docker?

Docker allows you to create lightweight environments for app deployment that can be hosted on any operating system with the container environment remaining the same. This streamlines the deployment process as you only have to configure the app to work in its container rather than a variety of linux distributions and windows systems.

Working in multiple containers that can be connected together allows for clean modular app design.

Very easy and quick to spin up and destroy containers compared to VMs.

## Installing Docker

### Windows 10

You will need to download [Docker Desktop](https://www.docker.com/products/docker-desktop/) and run the installerthrough a powershell terminal:

`Start-Process 'Docker Desktop Installer.exe' -Wait install`

If your admin account is different to your user account, you must add the user to the docker-users group:

`net localgroup docker-users [user] /add`

Start Docker Desktop by searching in the Windows Menu

### Linux

```
sudo apt update
sudo apt install docker.io
```

You can check Docker installed correctly with:

`sudo docker run hello-world`

## Images

Before a container can be run, there has to be an image to build it from (a snapshot of the base files etc). If an image is stored in the [Docker Hub registry](https://hub.docker.com/) then that image can be pulled to the local machine using:

`docker pull [image]:[version]`

To see a list of all the images on the system:

`docker images` or `docker image ls`

To remove an image:

`docker image rm [image]`

To remove all unused images:

`docker image prune`

To tag an image:

`docker image tag [image_id/name] [target_image]/[tag]`

Example:

`docker tag httpd fedora/httpd:version1.0`

To run an image as a container with an interactive terminal, so that multiple commands can be entered:

`docker run -it [image]`

### Docker Hub

To pull from the Docker Hub registry you will need to [make an account](https://hub.docker.com/signup) and log in.

When using Docker Desktop a sign in button will take you to a web portal to authenticate.

From the terminal the command is `docker login -u [username] [registry]`. Leave the registry blank to connect to the default Docker Hub (hub.docker.com). 

You can also request a personal access token from Docker Hub to use in place of a password. Go to your profile, click Account and Settings, Security, New Access Token. Give it a description, generate the token and you can paste it in when prompted for your password.

If a login command isn't working try restarting the docker daemon or also:

`docker login registry-1.docker.io/v1`

To push an image to the registry you first need to tag it and then push to your repository:

```
docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
docker push NAME[:TAG]

docker tag my_image myRegistry.com/myImage
docker push myRegistry.com/myImage
```

## Managing Containers
ps, image, prune, exec, run, rm, attach, export, kill, , rename, rmi, start, stop, 

To see which containers are running use:

`docker ps`

To see all containers including those that are stopped you will need the 'all' tag *-a*:

`docker ps -a`

## Creating Containers



## Building Your Own Image

### Dockerfile

In order to compile a project into a docker image, the project's root directory must have a Dockerfile, a file named Dockerfile with no extension. The instructions contained in this file are used by Docker to formulate an image.

Docker file example:
```
# 1. pick a Node 16 image
FROM node:16-alpine
# 2. set a working directory
RUN mkdir -p /charity/
WORKDIR /charity/
# 3. declare all the environemnt variables needed
ENV DATABASE_HOST=postgresCharity
ENV DATABASE_HOST=postgresCharity
ENV DATABASE_PORT=5432
ENV DATABASE_USERNAME=postgres
ENV DATABASE_PASSWORD=foo
ENV DATABASE_NAME=postgres
# 4. copy the app into the work directory
COPY . /charity/
# 5. install all the packages needed for NodeJS
RUN npm install
# 6. expose the port needed
EXPOSE 3000
# 7. start the app
CMD [ "npm", "start" ]
```

## Bind Mounts

## Deploying an Application Using Multiple Containers

## Docker Compose

## Docker Swarm

## Docker GUI

## Docker and Cloud Services

## Epilogue