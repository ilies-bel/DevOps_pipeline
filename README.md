[![Build Status](https://travis-ci.com/ilies-bel/DevOps_pipeline.svg?branch=master)](https://travis-ci.com/ilies-bel/DevOps_pipeline)

**Iliès Beldjilali & Brice Folléas**

# sample-application-students

This is a sample application Springboot - Vue - Postgres Student application

## Backend
Simple Rest API Server on port :8080

## Frontend
Simple Vue.js Client on port :80

## Database
Simple Postgres 9 Database on port :5432

## Continuous Integration with Travis CI

Our project is linked to Travis CI the online service that allows you to
build pipelines to test your application.

To do so, our project is set with a .travis.yml file which will setup the different jobs to execute to test our application.
We have currently two stages : one to test our backend application and one for the frontend application.

## Quality Gate with Sonar Cloud

## Deploy with Ansible

Our project contains the */ansible* directory which will contain the following files and directory :
- /inventories/setup.yml
- playbook.yml
- /roles

All different roles have their own tasks to execute and are called in the playbook.yml.

They are launched by the following command :
```shell
ansible-playbook -i inventories/setup.yml playbook.yml
```

With Ansible, we can deploy our application through different docker containers.
Indeed, each part of the application correpsond to a role and the backend, frontend and database are dockerized on our server.

## Other informations

Our server has the following domain name : `brice.folleas.takima.cloud``` 