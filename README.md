# Zeppelin

A `debian:jessie` based Spark and [Zeppelin](http://zeppelin.apache.org) Docker container.

This image is large and opinionated. It contains:

- [Spark 2.1.0](http://spark.apache.org/docs/2.1.0) and [Hadoop 2.7.3](http://hadoop.apache.org/docs/r2.7.3)
- [Spark BigQuery](https://github.com/samelamin/spark-bigquery) connector


## CREDENTIALS location
by default the credentials file can be found [here](https://github.com/samelamin/docker-zeppelin/blob/master/google-credentials/google-creds.json) , it is also mapped to the docker image under 

`/usr/google-credentials/google-creds.json`.

So please update your credentials file to interact with your BigQuery
## simple usage

To start Zeppelin pull the `latest` image and run the container:

```
docker pull samelamin/docker-zeppelin
docker run --rm -p 8080:8080 samelamin/docker-zeppelin
```

Zeppelin will be running at `http://${YOUR_DOCKER_HOST}:8080`.

## complex usage

You can use [docker-compose](http://docs.docker.com/compose) to easily run Zeppelin in more complex configurations. See this project's `./examples` directory for examples of using Zeppelin with `docker-compose` to :

- read and write from local data files
- read and write documents in ElasticSearch

## onbuild

The Docker `onbuild` container is still a part of this project, but **I have no plans to keep it updated**. See the `onbuild` directory to view its `Dockerfile`.

To use it, create a new `Dockerfile` based on `samelamin/docker-zeppelin:onbuild` and supply a new, executable `install.sh` file in the same directory. It will override the base one via Docker's [ONBUILD](https://docs.docker.com/reference/builder/#onbuild) instruction.

The steps, expressed here as a script, can be as simple as:

```
#!/bin/bash
cat > ./Dockerfile <<DOCKERFILE
FROM samelamin/docker-zeppelin:onbuild

ENV ZEPPELIN_MEM="-Xmx1024m"
DOCKERFILE

cat > ./install.sh <<INSTALL
git pull
mvn clean package -DskipTests \
  -Pspark-1.5 \
  -Dspark.version=1.5.2 \
  -Phadoop-2.2 \
  -Dhadoop.version=2.0.0-cdh4.2.0 \
  -Pyarn
INSTALL

docker build -t my_zeppelin .
```

# License

Copyright 2016 samelamin.

Licensed under the Apache License, Version 2.0: http://www.apache.org/licenses/LICENSE-2.0
