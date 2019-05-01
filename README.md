# Aerospike Hadoop Connector

Aerospike Hadoop/Spark connector. Customized aerospike-hadoop-connector from [aerospike-hadoop][1].

## Installation
Add the following to `build.sbt`:

```scala
libraryDependencies += "" % "aerospike-mapreduce" % "1.0.0"
```

## Usage

1. Using `AerospikeConfigUtil` to config Hadoop Configuration.

```scala
val configuration = new Configuration()
AerospikeConfigUtil.setInputHost(configuration, host)
AerospikeConfigUtil.setInputPort(configuration, port)
AerospikeConfigUtil.setInputNamespace(configuration, namespace)
AerospikeConfigUtil.setInputSetName(configuration, set)
AerospikeConfigUtil.setInputBinNames(configuration, bins)
```

2. Using spark to connect Aerospike cluster.

```scala
val rdd: RDD[(AerospikeKey, AerospikeRecord)] = spark.sparkContext.newAPIHadoopRDD(
    configuration,
    classOf[AerospikeInputFormat],
    classOf[AerospikeKey],
    classOf[AerospikeRecord]
)
```

## Different From [aerospike-hadoop][1]

- When scaning data from Aerospike to Hadoop/Spark, if there was an exception happened.
    - [aerospike-hadoop][1] will just log.error and stop the scanning, so we may lose some data without noticed. 
    - In our application, we will throw up the exception. 

**Notice**: If you are using spark to connect Aerospike, spark will terminate the current sub-task and automatically launch a new task to scan the data again from the failed node. And spark will only redo the failed task, not all tasks.

## Reference
[aerospike-hadoop][1]

[1]: https://github.com/aerospike/aerospike-hadoop
