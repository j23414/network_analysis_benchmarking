# Our Method

Fetch our method from bitbucket. Please contact Jennifer for password
```
git clone http://ComplexGuest@bitbucket.org/complexcomputation/cpugpu-graph.git
# Enter password here
cd cpugpu-graph
less README.txt
make # may need to modify Makefile depending on your system
unzip data.zip
./pagerank data/t.tsv > output.txt
```

## Run Benchmarks on all graphs
```
#! /usr/bin/env bash

# repeat 5 times or however many replicates
./pagerank c.tsv 2>> out.txt
./pagerank g.tsv 2>> out.txt
./pagerank h.tsv 2>> out.txt
./pagerank m.tsv 2>> out.txt
./pagerank r.tsv 2>> out.txt
./pagerank s.tsv 2>> out.txt
./pagerank t.tsv 2>> out.txt
./pagerank y.tsv 2>> out.txt
./pagerank z.tsv 2>> out.txt
```

Then fetch the benchmark data into a tsv

Perl script
```

```