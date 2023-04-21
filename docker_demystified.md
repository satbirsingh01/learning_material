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

### Linux

## Managing Containers
ps, image, prune, exec, run, rm, attach, export, kill, push/pull, rename, rmi, start, stop, 

## Creating Containers

### Dockerfile

### Building the image

### Running an Image

## Bind Mounts

## Deploying an Application Using Multiple Containers

## Docker Hub

## Docker Compose

## Docker Swarm

## Docker GUI

## Docker and Cloud Services

## Epilogue