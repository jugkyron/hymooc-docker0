# Part 2. Excercises

## Exercise 2.1: 

* docker-compose.yml which will create container of devopsdockeruh/first_volume_exercise and will create logs into its /usr/app/logs.txt shared with the host.*

 - Running: docker-compose up 

### docker-compose.yml
 
```version: '3.5' 

services: 

    firstvol:
      image: devopsdockeruh/first_volume_exercise 
      volumes: 
        - ./logs.txt:/usr/app/logs.txt
      container_name: write-logs
```

## Exercise 2.2: 

* docker-compose.yml for container running devopsdockeruh/ports_exercise that starts a web service that will answer in port 80 (localhost)*

 - Running: docker-compose up

## docker-compose.yml:  

```version: '3.5' 

services: 

   ports_exercise:
      image: devopsdockeruh/ports_exercise 
      ports:
        - 80:80
```
## Exercise 2.3: 

*Configured the backend and frontend from part 1 to work in docker-compose.* 

- Frontend: Dockerfile for front placed in subdirectory: front
- Server: Dockerfile for server placed in subdirectory: bserv
Running:  
> docker-compose build

> docker-compose up 

### docker-compose.yml:

```version: '3.5'

services: 

   front:
      image: front 
      build: './front'
      ports:
        - 8888:5000
      container_name: frontend

   bserv: 
      image: bserv
      build: './bserv'
      ports:
        - 8000:8000
      volumes:
        - .:/root/.npm/_logs
      container_name: bserver
```
## Exercise 2.4: 

*Solution is to use docker scale which enables scale "compute" nodes 
here 2 instances were enough i.e. by using command:* 

**docker-compose up --scale compute=2**

## Exercise 2.5: 

*Redis added to example backend i.e. hub.docker.com/_/redis/ (continuing 2.3)*

- Frontend: Dockerfile for front placed in subdirectory: front
- Server: Dockerfile for server placed in subdirectory: bserv

**Run: docker-compose up** 

### docker-compose.yml:

```version: '3.5'

services: 

   front:
      image: front 
      build: './front'
      ports:
        - 8888:5000
      container_name: frontend
   redis: 
      image: redis
      container_name: redis-cache
   bserv: 
      image: bserv
      restart: unless-stopped  
      build: './bserv'
      ports:
        - 8000:8000
      environment: 
        - REDIS=redis-cache
      volumes:
        - .:/root/.npm/_logs
      container_name: bserver
```
## Exercise 2.6:

*Continuing backend - frontend and adding postgres to it* 

- Frontend: Dockerfile for front placed in subdirectory: front
- Server: Dockerfile for server placed in subdirectory: bserv
- Cache: Redis (dockerhub)
- Database: postgres (dockerhub)

> docker-compose up

> useful: docker volume prune (clean docker volumes)

### docker-compose.yml:
```version: '3.5'

services: 

   front:
      image: front 
      build: './front'
      ports:
        - 8888:5000
      container_name: frontend
   db:
      image: postgres
      restart: unless-stopped
      environment:
        POSTGRES_DB: db-bserver
        POSTGRES_USER: db-user
        POSTGRES_PASSWORD: example
      container_name: postgres
   redis: 
      image: redis
      container_name: redis-cache
   bserv: 
      image: bserv
      restart: unless-stopped  
      build: './bserv'
      ports:
        - 8000:8000
      environment: 
        - REDIS=redis-cache
        - DB_USERNAME=db-user
        - DB_PASSWORD=example
        - DB_NAME=db-bserver
        - DB_HOST=postgres   
      container_name: bserver
```

## Exercise 2.7:

**ML Kurkkumopo project configured with docker-compose**
 
- github.com/docker-hy/ml-kurkkumopo-training
- github.com/docker-hy/ml-kurkkumopo-frontend
- github.com/docker-hy/ml-kurkkumopo-backend

**Services:**

 - subdirectory: frontend
 - subdirectory: backend
 - subdirectory: training

**Volumes: model: & imgs:**

  - volumes: > docker volume ls (to see volumes) 
  - and docker volume prune (i.e. to remove unnecessary/interfering volumes)
  - run: > docker-compose up

### docker-compose.yml: 

```version: '3.5'

services: 
   training: 
      image: training
      restart: always 
      build: './training'
      volumes: 
        - model:/src/model
        - imgs:/src/imgs
      container_name: training
   frontend:
      image: frontend 
      build: './frontend'
      ports:
        - 3000:3000
      depends_on:
        - backend
      container_name: frontend
   backend: 
      image: backend
      restart: unless-stopped  
      build: './backend'
      ports:
        - 5000:5000
      volumes: 
        - model:/src/model
      depends_on:
        - training
      container_name: backend

volumes: 
    model:
    imgs:
```

## Exercise 2.8
 
*Continuing frontend-backend example now adding nginx to it*

- Frontend: Dockerfile for front placed in subdirectory: front
- Server: Dockerfile for server placed in subdirectory: bserv

**Docker run details:**

- place the nginx.conf file to the ./lb_nginx sub-directory (with fixed urls)
 
> Run: docker-compose up 

> useful: docker volume prune, docker volume ls might needed

### docker-compose.yml:

```version: '3.5'

services: 

   front:
      image: front 
      build:
        context: './front'
      networks: 
        - nginxet
      container_name: frontend
   db:
      image: postgres
      restart: unless-stopped
      networks:
        - nginxet
      environment:
        POSTGRES_DB: db-bserver
        POSTGRES_USER: db-user
        POSTGRES_PASSWORD: example
      container_name: postgres
   redis: 
      image: redis
      networks:
        - nginxet
      container_name: redis-cache
   bserv: 
      image: bserv
      networks: 
        - nginxet
      restart: unless-stopped  
      build: './bserv'
      environment: 
        - REDIS=redis-cache
        - DB_USERNAME=db-user
        - DB_PASSWORD=example
        - DB_NAME=db-bserver
        - DB_HOST=postgres   
      volumes:
        - .:/root/.npm/_logs
      depends_on:
        - db
      container_name: bserver
   lb_nginx:
      image: nginx
      networks: 
        - nginxet
      ports: 
        - 80:80
      volumes:
        - ./lb_nginx/nginx.conf:/etc/nginx/nginx.conf 
      depends_on:
        - bserv
      container_name: lb_nginx

networks: 
   nginxet: 
```

### nginx.conf: 

```events { worker_connections 1024; }

http {
  server {
    listen 80;

    location / {
      proxy_pass http://frontend:5000;
    }

    location /api/ {
      proxy_pass http://bserver:8000/;
    }
  }
}
```

## Exercise 2.9: 

*Continuing frontend-backend example. Make database shared with host such way that it won't get deleted when container volume is removed* 

- Frontend: Dockerfile for front placed in subdirectory: front
- Server: Dockerfile for server placed in subdirectory: bserv
- Cache: Redis (dockerhub)
- Database: postgress (dockerhub)*

> docker-compose up

> docker volume prune (clean docker volumes)

### docker-compose.yml:

```version: '3.5'

services: 

   front:
      image: front 
      build: './front'
      ports:
        - 8888:5000
      container_name: frontend
   db:
      image: postgres
      restart: unless-stopped
      environment:
        POSTGRES_DB: db-bserver
        POSTGRES_USER: db-user
        POSTGRES_PASSWORD: example
        PGDATA: /var/lib/postgresql/data/pgdata
      volumes: 
        - ./pgdata:/var/lib/postgresql/data/pgdata
      container_name: postgres
   redis: 
      image: redis
      container_name: redis-cache
   bserv: 
      image: bserv
      restart: unless-stopped  
      build: './bserv'
      ports:
        - 8000:8000
      environment: 
        - REDIS=redis-cache
        - DB_USERNAME=db-user
        - DB_PASSWORD=example
        - DB_NAME=db-bserver
        - DB_HOST=postgres   
      volumes:
        - .:/root/.npm/_logs
      depends_on:
        - db
      container_name: bserver
```

## Exercise 2.10 

*Make sure that every button implemented in previous frontend-backend exercises works.* 

**Changes needed** 

```
1) docker-compose.yml: 
 - front -service build argument '/api' was added for API_URL i.e.changes:  
      ...
       build:
        context: './front'
        dockerfile: Dockerfile
        args:
          API_URL: '/api'
       ...

2) Backend Dockerfile: ENV FRONT_URL changed to be: http://localhost:80

3) Frontend Dockerfile: ENV API_URL changed to be: http://localhost:80

``` 

*Frontend Docker file:*

>  ENV ARG_URL changed to be: http://localhost:80


```

# This Dockerfile needs tobe located in ./front -directory
FROM ubuntu:16.04
WORKDIR /usr/app
RUN apt-get update && apt-get install -y curl git
RUN git clone https://github.com/docker-hy/frontend-example-docker.git
WORKDIR /usr/app/frontend-example-docker
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs
RUN npm install -y
ARG API_URL
ENV API_URL=${API_URL:-http://localhost:80}
CMD ["npm", "start"]
EXPOSE 5000 

```  

*Backend Docker file:*

>  ENV FRONT_URL changed to be: http://localhost:80

```

# This Dockerfile needs tobe located in ./bserv -directory
FROM ubuntu:16.04
WORKDIR /usr/app
RUN apt-get update && apt-get install -y curl git 
RUN git clone https://github.com/docker-hy/backend-example-docker.git 
WORKDIR /usr/app/backend-example-docker
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs
RUN npm install -y
ENV FRONT_URL=http://localhost:80
CMD ["npm", "start"]
EXPOSE 8000

```

## docker-compose.yml for exercise 2.10:

```version: '3.5' 

# Exercise 2.10 
# Frontend: Dockerfile for front placed in subdirectory: front
# Server: Dockerfile for server placed in subdirectory: bserv
# Docker run details:
# place the nginx.conf file to the ./lb_nginx sub-directory 
# > docker-compose up 
# > docker volume prune, docker volume ls might needed

services: 

   front:
      image: front 
      build:
        context: './front'
        dockerfile: Dockerfile
        args: 
          API_URL: '/api' 
      networks: 
        - nginxet
      container_name: frontend
   db:
      image: postgres
      restart: unless-stopped
      networks:
        - nginxet
      environment:
        POSTGRES_DB: db-bserver
        POSTGRES_USER: db-user
        POSTGRES_PASSWORD: example
        PGDATA : /var/lib/postgresql/data/pgdata
      volumes: 
        - ./pgdata:/var/lib/postgresql/data/pgdata
      container_name: postgres
   redis: 
      image: redis
      networks:
        - nginxet
      container_name: redis-cache
   bserv: 
      image: bserv
      networks: 
        - nginxet
      restart: unless-stopped  
      build: './bserv'
      environment: 
        - REDIS=redis-cache
        - DB_USERNAME=db-user
        - DB_PASSWORD=example
        - DB_NAME=db-bserver
        - DB_HOST=postgres   
      volumes:
        - .:/root/.npm/_logs
      depends_on:
        - db
      container_name: bserver
   lb_nginx:
      image: nginx
      networks: 
        - nginxet
      ports: 
        - 80:80
      volumes:
        - ./lb_nginx/nginx.conf:/etc/nginx/nginx.conf 
      depends_on:
        - bserv
      container_name: lb_nginx

networks: 
   nginxet: 

```

*nginx.conf not changed comparing to exercise 2.8*


```
lb_nginx/nginx.conf 

events { worker_connections 1024; }

http {
  server {
    listen 80;

    location / {
      proxy_pass http://frontend:5000;
    }
    location /api/ {
      proxy_pass http://bserver:8000/;
    }
  }
}

```
