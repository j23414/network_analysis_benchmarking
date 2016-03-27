# Create a graph

Tinkerpop3
```
gremlin> graph = TinkerGraph.open()
===>tinkergraph[vertices:0 edges:0]
```


```
gremlin> graph = TinkerFactory.createModern()
==>tinkergraph[vertices:6 edges:6]
gremlin> g = graph.traversal()
==>graphtraversalsource[tinkergraph[vertices:6 edges:6], standard]
```


```
# Print out traversal
gremlin> g.V()


# Select node with id 1
gremlin> g.V(1)

# Print out name of node 1
gremlin> g.V(1).values('name')

# These are equivalent
gremlin> g.V(1).outE('knows').inV().values('name')
gremlin> g.V(1).out('knows').values('name')

# Who is over thirty who Marko knows?
gremlin> g.V(1).out('knows').has('age', gt(30)).values('name')
```

Input from tab delimited file
```
Graph graph=TinkerGraph.open()
graph.createIndex('userId',Vertex.class)
g=graph.traversal()
getOrCreate={id->
g.V().has('userId',id).tryNext().orElseGet{g.addV('userId',id).next()}
}
new File('wiki-Vote.txt').eachLine{
if (!it.startsWith("#")){
(fromVertex,toVertex)=it.split('\t').collect(getOrCreate)
fromVertex.addEdge('votesFor',toVertex)
}
}
```


Output as a json
```
gremlin> graph.io(IoCore.graphson()).writeGraph("pagerank.json")
```

## PageRank

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