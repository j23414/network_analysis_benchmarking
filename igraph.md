#Pagerank on igraph

## Installation
```
$ git clone https://github.com/igraph/igraph.git
$ cd igraph
$ ./bootstrap.sh
$ ./configure
$ make
$ make check
$ make install
```

But there were still problems finding shared libraries until I ran the following:

```
$ echo "/usr/local/lib" > igraph.conf
$ sudo mv ~/Desktop/igraph.conf /etc/ld.so.conf.d/igraph.conf
$ sudo ldconfig
$ sudo cp -r include /usr/local/igraph
```

## Compile c code containing igraph functions

```
$ gcc igraph_test.c -I/usr/local/igraph -L/usr/local/lib -ligraph -o igraph_test
```

## Input Output of edgelist

```
#include <igraph.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]){
    igraph_t g;
    int ret;

    FILE *infile;
    infile=fopen(argv[0],"r");

    if (!infile) {
      printf("Cannot open file: %s\n", argv[0]);
      exit(1+i);
    }
    igraph_read_graph_dl(&g, infile, /*directed=*/ 1);
    ret=fclose(infile);
    if (ret) {
      printf("Cannot close file: %s\n", argv[0]);
      exit(101+i);
    }
    

    igraph_write_graph_edgelist(&g, stdout);
    igraph_destroy(&g);
}
```
