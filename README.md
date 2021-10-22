# Docker Compose Tutorial 

<img src="https://i1.wp.com/www.docker.com/blog/wp-content/uploads/2021/06/cover-docker-compose.png?resize=1110%2C522&ssl=1">

## Introduction 

This tutorial is about docker compose as the name suggests. The scope of this tutorial is talk about the motivation behind docker compose, how it works in brief and provide a quick guide to jump start on your local machine to see how it works.

First, one needs to really look at the name "compose". In the world of container orchestration, compose, just like a composer in music is all about composing, creating and architecting your containers. With the need of multiple containers, docker compose becomes a critical tool as well as powerful one while being simple to use. 

### Notes 

{} - indicates a placeholder variable for a command

## About Container Orchestration

Managing containers can often prove to be difficult in the context of scalability, distributed environments and having a lot of infrastructure. Docker compose provides the first building block of managing multiple containers. Some of its features include:

- Orchestrate multiple containers in a single machine in isolated environments
- Preserve state with docker 
- Only recreate containers when they have changed 
- Support for environment variables 
- Ability to automatically restart containers 


## Prerequisites & Getting Started

For this tutorial to work, you will need to have docker version 19.0 or above installed through docker for linux or desktop for mac or docker desktop for windows. Downloads to these can be found [here](https://docs.docker.com/get-docker/).

In terms of knowledge, this tutorial assumes basic knowledge of docker and containers. 

## Components of a Docker Compose file 

This section introduces various parts of a docker compose file. Let us take a look a sample docker compose file:

```
version: "3.9"
   
services:
  db:
    image: postgres
    volumes:
      - ./data/db:/var/lib/postgresql/data
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    ports:
      - "8000:8000"
    depends_on:
      - db
```

### Version 

The version of the docker compose file. Like any software and software system, compose files also have versions that correspond to updates made to file design, features and the docker engine. In most cases using the latest version works best. 

### Services

Under the services section are the services i.e. containers defined throuh YAML format. In the example "db" and "web" are the services. The name of the service is upto the developer, but having consistent and concise names help in development as best practice. 

### Images/Build 

For each service, there needs to be a way to build the container through an image. Through the image argument, a predefined image can be chosen for example a database like postgres or a web server like nginx. The other way to do it is to define a dockerfile and put the path in the build argument.

### Environment Variables 

Environment variables can be defined through the environment argument and then bullet points under it to specific each variable. Docker compose also supports pointing to a file to load many environment variables. 

### Volumes

While containers are designed to be stateless, some need to store some state like datbases. With Docker compose volumes, we can store state. With the volumes aregument we can assign a directory on the host where the state management data will be stored. 

### Ports 

The ports argument is used to expose the container to the host so it can be accessed. 

### Depends on/links

Depends on as the name suggests tells docker compose that a service depends on the availability of another. In the example, the web service depends on the database. This tells docker compose to create a network bridge between the two containers to interface. In the web application, one can simply refer to "db" to refer to teh database and it will simply work due to the network bridge. Depends on can also be written as "links" which is the same thing but different docker compose version. 

## Tutorial - Running Docker Compose Locally 

### Initializing docker compose

First create a docker compose yaml file. If you are finding hard to create one, use the sample in this tutorial. Here are a few commands to get started:

```
git clone https://github.com/abhinavtripathy/docker-compose.git
cd docker-compose
```

To start the containers, run:
```
docker compose up 
```

Sample Output:
![](img/output_1.png)

As you can see both the containers have logs after we run the command. 

To specify a specific yaml file, run:
```
docker compose -f {file name with path} {Another compose file, this is optional}
```

With -f flag, you can spcificy multiple compose files and it will package it into running containers all together. 

### View running containers

```
docker ps
```

Sample Output:
![](img/output_2.png)

### Create a service 

```
docker service create --replicas {number of replicas} --name {service name} {image name}
```

Example: 

```
docker service create --replicas 1 --name helloworld nginx
```

We can also use a docker-compose file to define this: 

Sample Yaml:
```
version: "3"
services:
  dataservice:
    image: nginx:latest
    deploy:
      replicas: 3
    ports:
      - "3000:80"
```

The above yaml file defines a service: dataservice with 3 replicas and uses the latest nginx image while exposing the port 80 container to port 3000 of the host. 

To run such an example application you can clone this repo, make a swarm from the prevous commands and create a service through the following commands:

```
git clone https://github.com/abhinavtripathy/docker-swarm.git
docker stack deploy -c sample.yml app
```

Go to [localhost:3000](localhost:3000) to view the welcome page for nginx after running those commands. 

In general the format to create a service in swarm from a docker-compose yaml file is:

```
docker stack deploy -c {docker-compose file name} {docker compose app name}
```

Docker stack is a command used to deploy a docker-compose file in a swarm environment. 

### Check running services

```
docker service ls 
```

Sample Output:
![](img/output_3.png)

### Scaling service

```
docker service scale {service name}={number of replicas}
```

Sample Output:
![](img/output_4.png)

### Viewing state of worker nodes 

```
docker service ps {service name}
```

Sample Output:
![](img/output_5.png)

### Remove a service 

```
docker service rm {service name}
```

### Quit Swarm Mode 

```
docker swarm leave
```

If the above doesn't work due to the current machining being a manager node. Use the following commmand:

```
docker swarm leave --force
```

## Docker Swarm Commands Cheatsheet 

```
#Start all services
docker compose up 

#Check the current state of the swarm with container information
docker compose -f {file name with path} {Another compose file, this is optional}

#View running containers
docker ps

#Create a service with the CLI
docker service create --replicas {number of replicas} --name {service name} {image name} 

#Create a service with docker-compose file
docker stack deploy -c {docker-compose file name} {docker compose app name} 

#Scaling a service
docker service scale {service name}={number of replicas}

#Check for running services 
docker service ls

#View state of worker nodes
docker service ps {service name}

#Remove a service
docker service rm {service name}

#Leave Swarm 
docker swarm leave 

#Leave swarm forcefully 
docker swarm leave --force
```

## Acknowledgements 

- [Docker Swarm Guide](https://gabrieltanner.org/blog/docker-swarm)
- [Official Docker Swarm Tutorial](https://docs.docker.com/engine/swarm/swarm-tutorial/)

