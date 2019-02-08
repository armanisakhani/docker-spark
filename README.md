[![Gitter chat](https://badges.gitter.im/gitterHQ/gitter.png)](https://gitter.im/big-data-europe/Lobby)
# Spark docker

Docker images to:
* Setup a standalone [Apache Spark](http://spark.apache.org/) cluster running one Spark Master and multiple Spark workers
* Build Spark applications in Java, Scala or Python to run on a Spark cluster

Currently supported versions:
* Spark 2.3.1 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.3.0 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.2.2 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.2.1 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.2.0 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.1.3 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.1.2 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.1.1 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.1.0 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.0.2 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.0.1 for Hadoop 2.7+ with OpenJDK 8
* Spark 2.0.0 for Hadoop 2.7+ with Hive support and OpenJDK 8
* Spark 2.0.0 for Hadoop 2.7+ with Hive support and OpenJDK 7
* Spark 1.6.2 for Hadoop 2.6 and later
* Spark 1.5.1 for Hadoop 2.6 and later

## Using Docker Compose

Add the following services to your `docker-compose.yml` to integrate a Spark master and Spark worker in [your BDE pipeline](https://github.com/big-data-europe/app-bde-pipeline):
(you should replace your host in docker-compose.yml file)
```yml
version: '2'
services:
  spark-master:
    build: ./master
    container_name: spark-master
    ports:
      - 127.0.0.1:8080:8080
      - 127.0.0.1:7077:7077
      - 127.0.0.1:6066:6066
    environment:
      - INIT_DAEMON_STEP=setup_spark
    network_mode: "host"
  spark-worker-1:
    build: ./worker
    container_name: spark-worker-1
    depends_on:
      - spark-master
    ports:
      - 127.0.0.1:8081:8081
    environment:
      - "SPARK_WORKER_WEBUI_PORT=8081"
      - "SPARK_MASTER=spark://your-hostname:7077"
    network_mode: "host"
  spark-worker-2:
    build: ./worker
    container_name: spark-worker-2
    depends_on:
      - spark-master
    ports:
      - 127.0.0.1:8082:8082
    environment:
      - "SPARK_WORKER_WEBUI_PORT=8082"
      - "SPARK_MASTER=spark://your-hostname:7077"
    network_mode: "host"
  zeppelin:
    build: ./zeppelin
    ports:
      - 6060:6060
    volumes:
      - ./zeppelin/notebook:/opt/zeppelin/notebook
      - ./zeppelin/conf/:/opt/zeppelin/conf/
      - ./zeppelin/jarFiles/:/opt/zeppelin/localJarFiles/
    environment:
      CORE_CONF_fs_defaultFS: "hdfs://localhost:54310"
      SPARK_MASTER: "spark://your-hostname:7077"
      MASTER: "spark://your-hostname:7077"
    depends_on:
      - spark-master
    network_mode: "host"
```
Make sure to fill in the `INIT_DAEMON_STEP` as configured in your pipeline.
Make sure to replace the `your-hostname` with your hostname.
Make sure to add your zeppelin config files to `zeppelin/conf` directory. (for example it is necessary to add zeppelin-site.xml and change the zeppelin port to 6060 by default it is set on 8080 and conflict by spark ports)
You could find some important config files in .gitignore.

## Running Docker containers without the init daemon
### Spark Master
To start a Spark master:

    docker run --name spark-master -h spark-master -e ENABLE_INIT_DAEMON=false -d bde2020/spark-master:2.3.1-hadoop2.7

### Spark Worker
To start a Spark worker:

    docker run --name spark-worker-1 --link spark-master:spark-master -e ENABLE_INIT_DAEMON=false -d bde2020/spark-worker:2.3.1-hadoop2.7

## Launch a Spark application
Building and running your Spark application on top of the Spark cluster is as simple as extending a template Docker image. Check the template's README for further documentation.
* [Java template](https://github.com/big-data-europe/docker-spark/tree/master/template/java)
* [Python template](https://github.com/big-data-europe/docker-spark/tree/master/template/python)
* Scala template (will be added soon)
