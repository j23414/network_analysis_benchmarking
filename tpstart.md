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
