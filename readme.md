# Docker
## Images
- Like blueprints for container
    - runtime environment
    - application code
    - Any dependencies
    - Extra configuration (e.g. env variables)
    - Commands
- Are ReadOnly

## Container
- Runnable instance of an image

## Parent image & Docker Hub
Images are made up from several layers (parent image, source code, dependencies, run commands)
**Parent image**: Includes the OS & sometimes the runtime environments
All parent images can be found at [dockerhub](https://hub.docker.com/search?type=image)

## Dockerfile
```
# Parent image
FROM node:17-alpine

WORKDIR /app 
# everithing from this point do in /app directory (copy, run, ...)

#copy first . - relative path from folder, where Dockerfile is stored
#     second . - root directory inside docker image, 
#      If workdir is specified before, then it's relative path from this directory.
COPY . .

# install all dependencies specified in package.json
# RUN command is used for build time commands, like install dependencies
RUN npm install

# usefull only for docker app, if we will use console, it shouldn't be there
EXPOSE 5000

# CMD is used for runtime instructions
CMD [ "node", "server.js"]
```

### .dockerignore
files/folders we don't want to copy to docker image. For example node_modules folder for node/express, react projects. Or documentation. We dont realy need it inside docker image.

### Building image
We should be in directory, which contains *Dockerfile*, or we must specify path to *Dockerfile*
`docker build -t docker_test_img .`
- **-t** : tag name for image (otherwise will add some random name)
- **.** : relative path to *Dockerfile*

## Starting and Stopping containers
`docker images` list all built images

`docker ps` list of running containers (processes)
- -a: list all (active / inactive) containers

`docker stop container_name/ID` will stop running container

`docker run --name docker_test_c -p 4000:4000 -d image_name` will create new container based on specified image
- --name
- -p : publis port local_computer_port:docker_container_port
- -d : datached mode, will not block our current terminal

`docker start container_name` will start existing container. This time we don't need to specify publish ports, because it was declared when we created new container

## Layer caching