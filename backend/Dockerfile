# Parent image
FROM node:17-alpine

RUN npm install -g nodemon

WORKDIR /app 
# everithing from this point do in /app directory (copy, run, ...)

COPY package.json .

# install all dependencies specified in package.json
# RUN command is used for build time commands, like install dependencies
RUN npm install

#copy first . - relative path from folder, where Dockerfile is stored
#     second . - root directory inside docker image, 
#      If workdir is specified before, then it's relative path from this directory.
COPY . .

# usefull only for docker app, if we will use console, it shouldn't be there
EXPOSE 4000

# CMD is used for runtime instructions
CMD [ "npm", "run", "dev"]