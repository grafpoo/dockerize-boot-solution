# dockerize-boot-solution

Step 1: Run application as-is, locally

First, clone the starter project. then build and run it
```sh
git clone https://github.com/grafpoo/dockerize-boot-starter
cd dockerize-boot-starter
mvn spring-boot:run
```

then test with _curl_:

```sh
curl localhost:12080/season-report/2019-2020
```

then shut it down (with Ctrl-C), as we'll be re-using that port in step 4

Step 2: Build image with maven and spring-boot plugin

```sh
mvn clean spring-boot:build-image
```

if you now look at your docker images, you should see one named _restapi_, tagged with version **0.0.1-SNAPSHOT**

Step 3: (Tag and) push image to docker registry. Solution here uses hub.docker.io, so there's no need to provide the hostname, just a docker username (you may need to _docker login_ first):

```sh
docker tag restapi:0.0.1-SNAPSHOT <username>/restapi:latest
docker push <username>/restapi:latest
```

Step 4: Create a _docker-compose.yaml_ that spins up both images, and run it (and _test_ it, of course - the same curl command should work)

docker-compose.yaml:

```yml
version: '3.1'
services :
  db:
    image: <username>/pgtest
    ports:
      - "5432:5432"
  app:
    image: restapi:0.0.1-SNAPSHOT
    restart: always
    depends_on:
      - db
    environment:
      FOOTIE_DB_HOST: db
    ports:
      - 12080:12080
    deploy:
      resources:
        limits:
          memory: 1000M
        reservations:
          memory: 200M
```

and, to run (from this directory):

```sh
docker-compose up -d
```

the _-d_ parameter disconnects the running docker-compose from your terminal, so you will see a prompt back. You can monitor how the images are doing with _docker logs_. For instance, when I run it, i can look at the application container's logs with:

```sh
docker logs dockerize-boot-starter_app_1
```
