# Part 3. Excercises

## Exercise 3.1: 

*Optimize previous frontend & backend exercise (from part 2.)*

```
Results and Dockerfiles:
Not optimized image for Frontend:  531MB
Optimized image for Frontend: 428MB
Not optimized image for Backend: 435MB
Optimized image for Backend: 332MB 

see more info: ex3.1_results.txt

```

## Exercise 3.2: 

*Create a new Dockerfile for yle-dl and optimize it.*
 
```
# *****************************************
# * Dockerfile                            * 
# * yle-dl docker optimization exercise   *
# * ref.https://aajanki.github.io/yle-dl/ *
# *****************************************
# ajo: docker run -it -v "$(pwd)":/usr/app yle https://areena.yle.fi/xxxx (xxxx=podcast-filename)
# tested with without breaking the YLE Terms of Service wih podcast download


FROM ubuntu:16.04 

ENV LC_ALL=C.UTF-8 

WORKDIR /usr 

RUN apt-get update && apt-get install -y \
    python ca-certificates wget software-properties-common && \
    add-apt-repository ppa:jonathonf/ffmpeg-4 -y && \
    apt-get update && apt-get install -y ffmpeg && \
    apt install python3-pip -y && \
    pip3 install --upgrade pip && \
    useradd -m app && \
    add-apt-repository --remove ppa:jonathonf/ffmpeg-4 -y && \
    rm -rf /var/lib/apt/lists/*

USER app

WORKDIR /usr/app

RUN pip3 install --user --upgrade yle-dl 

# path to executable: /home/app/.local/bin
# dowloads to default directory use: "$(pwd)":/usr/app 
ENTRYPOINT ["/home/app/.local/bin/yle-dl"]

```

## Exercise 3.3:

*With previous frontend & backend exercise, make sure the containers start their processes as a non-root user.*

#### BACKEND SERVER Dockerfile

```
# BACKEND SERVER  
# This Dockerfile needs tobe located in ./bserv -directory
FROM ubuntu:16.04
WORKDIR /usr/app
RUN apt-get update && apt-get install -y \ 
    curl python git ca-certificates && \
    git clone https://github.com/docker-hy/backend-example-docker.git && \
    curl -sL https://deb.nodesource.com/setup_10.x | bash && \
    apt-get install -y nodejs && \
    apt-get purge -y --auto-remove curl git && \
    rm -rf /var/lib/apt/lists/* && \
    useradd -m app && \
    chown -R app:app /usr/app/backend-example-docker

USER app

WORKDIR /usr/app/backend-example-docker

RUN npm install -y 
ENV FRONT_URL=http://localhost:80

CMD ["npm", "start"]
EXPOSE 8000

```

#### FRONTEND SERVER Dockerfile

```
# FRONTEND SERVER
# This Dockerfile needs tobe located in ./front -directory
FROM ubuntu:16.04
WORKDIR /usr/app
RUN apt-get update && apt-get install -y \ 
    curl python git ca-certificates && \
    git clone https://github.com/docker-hy/frontend-example-docker.git && \
    curl -sL https://deb.nodesource.com/setup_10.x | bash && \
    apt-get install -y nodejs && \
    apt-get purge -y --auto-remove curl git && \
    useradd -m app && \
    chown -R app:app /usr/app/frontend-example-docker && \
    rm -rf /var/lib/apt/lists/*

USER app

WORKDIR /usr/app/frontend-example-docker

RUN npm install -y

ARG API_URL

ENV API_URL=${API_URL:-http://localhost:80}

CMD ["npm", "start"]

EXPOSE 5000

```
## Exercise 3.4 


### Image sizes in beginning 
*(without optimizations)*

```
bserv 435 MB  = Backend / ubuntu:16.04
front 531 MB  = Frontend / ubuntu:16.04 
```

### node:alpine based images
*(the smallest configuration)*

```
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
bserv               latest              01cbf2f9be40        4 minutes ago       211MB
front               latest              1c0021a79b0f        5 minutes ago       307MB
```

### ubuntu:16.04 based images
*(second smallest)*

```
REPOSITORY           TAG                IMAGE ID            CREATED             SIZE
bserv (ubuntu:16.04) latest             976bc9c08443        11 minutes ago      332MB
front (ubuntu:16.04) latest             6b8afbd40317        13 minutes ago      429MB
```

### node:10 based images
*(i.e. bigger than original i.e. even with optimizations)*

```
REPOSITORY          TAG                 IMAGE ID            CREATED              SIZE
bserv               latest              f78cfae705d9        About a minute ago   969MB
front               latest              c8df439a409d        4 minutes ago        1.07GB

```

### Optimized Dockerfile for frontend (exercise 3.4)

```
# This Dockerfile needs tobe located in ./front -directory
FROM node:alpine
WORKDIR /home/node/app

RUN apk add --no-cache curl python git ca-certificates && \
    git clone https://github.com/docker-hy/frontend-example-docker.git && \
    apk del curl git && \  
    chown -R node:node /home/node/app/frontend-example-docker

USER node

WORKDIR /home/node/app/frontend-example-docker

RUN npm install -y

ARG API_URL

ENV API_URL=${API_URL:-http://localhost:80}

CMD ["npm", "start"]

EXPOSE 5000

```

### Optimized Dockerfile for backend (exercise 3.4)

```
# This Dockerfile needs tobe located in ./bserv -directory
FROM node:alpine
WORKDIR /home/node/app

RUN apk add --no-cache curl python git ca-certificates && \
    git clone https://github.com/docker-hy/backend-example-docker.git && \
    apk del curl git && \
    chown -R node:node /home/node/app/backend-example-docker   

USER node

WORKDIR /home/node/app/backend-example-docker

RUN npm install -y 

ENV FRONT_URL=http://localhost:80

CMD ["npm", "start"]

EXPOSE 8000

```

## Exercise 3.5 

### Dockerfile for Multi-stage exercise

```
# This Dockerfile for Multi-stage builds exercise 3.5
# Building happens in two stages: "Builder"-stage and "Build"-stage
# Image based on: node:alpine
# Service build is "Serve" fileserver front
# BUILD + RUN: 
# > docker build -t mstage .
# > docker run -d -p 5000:5000 -v $(pwd)/dist:/home/node/dist mstage 
# "image for run" size: 125 MB
#

FROM node:alpine as Builder
WORKDIR /home/node/app

RUN apk add --no-cache curl python git ca-certificates && \
    git clone https://github.com/docker-hy/frontend-example-docker.git && \
    apk del curl git && \  
    chown -R node:node /home/node/app/frontend-example-docker && \
    chown -R node:node /usr/local/lib/node_modules

WORKDIR /home/node/app/frontend-example-docker

RUN npm install -g serve

FROM node:alpine as Build

COPY --from=Builder /home/node/app/frontend-example-docker  .

# start serve - service
RUN apk add --no-cache libc6-compat && \
    yarn global add serve

# let's use non root user
USER node

# create directory and content and share it with container: 
# (-v $(pwd)/dist:/home/node/dist)  
# let's make also container directory for files:
WORKDIR /home/node/dist

# listen port 5000
EXPOSE 5000

#Starting server
ENTRYPOINT ["sh", "-c", "serve", "-s", "-l", "5000", "dist"]

```

## Exercise 3.6

### Non-optimized (i.e. baseline) Dockerfile for exercise 3.6
*Image size was: 316 MB*

```
# Dockerfile for exercise 3.6 (Not optimised versio)
# (Service is same as my Excercise 1.15 return was) 
# Dockerfile for exercise 3.6 -Not Optimized
# BUILD: docker build -t nodexa .
# RUN: docker run -d -p 3000:3000 nodexa   
# "Original" size: 316 MB 
 
FROM ubuntu:18.04

WORKDIR /usr

RUN apt-get update && apt-get install -y git

RUN apt-get install -y curl  

RUN git clone https://github.com/jugkyron/nodeserver-example.git

WORKDIR /usr/nodeserver-example

RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -

RUN apt-get install -y nodejs

RUN echo "node version:" >> version-of-service

RUN nodejs -v >> version-of-service

RUN echo "npm version:" >> version-of-service

RUN npm -v >> version-of-service

RUN npm install -y

EXPOSE 3000

ENTRYPOINT ["npm", "start"]
```

### Optimized Dockerfile for exercise 3.6 (Multi-stage build image)
*Image size 113 MB*
 
```
# Dockerfile for exercise 3.6 (Multi-Stage + optimized)
# (Service is same as my Excercise 1.15 return now optimized)
# BUILD: docker build -t multis .
# RUN: docker run -d -p 3000:3000 multis   
# "Original" (old) size was: 316 MB
# New image size is: 113 MB

# Image changed from ubuntu:18.04 to node:alpine
# Stages: 1) Builder and 2) Build 

FROM node:alpine as Builder
WORKDIR /home/node/app

RUN apk add --no-cache python git ca-certificates && \
    git clone https://github.com/jugkyron/nodeserver-example.git && \
    apk del git && \  
    chown -R node:node /home/node/app/nodeserver-example

WORKDIR /home/node/app/nodeserver-example

RUN npm install -y

# Stage 2: 
FROM node:alpine as Build

WORKDIR /home/node/app
RUN chown -R node:node /home/node/app

# Copy from Builder stage:
COPY --from=Builder /home/node/app/nodeserver-example  /home/node/app/.

# let's use non-root user:
USER node

RUN echo "node version:" >> version-of-service && \
    nodejs -v >> version-of-service && \
    echo "npm version:" >> version-of-service && \
    npm -v >> version-of-service

EXPOSE 3000

ENTRYPOINT ["npm", "start"]
```

## Exercise 3.7 a)

### (200-300 words) text/article on why and when to use Kubernetes

```
 See: ex3.7_Kubernetes_Why_and_when.md

```
