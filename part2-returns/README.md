Part 2. Excercises

Excercise 2.1: 

Running: 
docker-compose up 
(docker-compose run firstvol)

docker-compose.yml
 
version: '3.5' 

services: 

    firstvol:
      image: devopsdockeruh/first_volume_exercise 
      volumes: 
        - ./logs.txt:/usr/app/logs.txt
      container_name: write-logs

Excercise 2.2: 
