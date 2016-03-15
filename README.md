# Akka data source for dstream (Spark Streaming)

A library for reading data from Akka Actors to [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html).

[![Build Status](https://travis-ci.org/spark-packages/dstream-akka.svg?branch=master)](https://travis-ci.org/spark-packages/dstream-akka)
[![codecov.io](http://codecov.io/github/spark-packages/dstream-akka/coverage.svg?branch=master)](http://codecov.io/github/spark-packages/dstream-akka?branch=master)

## Requirements

This documentation is for Spark 2.0.0+. For old Spark versions, please use the old versions published with Spark.

## Linking

You can link against this library (for Spark 2.0.0+) in your program at the following coordinates:

Using SBT:

```
libraryDependencies += "org.spark-project" %% "dstream-akka" % "0.1.0"
```

Using Maven:

```xml
<dependency>
    <groupId>org.spark-project</groupId>
    <artifactId>dstream-akka_2.10</artifactId>
    <version>0.1.0</version>
</dependency>
```

This library can also be added to Spark jobs launched through `spark-shell` or `spark-submit` by using the `--packages` command line option.
For example, to include it when starting the spark shell:

```
$ bin/spark-shell --packages org.spark-project:dstream-akka_2.10:0.1.0
```

Unlike using `--jars`, using `--packages` ensures that this library and its dependencies will be added to the classpath.
The `--packages` argument can also be used with `bin/spark-submit`.

This library is cross-published for Scala 2.11, so 2.11 users should replace 2.10 with 2.11 in the commands listed above.

## Examples

DStreams can be created with data streams received through Akka actors by using `AkkaUtils.createStream(ssc, actorProps, actor-name)`.

### Scala API

You need to extend `ActorReceiver` so as to store received data into Spark using `store(...)` methods. The supervisor strategy of
this actor can be configured to handle failures, etc.

```Scala
class CustomActor extends ActorReceiver {
  def receive = {
    case data: String => store(data)
  }
}

// A new input stream can be created with this custom actor as
val ssc: StreamingContext = ...
val lines = AkkaUtils.createStream[String](ssc, Props[CustomActor](), "CustomReceiver")
```

See [ActorWordCount.scala](examples/src/main/scala/org/apache/spark/examples/streaming/akka/ActorWordCount.scala) for an end-to-end example.

### Java API

You need to extend `JavaActorReceiver` so as to store received data into Spark using `store(...)` methods. The supervisor strategy of
this actor can be configured to handle failures, etc.

```Java
class CustomActor extends JavaActorReceiver {
  @Override
  public void onReceive(Object msg) throws Exception {
    store((String) msg);
  }
}

// A new input stream can be created with this custom actor as
JavaStreamingContext jssc = ...;
JavaDStream<String> lines = AkkaUtils.<String>createStream(jssc, Props.create(CustomActor.class), "CustomReceiver");
```

See [JavaActorWordCount.scala](https://github.com/spark-packages/dstream-akka/blob/master/examples/src/main/java/org/apache/spark/examples/streaming/akka/JavaActorWordCount.java) for an end-to-end example.

## Building From Source
This library is built with [SBT](http://www.scala-sbt.org/0.13/docs/Command-Line-Reference.html),
which is automatically downloaded by the included shell script. To build a JAR file simply run
`build/sbt package` from the project root.

## Testing

Just Run `build/sbt test examples/test`.
