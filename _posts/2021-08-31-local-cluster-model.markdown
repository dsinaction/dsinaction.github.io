---
layout: single
title:  "Apache Spark & Docker – Local Cluster Mode"
date:   2021-10-09 14:16:43 +0200
categories: data-engineering
---

**Apache Spark** is a platform for processing large volumes of data in a distributed manner. Lack of access to a computer cluster seems for many people interested in learning Spark as an impassable obstacle. But fortunately that's not the case. We can successfully learn Apache Spark on our local machine. We can use the so called local mode. Another approach is based on using Docker and emulating a cluster with simple containers.

In the text we briefly introduce a local mode. Next we focus on a later approach, which enable to almost completely reflect the way one works with actual cluster. We assume that the reader is familiar with Docker and have already some basic understanding of Apache Spark.

## Local Mode

There are two main modes of Apache Spark: local and cluster.

Local mode runs all required processes and tasks within a single Java virtual machine. It's very suitable for prototyping and testing with small datasets. Local mode does not require any resource manager. We just need to download Spark.

Let's download Spark from the official site. The current version is 3.1.2. We use two Linux commands, wget & tar. 

```bash
wget https://dlcdn.apache.org/spark/spark-3.1.2/spark-3.1.2-bin-hadoop3.2.tgz
tar -xzvf spark-3.1.2-bin-hadoop3.2.tgz
```

In the next step we can move the installation to *~/.local/spark* and update the environmental variable *PATH* that contains an ordered list of paths that Linux will search for executables.

```bash
mv spark-3.1.2-bin-hadoop3.2 ~/local/spark
export PATH=$PATH:~/local/spark/bin:~/local/spark/sbin
```

After closing current session *PATH* will be restored to the previous state. In order to permanently modify *PATH* we need to append the last command to the script *~/.bashrc*.

There are available many Spark interpreters (e.g. pyspark for Pyhton, spark-shell for Scala). They work on the same principle as interactive environments for Python or R. By executing a command *pyspark* we launch an interpreter for Python.

```bash
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /__ / .__/\_,_/_/ /_/\_\   version 3.1.2
      /_/

Using Python version 3.6.9 (default, Jan 26 2021 15:33:00)
Spark context Web UI available at http://192.168.43.93:4040
Spark context available as 'sc' (master = local[*], app id = local-1628958581215).
SparkSession available as 'spark'.
```

What is important here is that SparkSession object has been created for us. It is the entry point to Spark SQL. We can print Spark version with a simple command.

```python
spark.version
```

Below is a simple code for counting words. We encourage the reader to run the code for some file and see whether everything works properly.

```python
from pyspark.sql.functions import split, col, explode, lower, count, length

lines = spark.read.text('/tmp/cluster-data/pg5200.txt')

lines.select(split(col('value'), ' ').alias('words')) \
    .select(explode(col('words')).alias('word')) \
    .select(lower(col('word')).alias('word')) \
    .filter(length(col('word')) >= 5) \
    .groupBy('word') \
    .agg(count('word').alias('n')) \
    .orderBy(col('n').desc()) \
    .show(10)
```

That was all for the local model. It's really simple and straightforward.

## Local Cluster Mode

A typical **distributed system** is composed of a single **master node** playing a role of a coordinator. There are also multiple **worker nodes**, which are responsible for doing actual work (incl. processing data, doing calculation). In every cluster there is also a **resource manager**. It's responsible for managing resources, like memory and CPU. It allocates resources by creating containers where processes are executed. Apache Spark closely collaborates with a resource manager. In case of **Hadoop** cluster, it can use **YARN**. Otherwise Spark has its own resource manager which is called **Standalone Cluster Manager**.

Apache Spark is based on a master-slave architecture. In every Spark application, there are two types of processes: **driver** and **executor**. The driver has a lot of important tasks including communicating with a resource manager, distributing work among executors and coordinating the whole application. There is only one driver process. However, there can be plenty of executors and they are responsible for executing tasks.

After this short introduction, we are going to focus on building our own cluster. For this purpose, we will use Docker. Our cluster will be composed of a single master node and two workers nodes. We use Spark's own cluster manager.

All scripts and codes are available in [GitHub repository](https://github.com/dsinaction/spark-local-cluster).

In the first step we will create a new image named *spark-cluster*. Dockerfile is presented below. We use python image as a base image. We need to update packages and install *curl* and *Java*. Next we download Apache Spark and set environment variables. Finally, as an entry point we use a script *start-spark.sh*. 

```bash
FROM python:3.8.10-buster

ARG spark_version=3.1.2
ARG hadoop_version=3.2

RUN apt-get update -y && \
    apt-get install -y curl openjdk-11-jre-headless && \
    apt-get clean;

RUN curl https://dlcdn.apache.org/spark/spark-${spark_version}/spark-${spark_version}-bin-hadoop${hadoop_version}.tgz -o spark.tgz && \
    tar -xxvf spark.tgz && \
    mv spark-${spark_version}-bin-hadoop${hadoop_version} /usr/local/spark && \
    mkdir /usr/local/spark/logs && \
    rm spark.tgz

ENV PYSPARK_PYTHON=python3

ENV SPARK_HOME=/usr/local/spark
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

ENV SPARK_MASTER_HOST=spark-master
ENV SPARK_MASTER_PORT=7077

ADD start-spark.sh /

ENTRYPOINT [ "/start-spark.sh" ]
```

*start-spark.sh* is a simple bash script whose the only purpose is to execute a proper process of a resource manager. There are two types of processes, master and worker. The script decides which process to execute on the base of an environmental variable *SPARK_DEPLOY_ROLE*. On a master node we want to execute a resource manager in a master mode and on workers nodes in a worker mode.

```bash
#!/bin/bash

if [ "$SPARK_DEPLOY_ROLE" == "master" ]
then
    spark-class org.apache.spark.deploy.master.Master >> $SPARK_HOME/logs/spark-master.out 2>&1
else
    spark-class org.apache.spark.deploy.worker.Worker spark://${SPARK_MASTER_HOST}:${SPARK_MASTER_PORT} >> $SPARK_HOME/logs/spark-worker.out 2>&1
fi
```

Let's build our image. Execute the following command in a directory with Dockerfile. 

```bash
docker build -t spark-cluster .
```

There are a few more things. We need to create a directory */tmp/cluster-data* that will be shared across all our containers. We explain in a moment why it's important. We will also define a network for our cluster called spark-network. It's required for processes running in the containers to communicate with each other without any problems.

```bash
mkdir /tmp/cluster-data

docker network create spark-network
```

Finally, we can create the nodes.

```bash
docker run -d --rm --network spark-network --name spark-master \
    --env SPARK_DEPLOY_ROLE=master \
    -p 8080:8080 -p 7077:7077 \
    -v /tmp/cluster-data:/tmp/cluster-data \
    spark-cluster

docker run -d --rm --network spark-network --name spark-worker-1 \
    --env SPARK_DEPLOY_ROLE=worker \
    --env SPARK_MASTER_HOST=spark-master \
    --env SPARK_MASTER_PORT=7077 \
    -v /tmp/cluster-data:/tmp/cluster-data \
    spark-cluster

docker run -d --rm --network spark-network --name spark-worker-2 \
    --env SPARK_DEPLOY_ROLE=worker \
    --env SPARK_MASTER_HOST=spark-master \
    --env SPARK_MASTER_PORT=7077 \
    -v /tmp/cluster-data:/tmp/cluster-data \
    spark-cluster
```

Verify whether the containers work properly by executing command *docker ps*.

```bash
CONTAINER ID   IMAGE           COMMAND             CREATED       STATUS       PORTS                                                                                  NAMES
b4fb87898b3a   spark-cluster   "/start-spark.sh"   4 hours ago   Up 4 hours                                                                                          spark-worker-2
018655d73cdc   spark-cluster   "/start-spark.sh"   4 hours ago   Up 4 hours                                                                                          spark-worker-1
de8eddcfaa8b   spark-cluster   "/start-spark.sh"   4 hours ago   Up 4 hours   0.0.0.0:7077->7077/tcp, :::7077->7077/tcp, 0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   spark-master
```

In case of *spark-master* two ports were exposed: 8080, 7077. The first one is used to access web interface where we can monitor our applications. You can open a webpage http://localhost:8080. Port 7077 is required for communication with a resource manager.

<img src="{{site.url}}/assets/images/posts/sparkcluster/spark-gui.png" style="display: block; margin: auto;" />

In section *Workers* we should see both our workers. It means our cluster is ready for work.

We can add more containers in the cluster and in turn increase number of workers.

## pyspark

In order to connect to our cluster from pyspark or other interpreter we need to use command line argument *master* and set it to *spark://localhost:7077*.

```bash
pyspark --master spark://localhost:7077
```

Now when we visit Spark web interface (http://localhost:8080), we will see new entry in the section *Running Applications* with id and name of our application. When we close pyspark, the entry should be moved to the section *Completed Applications*.

If we try to execute the code counting words in a file, it will probably end with failure. The reason is that our cluster is a separte entity and as a rule it doesn't have access to files on our local disk. We need to keep in mind that the code is going to be executed on worker nodes which are separate machines (or containers in our case). Therefore, when working with Spark we use as a data source other systems like distributed file systems (e.g. HDFS) or dedicates databases, to which all workers have access.

In our situation, in order to solve this problem, we can create a dedicated directory that is shared by all containers. That is exactly what we have done when we created a directory */tmp/cluster-data* and mounted it in every container (*-v /tmp/cluster-data: /tmp/cluster-data*). Now we can move all our datasets and files into that directory, and all workers will have access to them.

## Jupyter Notebooks

We can also connect to our cluster from a jupyter notebook. The only difference is that we have to manually create SparkSession object. It's quite simple as can be seen below.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .master('spark://localhost:7077') \
    .appName('spark-jupyter') \
    .getOrCreate()
```

You can find complete notebook in a GitHub repository.

## Summary

In the text we've shown how to use Apache Spark on local machine. First, we've described local model, which can be very easily set up. Next we've focused on a little bit more complicated approach, which is based on emulating local cluster with Docker containers. It reflects to a much greater extent the way one works with Spark on actual cluster. Moreover, the cluster can always be expanded with other systems like Hadoop (incl. HDFS) or some database. We think that it is a perfect way to learn Apache Spark, and other distributed systems and frameworks.