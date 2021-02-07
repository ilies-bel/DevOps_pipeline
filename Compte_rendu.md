
# Rendu DevOps

  

## Docker

  

### Database

We used the given configuration of the *Dockerfile*.

  

**Why should we run the container with a flag -e to give the environment variables ?**

We used :

```sh

docker run -p 5432:5432 --name running-database -e POSTGRES_PASSWORD=pwd -d tp1-postgres

```

This way, we bind port, set the name of our container and set password env variable through the command line so that it isn’t stored in the *Dockerfile*.

  

#### Init data

  

The data are set with the given lines on the Dockerfile :

`COPY InsertData.sql ./docker-entrypoint-initdb.d/InsertData.sql

COPY CreateScheme.sql ./docker-entrypoint-initdb.d/CreateScheme.sql`

  
  

#### Persist data

  

To add persistence to our data we used volumes for our container with the following lines :

  

```sh

docker run --mount source=[volume_name],destination=[path_in_container] [docker_image]

```

alternatively we can just bind our data folder to a host data folder ( less flexible )

  

**Why do we need a volume to be attached to our postgres container ?**

  

This way we can have a backup of our data and don’t lose them in case the container is removed. It can also give the possibility to have access to logs.

  

### Backend API

#### Basics

  

We initialize a new java project and build it with our class Main.java

We can then execute it on the container with a jre

  

#### Multistage Build

  

We create a new maven project with the given configuration and build and run your application with Docker:

  

**Why do we need a multistage build ?**

  

We use multiple FROM statements in our Dockerfile. One stage is for Maven which will build our Java Project. But Maven depends on JDK that is why we also use a FROM statement for openjdk. To show how this works, let’s adapt the Dockerfile from the previous section to use multi-stage builds.This way we have just one container based on several images.

  

#### Backend API

  

We created a new network called “mybridge” to let our api container and our postgres database communicate. This way, we can get our students through the URL : [http://localhost:8080/departments/IRC/students](http://localhost:8080/departments/IRC/students)

  

### HTTP Server

  

#### Basics

  

We use the official Apache image of httpd from our Dockerfile. There, we just copy a basic html page for the index of our application.

  

#### Configuration

  

We copy a local configuration file to /usr/local/apache2/conf/httpd.conf through our Dockerfile.

  

#### Reverse proxy

  

**Why do we need a reverse proxy ?**

  

We need a reverse proxy to have access to our API from outside of its network.

We add the following lines to our configuration file to connect to the API.

````
ServerName localhost

<VirtualHost *:80>

ProxyPreserveHost On

ProxyPass / http://devops-tp1-api:8080/

ProxyPassReverse / http://devops-tp1-api:8080/

</VirtualHost>

````

### Versioning

  

**Why is docker-compose so important ?**

  

Docker-compose let’s us configure and manage our different containers inside a single file.

  

Here is what our docker-compose.yml looks like :

````yml

version: '3'

services:

postgres:

image: postgres:11.6-alpine

container_name: devops-tp1-postgres

networks:

- mybridge

ports:

- "5432:5432"

environment:

- POSTGRES_USER=usr

- POSTGRES_DB=db

- POSTGRES_PASSWORD=pwd

restart: always

  

api:

build: .\API\simple-api

image: openjdk:11-jre

container_name: devops-tp1-api

networks:

- mybridge

ports:

- "8080:8080"

restart: always

  

httpd:

build: .\Apache

image: httpd:2.4.46

container_name: devops-tp1-httpd

networks:

- mybridge

ports:

- "80:80"

restart: always

networks:

mybridge:

driver: bridge

````

  

### Publishing

  

**Why do we put our images into an online repository ?**

So that our application will be run as one image and make it accessible and easy to launch from other locations (users, machines).

  

#### Sample application

  

### Ansible

  

#### Setup

  

**What’s up with the /api/actuator endpoint? What could it be used for?**

  

It lets us add new features production ready. We can expose internal data through the API to monitor them.

  

#### Inventories

  

Test your inventory with the ping command:

`ansible all -i inventories/setup.yml -m ping`

  
  

#### Facts

  

### Playbooks

  

#### First playbook

  

We use playbooks to manage the deployment of our application

  

**Ask yourself: what is $basearch?**

  

Use yum repolist to find the $basearch’s variable value which is in our case : x86_64

It corresponds to the Docker’s version we are downloading.

  

#### Using rôles

  

We create roles with the following line to split each part of our application into different tasks :

```sh

ansible-galaxy init roles/docker

```

  

Each roles are called by the playbook.yml with the following lines :

rôles:

- install

- docker

- network

- database

- app

- front

  

Then we just have to launch our playbook :

```sh

ansible-playbook -i inventories/setup.yml playbook.yml

```
