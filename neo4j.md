#Neo4j

Visit https://neo4j.com and download free community or enterprise edition.

username: neo4j
password: hmm

Data for graphs are stored as properties or name:value pairs. In Mango we call these node and link attributes. Mango maintains homogenous properties across nodes and across links. node and link properties can be different. In neo4j nodes are grouped by labels. Nodes can have zero or more labels. Properties can be string, numbers, or booleans, can store billions of nodes.

Relationships (edges, links in Mango) always have direction, have a type, and form patterns of data.

Their command line interface is called the editor. Mango calls it a console. Our editor edits Gel scripts.

Cypher
```
:help
```

Contains frames which can have panes, for tutorials, code snippets, visualization results. 

```
:clear
```

## Cypher

* uses patterns to describe graph data
* familiar SQL-like clauses
* declarative, describing what to find, not how to find it