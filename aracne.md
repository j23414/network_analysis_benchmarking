# aracne

## Installation
```
## try http:// if https:// URLs are not supported
source("https://bioconductor.org/biocLite.R")
biocLite("minet")
library(minet)
```

## Example
```
# load data
data(syn.data)

# Run Aracne
mim <- build.mim(syn.data,estimator="spearman")
net <- aracne(mim)  #aracne( mim, eps=0 )
dim(net)

# Visualize or generate edgelist
library(igraph)
g<-graph.adjacency(net,weighted=TRUE,mode="max",diag=FALSE)
#plot(g) # visual, if not too large

# export as an edgelist
el<-get.data.frame(g)
write.table(el,file="edgelist.txt",quote=FALSE,sep="\t",row.names=FALSE,col.names=FALSE)
```