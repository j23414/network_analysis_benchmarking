# Create a graph

```
CREATE (ee:Person {name:"Emil",from:"Sweden",klout:99})
Added 1 label, created 1 node, set 3 properties, statement executed in 374 ms.
```
* CREATE clause to create data
* () parenthesis to indicate a node
* ee:Person a variable 'ee' and label "Person' for the new node
* {} brackets to add properties to the node

GEL comparison

```
node(string name, string from, int klout, string label) nt;
link[] lt;
graph(nt,lt) g={("Emil","Sweden",99,"Person")};
```
* Create a node with name,from,klout, and label attributes
* No link information yet
* {} defines a graph object
* () parenthesis to define a  node
* id and node attributes must be listed in order.

```
MATCH(ee:Person) WHERE ee.name="Emil" RETURN ee;
```

* MATCH clause to specify a pattern of nodes and relationships
* (ee:Person) a single node pattern with label 'Person' which will assign matches to the variable 'ee'
* WHERE clause to constrain the results
* ee.name="Emil" compares name property to the value "Emil"
* RETURN clause used to request particular results

```
auto ee = select node from g where name=="Emil" && label=="Person";
auto ee = g.node."Emil";  # references the particular node by id
```
* store node in subgraph ee. if not found ee will be empty.
* select clause means you are fetching a subgraph that matches a condition
* name=="Emil"

```
MATCH (ee:Person) WHERE ee.name = "Emil" CREATE (js:Person {name:"Johan",from:"Sweden",learn:"surfing"}),(ir:Person {name:"Ian",from:"England",title:"author"}),(rvb:Person{name:"Rik",from:"Belgium",pet:"Orval"}),(ally:Person{name:"Allison",from:"California",hobby:"surfing"}),(ee)-[:KNOWS {since:2001}]->(js),(ee)-[:KNOWS{rating:5}]->(ir),(js)-[:KNOWS]->(ir),(js)-[:KNOWS]->(rvb),(ir-[:KNOWS]->(js),(ir)-[:KNOWS]->(ally),(rvb)-[:KNOWS]->(ally)
```

```
node(string id, string type, string name, string from, int klout, string learn, string title, string pet, string hobby) nt;
link<string type="KNOWS", string since, int rating> lt;
graph(nt,lt) g = {("ee","Person","Emil","Sweden",99)["KNOWS","2001">("js","Person","Johan","Sweden",0,"surfing")["KNOWS">("ir","Person","Ian","England",0,"","author")[>js,ir[>("ally","Person","Allison","California",0,"","","","surfing")};

graph(nt,lt) g = {("ee","Person","Emil","Sweden",99),
                  ("js","Person","Johan","Sweden",0,"surfing"),
                  ("ir","Person","Ian","England",0,"","author"),
                  ("rvb","Person","Rik","Belgium",0,"","","Orval"),
                  ("ally","Person","Allison","California",0,"","","","surfing"),
		  ee["KNOWS","2001">js,ee["KNOWS","",5>ir,
		  js["KNOWS">ir,js[>rvb,
		  ir[>js,ir[>ally,
		  rvb[>ally
                 };
```

There are other ways to code the graph information

Pattern matching

```
MATCH (ee:Person)-[:KNOWS]-(friends) where ee.name="Emil" RETURN ee,friends
```
* MATCH clause to describe the pattern from known Nodes to found Nodes
* (ee) starts the patern with a Person (qualified by WHERE)
* -[:KNOWS]- matches "KNOWS" relationship (in either direction)
* (friends) will be bound to Emil's friends

```
auto friends=select link from g where (in.name=="Emil" ||out.name=="Emil")&& type="KNOWS"; # only links to emil
friends+=g; # fetch the links among friends
```

Recommend using patterns

```
MATCH (js:Person)-[:KNOWS]-()-[:KNOWS]-(surfer) WHERE js.name="Johan" AND surfer.hobby="surfing" RETURN DISTINCT surfer
```

```
foreach node in g where hobby=="surfer" set _r=1;
node(g:node,int step) new_n;
link<g:link, int step> new_l;

graph(new_n, new_l) js = g;
foreach node in js set step=0;
foreach link in js set step=0;
js.node."js".step=1;

# run this twice
foreach link in js where in.step>0 && out.step==0 set out.step=in.step+1,in._text=in.step,out._text=out.step;
foreach link in js where in.step==0 && out.step>0 set in.step=out.step+1,in._text=in.step,out._text=out.step;

auto query=select node from js where step==3 &&	  hobby=="surfing";
foreach node in query set _text=name;
```

* maybe we need a feature for connected components and certain steps away from a node...

Analyze

```
PROFILE MATCH (js:Person)-[:KNOWS]-()-[:KNOWS]-(surfer) WHERE js.name="Johan" AND surfer.hobby="surfing" RETURN DISTINCT surfer
Cypher version: CYPHER 2.3, planner: COST. 29 total db hits in 32 ms.
```

```
There is no profile function in gel. need this!!!
```

Load from a CSV!!!

```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/products.csv"
AS row
CREATE (n:Product)
SET n = row,
  n.unitPrice=toFloat(row.unitPrice),
  n.unitsInStock=toInt(row.unitsInStock),n.unitsOnOrder=toInt(row.unitsOnOrder),
  n.reorderLevel=toInt(row.reorderLevel),n.discontinued=row.discontinued<>"0")
```

```
LOAD CSV WITH HEADERS from "http://data.neo4j.com/northwind/categories.csv" AS row
CREATE(n:Category)
SET n=row
```

```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/suppliers.csv"
  AS row
CREATE (n:Supplier)
SET n=row
```

Create indexes(??)
```
CREATE INDEX ON :Product(productID)
CREATE INDEX ON :Category(categoryID)
CREATE INDEX ON :Supplier(supplierID)
```

Create edges
```
MATCH (p:Product),(c:Category)
WHERE p.categoryID = c.categoryID
CREATE (p)-[:PART_OF]->(c)
```

GEL
```
node(p:node,c:node) nt;
link<string type>lt;

graph(nt,lt) combined = p*c;
combined=select link from combined where in.supplierID==out.supplierID;
foreach link in combined set type="PART_OF";  # actually match function might be better, variable length?
```

```
MATCH (p:Product),(s:Supplier)
WHERE p.supplierID=s.supplierID
CREATE (s)-[:SUPPLIES]->(p)
```

http://neo4j.com/developer/guide-importing-data-and-etl/

List the product categories provided by each supplier
```
MATCH (s:Supplier)-->(:Product)-->(c:Category)
RETURN s.companyName as Company, collect(distinct c.cateogryName) as Categories
```

Find the produce suppliers
```
MATCH(c:Category {CategoryName:"Produce"})<--(:Product)<--(s:Supplier)
RETURN DISTINCT s.companyName as ProduceSuppliers
```

Northwind customers place orders which may detail multiple products
```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/customer.csv"
  AS row
CREATE (n:Customer)
SET n=row

LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/orders.csv"
  AS row
CREATE (n:Order)
SET n=row

CREATE INDEX ON :Customer(customerID)
CREATE INDEX ON :Order(orderID)
```

Create data relationships
```
MATCH (c:Customer),(o:Order)
WHERE c.customerID=o.customerID
CREATE (c)-[:PURCHASED]->(o)
```

```
LOAD CSV WITH HEADERS FROM "http://data.neo4j.com/northwind/order-details.csv"
  AS row
MATCH (p:Product), (o:Order)
WHERE p.ProductID = row.productID AND o.orderID = row.orderID
CREATE (o)-[details:ORDERS]->(p)
SET details=row
  details.quantity=toInt(row.quantity)
```

Query using patterns
```
MATCH (cust:Customer)-[:PURCHASED]->(:Order)-[o:ORDERS]->(p:Product),(p)-[:PART_OF]->(c:Category {categoryName:"Produce"})
RETURN DISTINCT cust.contactName as CustomerName, SUM(o.quantity) AS Total ProductsPurchased
```

