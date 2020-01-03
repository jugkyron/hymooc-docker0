Part 2. Excercises

Exercise 2.1: 
Running: docker-compose up 

docker-compose.yml
 
version: '3.5' 

services: 

    firstvol:
      image: devopsdockeruh/first_volume_exercise 
      volumes: 
        - ./logs.txt:/usr/app/logs.txt
      container_name: write-logs


Exercise 2.2: 

docker-compose.yml:  

version: '3.5' 

services: 

   ports_exercise:
      image: devopsdockeruh/ports_exercise 
      ports:
        - 80:80

Exercise 2.3: 

(main directory) docker-compose.yml 

Frontend: Dockerfile for front placed in subdirectory: front
Server: Dockerfile for server placed in subdirectory: bserv
Starting: 
> docker-compose build
> docker-compose up 

docker-compose.yml:

version: '3.5'

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

Exercise 2.4: 

Solution is to use docker scale which enables scale "compute" nodes 
here 2 instances were enough i.e. by using command: 
docker-compose up --scale compute=2

Exercise 2.5: 

Frontend: Dockerfile for front placed in subdirectory: front
Server: Dockerfile for server placed in subdirectory: bserv
Run: docker-compose up 

docker-compose.yml:

version: '3.5'

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

Exercise 2.6:

Frontend: Dockerfile for front placed in subdirectory: front
Server: Dockerfile for server placed in subdirectory: bserv
volumes: > docker volume ls (to see volumes) and docker volume prune (i.e. to remove unnecessary/interfering volumes)
run: > docker-compose up

docker-compose.yml:
version: '3.5'

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

Exercise 2.7:

