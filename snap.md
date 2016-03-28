# SNAP Standford Network Analysis 

##Installation

```
$ git clone https://github.com/snap-stanford/snap.git
$ cd snap
$ make all
$ cd examples/centrality
$ make
```

I modified their centrality.cpp code so it only computed and output the pagerank
```
#include "stdafx.h"

int main(int argc, char* argv[]) {
  Env = TEnv(argc, argv, TNotify::StdNotify);
  Env.PrepArgs(TStr::Fmt("Node Centrality. build: %s, %s. Time: %s", __TIME__, __DATE__, TExeTm::GetCurTm()));
  TExeTm ExeTm;
  Try
  const TStr InFNm = Env.GetIfArgPrefixStr("-i:", "../as20graph.txt", "Input un/directed graph");
  const TStr OutFNm = Env.GetIfArgPrefixStr("-o:", "node_centrality.tab", "Output file");
  printf("Loading %s...", InFNm.CStr());
  PNGraph Graph = TSnap::LoadEdgeList<PNGraph>(InFNm);
  //PNGraph Graph = TSnap::GenRndGnm<PNGraph>(10, 10);
  //TGraphViz::Plot(Graph, gvlNeato, InFNm+".gif", InFNm, true);
  printf("nodes:%d  edges:%d\n", Graph->GetNodes(), Graph->GetEdges());
  PUNGraph UGraph = TSnap::ConvertGraph<PUNGraph>(Graph); // undirected version of the graph
  TIntFltH BtwH, EigH, PRankH, CcfH, CloseH, HubH, AuthH;
  //printf("Computing...\n");
  printf("Treat graph as DIRECTED: ");
  printf(" PageRank... ");             TSnap::GetPageRank(Graph, PRankH, 0.85);
  /*
  printf(" Hubs&Authorities...");      TSnap::GetHits(Graph, HubH, AuthH);
  printf("\nTreat graph as UNDIRECTED: ");
  printf(" Eigenvector...");           TSnap::GetEigenVectorCentr(UGraph, EigH);
  printf(" Clustering...");            TSnap::GetNodeClustCf(UGraph, CcfH);
  printf(" Betweenness (SLOW!)...");   TSnap::GetBetweennessCentr(UGraph, BtwH, 1.0);
  printf(" Constraint (SLOW!)...");    TNetConstraint<PUNGraph> NetC(UGraph, true);
  printf(" Closeness (SLOW!)...");
  for (TUNGraph::TNodeI NI = UGraph->BegNI(); NI < UGraph->EndNI(); NI++) {
    const int NId = NI.GetId();
    CloseH.AddDat(NId, TSnap::GetClosenessCentr(UGraph, NId));
  }
  */
  printf("\nDONE! saving...");
  FILE *F = fopen(OutFNm.CStr(), "wt");
  fprintf(F,"#Network: %s\n", InFNm.CStr());
  fprintf(F,"#Nodes: %d\tEdges: %d\n", Graph->GetNodes(), Graph->GetEdges());
  //  fprintf(F,"#NodeId\tDegree\tCloseness\tBetweennes\tEigenVector\tNetworkConstraint\tClusteringCoefficient\tPageRank\tHubScore\tAuthorityScore\n");
  fprintf(F,"#NodeId\tPageRank\n");
  for (TUNGraph::TNodeI NI = UGraph->BegNI(); NI < UGraph->EndNI(); NI++) {
    const int NId = NI.GetId();
    /*
    const double DegCentr = UGraph->GetNI(NId).GetDeg();
    const double CloCentr = CloseH.GetDat(NId);
    const double BtwCentr = BtwH.GetDat(NId);
    const double EigCentr = EigH.GetDat(NId);
    const double Constraint = NetC.GetNodeC(NId);
    const double ClustCf = CcfH.GetDat(NId);
    */
    const double PgrCentr = PRankH.GetDat(NId);
    /*
    const double HubCentr = HubH.GetDat(NId);
    const double AuthCentr = AuthH.GetDat(NId);
    fprintf(F, "%d\t%f\t%f\t%f\t%f\t%f\t%f\t%f\t%f\t%f\n", NId, 
      DegCentr, CloCentr, BtwCentr, EigCentr, Constraint, ClustCf, PgrCentr, HubCentr, AuthCentr);
    */
    fprintf(F, "%d\t%f\n", NId, 
       PgrCentr);
  }
  fclose(F);
  Catch
  printf("\nrun time: %s (%s)\n", ExeTm.GetTmStr(), TSecTm::GetCurTm().GetTmStr().CStr());
  return 0;
}
```

## Run Page rank

```
$ make
$ ./centrality
Node Centrality. build: 19:55:10, Mar 27 2016. Time: 19:58:45 [Mar 27 2016]
===========================================================================
usage: centrality
   -i:Input un/directed graph (default:'../as20graph.txt')
   -o:Output file (default:'node_centrality.tab')
Loading ../as20graph.txt...nodes:6474  edges:26467
Treat graph as DIRECTED:  PageRank... 
DONE! saving...
run time: 0.02s (19:58:45)
```


## You can reference the python script as well...
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