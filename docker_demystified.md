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

## Building Your Own Image

### Dockerfile

In order to containerise an application with Docker, the project's root directory must have a Dockerfile, a file named Dockerfile with no extension. The instructions contained in this file are used by Docker to formulate an image.

A nice explanation of Dockerfile syntax can be found [here](https://medium.com/bb-tutorials-and-thoughts/docker-a-beginners-guide-to-dockerfile-with-a-sample-project-6c1ac1f17490).

Important Dockerfile commands, [full docs here](https://docs.docker.com/engine/reference/builder/):

```
# - Comments.

FROM - The base image your app will be built on .

CMD - Gives commands after the build stage, there should only be one CMD per Dockerfile.

WORKDIR - Working directory for the app.

ENV - Set environmental variables `ENV variable=value`.

COPY - Copy files from the host system to the container `COPY . .` copies all the current dir to the working directory in the container.

RUN - Runs terminal commands in the build stage against the image. For each run command Docker creates an intermediary image and runs each command as another layer above it. If you have an apt update and an apt install in run commands, make sure they are on the same line.

LABEL - Add label to image `LABEL ["label_name"]=["label_message"]

EXPOSE - Doesn't actually do anything but it tells the human reading it which ports to publish with the docker run -p 

USER - Username to run in, if not specified defaults to root

VOLUME - The specified directory will persist following the destruction of the container, remaining on the host system.

ARG - creates a variable for use in the Dockerfile

ADD - Adds files from host system or URLs to the container, can also extract compressed files. COPY is generally a safer option.

```

#### .dockerignore

Working in the same way as .gitignore files, add files or directories to a .dockerignore in the same directory as the Dockerfile so that they are not incorporated into the image.

#### Docker file example:

```
# 1. pick a Node 16 image
FROM node:16-alpine
# 2. set a working directory
RUN mkdir -p /exampleApp/ && \
    apk update
WORKDIR /exampleApp/
# 3. declare all the environmental variables needed
ENV DATABASE_HOST=postgresExampleApp
ENV DATABASE_PORT=5432
ENV DATABASE_USERNAME=postgres
ENV DATABASE_PASSWORD=foo
ENV DATABASE_NAME=postgres
# 4. copy the app into the work directory
COPY . /exampleApp/
# 5. install all the packages needed for NodeJS
RUN npm install
# 6. expose the port needed
EXPOSE 3000
# 7. start the app
CMD [ "npm", "start" ]
```

To then build an image of the project, move to the directory of the Dockerfile and run:

`docker build -t [image_tag] .`

The trailing full stop is necessary to select all the files in the directory.

## Creating Containers

`docker run -dp [host_port]:[container_port] [image_tag]`

`docker run -dp 3000:3000 exampleApp`

The *-d* tag runs the container in detached mode, in the background. The *-p* tag is the port. The Dockerfile example above exposes port 3000 in the container. The run command above then maps the container's port 3000 to the host machine's port 3000. *-i* will give you an interactive terminal

To ensure the container is not accessible from outside the machine, you have to bind the port to the host:

`docker run -p 127.0.0.1:3000:3000 exampleApp`

Use `docker ps` to check if your container is running.

## Managing Containers

### exec, run, rm, attach, export, kill, , rename, rmi, start, stop, 

To specify a container either use its name or id.

To see which containers are running use:

`docker ps`

To see all containers including those that are stopped you will need the 'all' tag *-a*:

`docker ps -a`

To create a container from an image:

`docker run [image]`

To start a container:

`docker start [container]`

To stop a container:

`docker stop container`

To remove a stopped container:

`docker rm [container]`

To enter the shell of the container:

`docker exec -it [container] /bin/sh`

To see the logs of a container:

`docker logs [container]`

To copy between the host and container:

`docker container cp [container:source/path] [host/destination/path]`

To export a container filesystem as a tar:

`docker container export [container]`

To rename a container:

`docker container rename [old_name] [new_name]`

To see detailed information about a container:

`docker inspect [container]`

To kill a container:

`docker kill [container]`

## Deploying an Application Using Multiple Containers

First a network will have to be created for the docker containers to see each other:

`docker network create [network_name]`

The default network created is a bridge. For other options see the [documentation](https://docs.docker.com/network/).

When initialising your containers you will need to include the network tag after the run command:

`docker run --network=[network_name] [image]`

The containers will be started connected to the internal Docker network that you have created.

## Bind Mounts

### Volumes vs Bind Mounts

## Docker Compose

Docker Compose is a tool used for running multiple container applications. Rules are declared in a YAML file which Docker Compose can then read and apply. This can streamline deployment. It is included in modern versions of Docker.

### Configuring the *docker-compose.yml* File

Example shamelessly stolen from [here](https://docs.linuxserver.io/general/docker-compose):

```
version: "2.1"
services:
  heimdall:
    image: linuxserver/heimdall
    container_name: heimdall
    volumes:
      - /home/user/appdata/heimdall:/config
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    ports:
      - 80:80
      - 443:443
    restart: unless-stopped
  nginx:
    image: linuxserver/nginx
    container_name: nginx
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/London
    volumes:
      - /home/user/appdata/nginx:/config
    ports:
      - 81:80
      - 444:443
    restart: unless-stopped
  mariadb:
    image: linuxserver/mariadb
    container_name: mariadb
    environment:
      - PUID=1000
      - PGID=1000
      - MYSQL_ROOT_PASSWORD=ROOT_ACCESS_PASSWORD
      - TZ=Europe/London
    volumes:
      - /home/user/appdata/mariadb:/config
    ports:
      - 3306:3306
    restart: unless-stopped
```

### Deployment and Management with Docker Compose

To deploy using docker compose, navigate to the directory your *docker-compose.yml* file is stored and run:

`docker-compose up -d`

*-d* is detached mode.

If the yaml file is named differently, use the *-f* file tag:

`docker-compose -f /path/to/file.yml up -d`

To stop the services and preserve networks and containers:

`docker-compose stop`

To bring down the services created and destroy the containers:

`docker-compose down`

To update images and recreate the containers as needed:

```
docker compose pull
docker compose up -d
```

## Docker Swarm

Docker Swarm is a container orchestration tool. When dealing with larger scale projects, such orchestration is an essential time saver, being able to spin up and connect multiple containers quickly and in one go.

### Docker Swarm vs Kubernetes

Another popular orchestration tool is Kubernetes, which will be covered in another document. The two have a few differences and use cases worth noting.

## Docker and Cloud Services

## Example of deploying an app with docker

## Epilogue
