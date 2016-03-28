# Pagerank on TinkerPop3

** References**

* [Original Tutorial](https://tinkerpop.apache.org/docs/3.1.0-incubating/tutorials-getting-started.html)

## Installation

```
$ wget http://www-us.apache.org/dist/incubator/tinkerpop/3.1.1-incubating/apache-gremlin-console-3.1.1-incubating-bin.zip
$ unzip apache-gremlin-console-3.1.1-incubating-bin.zip
$ cd apache-gremlin-console-3.1.1-incubating
$ # On Mac, switch to bash and set the JAVA_HOME variable or the following exception occurs:
$ # Exception in thread "main" java.lang.NoClassDefFoundError: java/util/concurrent/CompletableFuture
$ # bash
$ # JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_77.jdk/Contents/Home
$ ./bin/gremlin.sh   

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
plugin activated: tickerpop.server
plugin activated: tickerpop.utilities
plugin activated: tickerpop.tickergraph
gremlin> 
```

To exit the gremlin shell type the following.

```
gremlin>:q
```

##Load data into Tinkerpop from a file and export to file

First we need a file to load into Tinkerpop

```
$ curl -L -O http://snap.stanford.edu/data/wiki-Vote.txt.gz
$ gunzip wiki-Vote.txt.gz
```

The top of the network file looks like this

```
# Directed graph (each unordered pair of nodes is saved once): Wiki-Vote.txt 
# Wikipedia voting on promotion to administratorship (till January 2008). Directed edge A->B means user A voted on B becoming Wikipedia administrator.
# Nodes: 7115 Edges: 103689
# FromNodeId    ToNodeId
30      1412
30      3352
30      5254
30      5543
30      7478
3       28
3       30
```

Then we load the network into TinkerPop using the following gremlin commands.

```
gremlin> graph = TinkerGraph.open()
===>tinkergraph[vertices:0 edges:0]
gremlin> graph.createIndex('userId',Vertex.class)
==>null
gremlin> g=graph.traversal()
==>graphtraversalsource[tinkergraph[vertices:0 edges:0], standard]
gremlin> getOrCreate={id->
gremlin> g.V().has('userId',id).tryNext().orElseGet{g.addV('userId',id).next()}
gremlin> }
==>groovysh_evaluate$_run_closure1@3a4b0e5d
gremlin> new File('wiki-Vote.txt').eachLine{
gremlin> if(!it.startsWith("#")){
gremlin> (fromVertex,toVertex)=it.split('\t').collect(getOrCreate)
gremlin> fromVertex.addEdge('votesFor',toVertex)
gremlin> }
gremlin> }
==>e[117918][117916-votesFor->44907]
```

output the graph with...
```
gremlin> graph.io(IoCore.graphson()).writeGraph("pagerank.json")
```

## Run PageRank on a graph in TinkerPop3

For the moment, we will use a smaller graph, but the same principle applies to the loaded graph

```
gremlin> graph=TinkerFactory.createModern()
==>tinkergraph[vertices:6 edges:6]
gremlin> result=graph.compute().program(PageRankVertexProgram.build().create()).submit().get()
==>result[tinkergraph[vertices:6 edges:0],memory[size:0]]
gremlin> result.memory().runtime
==>149
gremlin> g=result.graph().traversal(standard())
==>graphtraversalsource[tinkergraph[vertices:6 edges:0], standard]
gremlin> g.V().valueMap('name',PageRankVertexProgram.PAGE_RANK)
==>[gremlin.pageRankVertexProgram.pageRank:[0.15000000000000002], name:[marko]]
==>[gremlin.pageRankVertexProgram.pageRank:[0.19250000000000003], name:[vadas]]
==>[gremlin.pageRankVertexProgram.pageRank:[0.4018125], name:[lop]]
==>[gremlin.pageRankVertexProgram.pageRank:[0.19250000000000003], name:[josh]]
==>[gremlin.pageRankVertexProgram.pageRank:[0.23181250000000003], name:[ripple]]
==>[gremlin.pageRankVertexProgram.pageRank:[0.15000000000000002], name:[peter]]
gremlin> result.graph.io(IoCore.graphson()).writeGraph("pagerank.json")
```

## Process the output file
It outputs as a json, can't find way to get regular text. An example entry is listed below:

```
{"id":1,"label":"person","properties":{"gremlin.pageRankVertexProgram.pageRank":[{"id":192,"value":0.15000000000000002}],"name":[{"id":0,"value":"marko"}],"gremlin.pageRankVertexProgram.edgeCount":[{"id":21,"value":3.0}],"age":[{"id":1,"value":29}]}}
```

I'm going to use a perl script to fetch the pagerank values.

```
#! /usr/bin/env perl                                                                                                       

use strict;
use warnings;

while(<>){
    chomp;
#    if(/"gremlin.pageRankVertexProgram.pageRank":\[{"id":\d+,"value":(\d+)}\],"name":\[{"id":\d+,"value":"(.+)"}/){       
    if(/gremlin.pageRankVertexProgram.pageRank(.+)value":(\d+).(\d+)}(.+)name(.+)value":"(\S+)"}(.+)gremlin/){
        print "$6\t$2.$3\n";
    }
}
```