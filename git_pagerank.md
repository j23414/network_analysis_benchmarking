# pagerank

Using c++ implementation of pagerank

website: https://github.com/louridas/pagerank

```
git clone https://github.com/louridas/pagerank.git
cd pagerank/cpp
make

time ./pagerank -d "," t.csv > out.txt
```

Using igraph
```
install.packages("igraph")
library(igraph)

pmt<-proc.time() # start timing
dat<-read.table("t.csv",header=FALSE,sep=",")
el=as.matrix(dat)
el[,1]=as.character(el[,1])
el[,2]=as.character(el[,2])
g=graph.edgelist(el,directed=TRUE)

# or 
g=graph.data.frame(dat,directed=FALSE)

# Run Page Rank
out<-page.rank(g,algo="prpack",vids=V(g))
temp<-out$vector
df<-data.frame(names=names(temp),prpack=temp)
#out<-page.rank(g,algo="arpack",vids=V(g))
#temp<-out$vector
#df$arpack=temp
#out<-page.rank(g,algo="power",vids=V(g))
#temp<-out$vector
#df$power=temp
write.table(df,"igraphout.tsv",quote=FALSE,sep="\t",row.names=FALSE,col.names=TRUE)
proc.time()-pmt # stop timing
```

