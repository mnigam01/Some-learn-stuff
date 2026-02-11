1. check version of docker and docker compose
docker --version
docker compose version

2. pull an image from repo
docker pull image_name:tag

3. run pulled image in a container
docker run image_name:tag

4. see all images
docker images

5. see all containers
docker ps / docker ps -a

6. delete a container
docker rm container_id

7. stop a container
docker stop container_id

8. start a container
docker start container_id

9. restart a container
docker restart container_id

10. delete an image
docker rmi image_id

11. in a project or application you write a Dockerfile (name is case sensitive)
```
FROM python:3.10-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["gunicorn", "-b", "0.0.0.0:5000", "app:app"]

```

12. Build a image from Dockerfile
docker build -t flask-app:1.0 .

13. Run docker image in detached mode and bind port
docker run -d -p 8000:5000 --name flask-container flask-app:1.0 

14. see docker container logs
docker logs container_id

15. access to docker shell
docker exec -it container_id sh

16. write a docker compose file docker-compose.yml
```
version:"3.8"

services:
    backend:
        build: . 
        ports:
            - "8000:5000"
        depends_on:
            my_db

    my_db:
        image: postgres:15-alpine
        environment:
            POSTGRES_DB: appdb
            POSTGRES_USER: user
            POSTGRES_PASSWORD: pass

```

17. start a docker application using docker compose
docker compose up -d

18. stop and tear down everything
docker compose down

19. login to docker repo
docker login

20. tag a image with different name
docker tag flask-app:1.0 myusernameonwebsite/someappname:1.0

21. push the image on repo
docker push myusernameonwebsite/someappname:1.0

22. pull your image
docker pull myusernameonwebsite/someappname:1.0

23. maintain a .dockerignore file

24. some example of full docker-compose.yml
```yaml
version: '3.8' # Specify Compose file format version

services:
  # Define your application services (containers)
  web:
    build: . # Build from Dockerfile in current directory
    # image: 'myusername/web-app:latest' # Alternatively, use a pre-built image
    ports:
      - "80:80" # Host_port:Container_port
    environment:
      MYSQL_HOST: mysql-db # Use the container_name of the MySQL service
      MYSQL_USER: root
      MYSQL_PASSWORD: root
      MYSQL_DATABASE: devops_db
    env_file:
      - .env
    volumes:
      - 'app-data:/app/data' # Mount a named volume
      # - './src:/app/src' # Mount a bind mount
    networks:
      - app-network # Attach to a custom network
    depends_on:
    #   - db
      mysql-db:
        condition: service_healthy 
    restart: 'always' # Restart policy (e.g., 'always', 'on-failure')
    healthcheck: # Check if service is truly ready
      test: ["CMD-SHELL", "curl -f http://localhost/health || exit 1"]
      interval: 10s
      timeout: 5s
      retries: 5
    #   start_period: 30s # Give service time to initialize before starting health checks

  db:
    image: 'mysql:8.0'
    container_name: 'mysql-db' # Specific container name
    environment:
      MYSQL_ROOT_PASSWORD: 'root'
      MYSQL_DATABASE: 'myapp_db'
    volumes:
      - 'db-data:/var/lib/mysql' # Persist DB data
    networks:
      - app-network
    restart: 'always'
    healthcheck:
      test: ["CMD-SHELL", "mysqladmin ping -h localhost -u$$MYSQL_USER -p$$MYSQL_PASSWORD --silent || exit 1"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s # Give MySQL time to initialize


volumes:
  # Define named volumes used by services
  app-data:
  db-data:

networks:
  # Define custom networks
  app-network:
    driver: bridge # Explicitly use bridge driver
```

25. view live logs
docker logs -f <container_id_or_name>

26. view specific last n logs
docker logs --tail 10 <container_id_or_name>

27. view timestamps with logs
docker logs -t <container_id_or_name>

28.