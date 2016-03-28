# Pagerank on GraphX Apache Spark

This uses apache spark. Installation is similar to gremlin.

```
$ wget http://www-us.apache.org/dist/spark/spark-1.6.1/spark-1.6.1-bin-hadoop2.6.tgz
$ tar zxvf spark-1.6.1-bin-without-hadoop.tgz
$./bin/spark-shell

scala> import org.apache.spark._
import org.apache.spark._

scala> import org.apache.spark.graphx._
import org.apache.spark.graphx._

scala> import org.apache.spark.rdd.RDD
import org.apache.spark.rdd.RDD
```

Exit the shell with the following command:

```
scala> exit
```

## Load in a network file

The file looks like this:
```
2 1
4 1
1 2
6 3
7 3
7 6
6 7
3 7
```

```
scala> var graph=GraphLoader.edgeListFile(sc,"followers.txt")
graph: org.apache.spark.graphx.Graph[Int,Int] = org.apache.spark.graphx.impl.GraphImpl@181f2388

scala> val ranks=graph.pageRank(0.0001).vertices
ranks: org.apache.spark.graphx.VertexRDD[Double] = VertexRDDImpl[897] at RDD at VertexRDD.scala:57

scala> val users = sc.textFile("users.txt").map { line =>
     |   val fields = line.split(",")
     |   (fields(0).toLong, fields(1))
     | }
users: org.apache.spark.rdd.RDD[(Long, String)] = MapPartitionsRDD[908] at map at <console>:34

scala> val ranksByUsername = users.join(ranks).map {
     |   case (id, (username, rank)) => (username, rank)
     | }
ranksByUsername: org.apache.spark.rdd.RDD[(String, Double)] = MapPartitionsRDD[912] at map at <console>:40

scala> println(ranksByUsername.collect().mkString("\n"))
(justinbieber,0.15)
(matei_zaharia,0.7013599933629602)
(ladygaga,1.390049198216498)
(BarackObama,1.4588814096664682)
(jeresig,0.9993442038507723)
(odersky,1.2973176314422592)
```

Write output to a file

```
scala> import java.io.PrintWriter;
import java.io.PrintWriter

scala> import java.io.File
import java.io.File

scala> var file=new File("file.txt")
file: java.io.File = file.txt

scala> var printWriter = new PrintWriter("file.txt")
printWriter: java.io.PrintWriter = java.io.PrintWriter@1f2fc50

scala> printWriter.println(ranksByUsername.collect().mkString("\n"))

scala> printWriter.close()
```

Process the output file
```
$ cat file.txt | sed 's/(//g' | sed 's/)//g' | tr ',' '\t' > out.txt
```