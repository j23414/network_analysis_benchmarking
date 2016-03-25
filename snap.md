# SNAP Standford Network Analysis 

They have c and python versions of SNAP. C does indeed compile but it was easier to use the python script.

```
#! /usr/bin/python

import snap

#https://snap.stanford.edu/snappy/doc/reference/GetPageRank.html

#Graph=snap.GenRndGnm(snap.PNGraph,100,1000)

Graph = snap.LoadEdgeList(snap.PNGraph,"followers.txt",0,1,' ')
#Graph.Dump()

PRankH=snap.TIntFltH()
snap.GetPageRank(Graph,PRankH)
for item in PRankH:
    print item,PRankH[item]

#UGraph = snap.GenRndGnm(snap.PUNGraph,100,1000)
#PRankH = snap.TIntFltH()
#snap.GetPageRank(UGraph, PRankH)
#for item in PRankH:
#    print item, PRankH[item]

#Network = snap.GenRndGnm(snap.PNEANet, 100, 1000)
#PRankH = snap.TIntFltH()
#snap.GetPageRank(Network, PRankH)
#for item in PRankH:
#    print item, PRankH[item]

#snap.SaveEdgeList(Graph,"PNGraph.edges")
#Graph.Dump()
```