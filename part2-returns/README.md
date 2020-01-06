# Part 2. Excercises

## Exercise 2.1: 

*Running: docker-compose up* 

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

## docker-compose.yml:  

```version: '3.5' 

services: 

   ports_exercise:
      image: devopsdockeruh/ports_exercise 
      ports:
        - 80:80
```
## Exercise 2.3: 

*Frontend: Dockerfile for front placed in subdirectory: front
Server: Dockerfile for server placed in subdirectory: bserv
Starting:*
 
**> docker-compose build
> docker-compose up ** 

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

*Frontend: Dockerfile for front placed in subdirectory: front
Server: Dockerfile for server placed in subdirectory: bserv*

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

*Frontend: Dockerfile for front placed in subdirectory: front
Server: Dockerfile for server placed in subdirectory: bserv.*
> to see volumes: 'docker volume ls' 
> to remove volumes: 'docker-compose up'

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

*Services:
 - subdirectory: frontend
 - subdirectory: backend
 - subdirectory: training
 Volumes: model: & imgs:
  - volumes: > docker volume ls (to see volumes) 
  - and docker volume prune (i.e. to remove unnecessary/interfering volumes)
  - run: > docker-compose up*

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
 
  * Frontend: Dockerfile for front placed in subdirectory: front
  Server: Dockerfile for server placed in subdirectory: bserv
  Docker run details:
  place the nginx.conf file to the ./lb_nginx sub-directory*
 
> docker-compose up 
> docker volume prune, docker volume ls might needed

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

