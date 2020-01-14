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
