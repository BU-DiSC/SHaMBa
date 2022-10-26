<H1> SHaMBa codebase: "LSM-Trees Under (Memory) Pressure" </H1>

This repository contains the experiment codes and a modified version of RocksDB which implements SHaMBa: ["LSM-Trees Under (Memory) Pressure"](http://cs-people.bu.edu/mathan/publications/adms22-mun.pdf).

In this work, we observe that, with larger datasets, the size of metadata like index and filters also increases, making it less feasible to keep all Bloom Filters (BFs) in cache.
We introduce SHaMBa, a new LSM-based key-value engine that addresses the suboptimal performance when BFs do not fit in memory. 
SHaMBa integrates a new variation of BF, called Modular Bloom filters (MBFs) that replace a single Bloom filter with a set of mini-BFs modules having the same aggregate size and requiring the same total number of probes, distributed among the modules. Querying MBFs accesses the modules sequentially, resulting in the first module being more frequently in memory while the remaining modules compete with data blocks in case of 
positive queries.
This work is accepted for publication in ADMS 2022. You can access our presentation video [here](https://www.youtube.com/watch?v=sb6jw0zz-CU). 

<H1> Workload Generation </H1>
To run our experiments we also implemented our own workload generator: https://github.com/BU-DiSC/K-V-Workload-Generator
To generate a workload, simply give 

```
make
./load_gen
```

with the desired parameters. These include: Number of inserts, updates, deletes, point & range lookups, distribution styles, etc. 

<H1> Quick How-To </H1>
To run a simple experiment you can follow this guide:

First, clone the workload generator reposirtory and generate a workload. A simple command would be: 

```
./load_gen -I10000 -Q3000
```

This will generate a workload file (e.g., `workload.txt`) with 10000 inserts and 3000 point-queries on existing keys.

To vary the distribution of point queries, you can specify `--ED [ED] --ZD [ZD]`, where `[ED]` and `[ZD]` represent the distribution number for existing and non-existing point queries, respectively (distribution number: 0->uniform, 1->normal, 2->beta, 3->zipf)

More details can be found by running `./load_gen --help`.


After that, make sure that you have also compiled RocksDB. To do that under `rocksdb-mbf` directory and run:
```
make static_lib
```

To run the workload, you need to go to `mbf-benchmark` directory and just give:

```
make
./mbf-benchmark --wp=[path/to/workload]
```

, where `[path/to/workload]` means the path of the workload.txt generated from `load_gen`. The experiment results (e.g., latency or throughput) are printed when the benchmark finishes. More options are available. Type:

```
./mbf-benchmark --help 
```

for more details.
