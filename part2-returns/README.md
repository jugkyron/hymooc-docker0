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

docker-compose.yml: 
version: '3.5' 

# Frontend: Dockerfile for front placed in subdirectory: front
# Server: Dockerfile for server placed in subdirectory: bserv
# Starting: 
# > docker-compose build
# > docker-compose up 

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
 # for debugging npm errors:
      volumes:
        - .:/root/.npm/_logs
      container_name: bserver
