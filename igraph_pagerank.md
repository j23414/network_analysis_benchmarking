# igraph

This is an R package for network analysis

```R
# installation and loading
install.packages("igraph");
library(igraph)

# load an edgelist
dat<-read.table("edgelist.csv",header=FALSE,sep=",")
el=as.matrix(dat)
el[,1]=as.character(el[,1])
el[,2]=as.character(el[,2])
#g=graph.edgelist(el,directed=TRUE)
g=graph.data.frame(dat,directed=FALSE)

# export as an edgelist
el<-get.data.frame(g)
write.table(el,file="edgelist.txt",quote=FALSE,sep="\t",row.names=FALSE,col.names=FALSE)
```

## Creating networks in R and loading into igraph

```R
data<-read.table("gene_treatments.tab",header=TRUE,sep="\t")
# Genes names along the top, expression allong the columns
tdata<-data.frame(t(data[,2:dim(data)[2]])) 
names(tdata)<-data[,1]

# Choose a correlation coefficient
adjmatrix<-cor(tdata,method="spearman")
adjmatrix<-cor(tdata,method="pearson")
adjmatrix<-cor(tdata,method="kendall")

# Threshold
adj<-adjmatrix
adj[adjmatrix<=0.5]<-0
g<-graph.adjacency(adj,weighted=TRUE,mode="max",diag=FALSE)
```

## Creating igraph networks
Really nice series of scripts and images for each graph [Click Here for original material](http://chengjun.github.io/web_data_analysis/demo2_simulate_networks/)

```R
size<-50 # 50 nodes
g<-graph.tree(size,children=2) 
plot(g)
g<-graph.star(size)
plot(g)
g<-graph.full(size) # complete network
plot(g)
g<-graph.ring(size)
plot(g)
# connect the nodes with vertices not farther than a given limit
g<-connect.neighborhood(graph.ring(size),4)
plot(g)

# random network
g<-erdos.renyi.game(size,0.1)
plot(g)

```

##General Network Analysis Questions
```R
# Get number of nodes and edges
summary(g)

# Number of clusters
clusters(g)$no

# Largest cluster
max(clusters(g)$csize)

# Average shortest path
average.path.length(g,directed=FALSE)
```

##Plot degree distribution

This is useful to check for scale-free network. The original source had a nice series of code and images: [Click Here for original code and material](http://chengjun.github.io/web_data_analysis/demo2_simulate_networks/) The function was copied below, although I may modify it later

```R
dd = degree.distribution(g, mode = "all", cumulative = FALSE)

# Plot degree distribution

# write a function to plot the degree distribution
plot_degree_distribution = function(graph) {
    # calculate degree
    d = degree(graph, mode = "all")
    dd = degree.distribution(graph, mode = "all", cumulative = FALSE)
    degree = 1:max(d)
    probability = dd[-1]
    # delete blank values
    nonzero.position = which(probability != 0)
    probability = probability[nonzero.position]
    degree = degree[nonzero.position]
    # plot
    plot(probability ~ degree, log = "xy", xlab = "Degree (log)", ylab = "Probability (log)", 
        col = 1, main = "Degree Distribution")
}

plot_degree_distribution(g)
```

