+++
date = '2025-02-24T21:36:55+05:45'
draft = false
title = 'Docker'
+++

#### Docker

> A container platform that allows you to build, test & deploy applications quickly

#### Dockerfile

> A set of instructions for creating an image

#### Image

> A packaged application and its dependencies

#### Container

> A running instance of that image executing the application in an isolated environment

#### Docker Volume

> A persistent storage mechanism that allows containers to store and share data beyond their lifecycle.

#### Docker Compose

> A tool for defining and managing multi-container applications using a YAML configuration file.

#### Layer Caching

> A mechanism that speeds up Docker builds by reusing unchanged layers from previous builds, reducing build time and improving efficiency.

```bash
# Pull docker image from DockerHub
docker pull IMAGE_NAME:TAG

```

#### Steps

- Create dockerfile

#### This is a simple example of Dockerfile for an express API

> Here, RUN is used to specify commands which runs while the docker image is being built
>
> While, CMD is used to specify commands which runs while the docker container is run

```dockerfile
FROM node:17-alpine

WORKDIR /app

# Copy package files first for caching
COPY package*.json ./

RUN npm install

# Copy app source
COPY . .

EXPOSE 3000

# Expose port and start
CMD ["node","app.js"]
```

#### Build the image

```bash
# Build the docker image from docker file
# . specifies the relative path to the docker file form the terminal
# The tag is optional
docker build -t IMAGE_NAME:TAG_NAME .

docker build -t myapp:v1 .
```

#### Run the image

```bash
# Here,
# -d mean detached mode : run in the background
# -p 4000:3000 means map the port 4000 of your host machine
# to the port 3000 inside the container
docker run -d -p 4000:3000 --name acme-api acme-api:latest

sudo docker run -d -p 27017:27017 7ee26c8012da

# All the associated commands to images
# Run docker image
docker run IMAGE_NAME:TAG
docker run ubuntu:latest

# Using this does not expose the port to the host machine
docker run --name myapp_c1 myapp

# Runs container in the background : -d
docker run -d node:latest

# Run and interact with the container : -it
docker run -it node

sudo docker run -d -p 27017:27017 7ee26c8012da
```

#### To stop a container

```bash
# Stop docker container
sudo docker stop CONTAINER_NAME_or_ID

# Stops all the containers
sudo docker stop $(sudo docker ps -q)
```

#### To start or stop container

> (No need to map the ports again or run on detached mode while starting the container)

```bash
# List all the images
docker images

# List all the running containers
docker ps

# List containers including the stopped ones
docker ps -a

docker start myapp

docker stop myapp


```

#### To remove images and container

```bash
# Lists the built images
docker images

docker image rm myapp4

docker rmi myapp4

# -f option forces the deletion of image even if a container uses the image
docker image rm myapp4 -f


# Specify the name of the container
sudo docker container rm app_c2

# Remove all the docker images
sudo docker rmi $(sudo docker images -q)
```

#### Main command to remove all images and containers (WARNING)

```bash
docker system prune -a
```

#### Docker Volumes

```dockerfile
FROM node:17-alpine

RUN npm install -g nodemon

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm","run","dev"]
```

> Here,
> --rm Remove or Delete Container once its stopped
> -v to map the api folder in the host machine to the /app folder in the container
> This maps the files of host machine to the directy in the container

```bash
docker run --name myapp -p 3000:4000 -d --rm -v /home/pratik/work/api:/app -v /app/node_modules myimage:nodemon
```

#### Docker Compose

```bash
docker-compose up
# Deletes the images and volume as well
docker-compose down --rmi all -v
```

#### Docker Compose File

```yaml
version: "1.0"
services:
  api:
    build: ./api
    container_name: test_api
    ports:
      - "4000:4000"
    volumes:
      - ./api:/app
      - ./app/node_modules

  web:
    build: ./web
    container_name: web
    ports:
      - "4000:4000"
    volumes:
      - ./web:/app
      - ./web/node_modules
    stdin_open: true
    tty: true
```

#### GitHub Action YAML

```yaml
name: CD for Production

on:
  push:
    branches: ["main"]

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Deploy to Production
        run: |
          docker-compose -f docker-compose.prod.yml build
          docker-compose -f docker-compose.prod.yml up -d
```
