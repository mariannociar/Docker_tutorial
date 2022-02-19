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
`docker build -t docker_test_img:tag .`
- **-t** : tag name for image (otherwise will add some random name)
- **.** : relative path to *Dockerfile*
- **:tag** : tag name for image (usually used for versioning, :v1, :alpine)

## Starting and Stopping containers
`docker images` list all built images

`docker ps` list of running containers (processes)
- -a: list all (active / inactive) containers

`docker stop container_name/ID` will stop running container

`docker run --name docker_test_c -p 4000:4000 -d image_name:tag` will create new container based on specified image
- --name
- -p : publis port local_computer_port:docker_container_port
- -d : datached mode, will not block our current terminal

`docker start container_name` will start existing container. This time we don't need to specify publish ports, because it was declared when we created new container

## Layer caching
Every time we build image, docker stores information about each layers in the cache. When we start build process again, docker look to the cache and will build only from changed layer. So this build time will be reduced. 

## Managing Images & Containers
`docker image rm image_name -f` remove image, but image can't be used in container (if no -f flag is used)
- -f : force remove if image is used by any container

`docker container rm container_name` will remove container

## Volumes
One we create image, it will be read only. When we change something inside code, we must rebuild new image based on these changes.

To link docker container with our project structure on local computer, we can use **-v** flag to specify volue inside docker run command. Format - absolute_path_to_project_folder:path_to_container_working_directory.

`docker run --name node_server_c -p 4000:4000 --rm -v /Users/mariannociar/JS/node/docker:/app node_server_img:nodemon`

There can be problem if we remove node_modules from project repository, because it will be also removed from docker container. To avoid this, we can add anonimous volume for node_modules directory.

`docker run --name node_server_c -p 4000:4000 --rm -v /Users/mariannociar/JS/node/docker:/app -v /app/node_modules node_server_img:nodemon`

## Dicker Compose
Hold all docker configuration for multiple projects. 

```
version: "3.8"
services: 
  api:
    build: ./backend
    container_name: node_c
    ports:
      - '4000:4000'
    volumes:
      - ./api:/app
      - ./app/node_modules
```

`docker-compose up` - build and run containers based on docker-compose file. Much simpler for multiple containers.

`docker-compose down --rmi all -v` stop running containers
- --rmi all : remove all images
- -v : remove all volumes