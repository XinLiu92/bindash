
BinDash is a command-line software for comparing genomes (including metagenomes and pangenomes) on a typical personal laptop. BinDash is based on binwise densified minhash.
It is extremely fast and memory efficient.
It can handle sequences consisting of terabytes of input data (gzipped or not, in fasta or fastq format). 

# Dependencies:

 - any C++ compiler supporting the C++11 standard
 - CMake version 2.6 or plus
 - zlib 
 
# How to install:
```sh
cd ${PROJECT_ROOT_DIRECTORY}  
mkdir release && cd release
cmake -DCMAKE_BUILD_TYPE=Release ..  
make # For Windows with MSYS Makefiles, the command might be "cd ../ && make" because out-of-source build may or may not be supported on this platform. 
./bindash --help # to see a general help message   
```

For MacOS, the native clang compiler cannot compile without compiling error. It is recommended to install gcc first as follows.

```
# install homebrew if not already done
brew update
brew install gcc@8
GCCPATH=$(ls /usr/local/Cellar/gcc/*/bin/)
export PATH="${GCCPATH}:${PATH}"
# Set CC CXX to /usr/local/Cellar/gcc/*/bin/* as necessary
# Then run cmake as above (a GUI for cmake may also be available for MacOS)
```

# How to run:

The folowing three commands show how to run BinDash:
```sh
bindash sketch --outfname=genomeA.sketch genomeA.fasta
bindash sketch --outfname=genomeB.sketch genomeB.fasta
bindash dist genomeA.sketch genomeB.sketch # print to stdout
```

The output of "bindash dist" consists of several lines. 
Each line has these five tab-separated fields: 
 - query genome (Q)
 - target genome (T) 
 - mutation distance between Q and T
 - p-value for the mutation distance
 - Jaccard Index between Q and T

# How BinDash works:

By default, BinDash uses the optimally densified MinHash proposed by Shrivastava. This MinHash technique allows for efficient compression and fast comparison. 

Basically, compression of a genome is done as follows.
 1. All k-mers of each genome are selected and then transformed into hash values.
 2. The range of all possible hash values are partitioned into some bins.
 3. The smallest hash value in each bin is selected.
 4. If a bin is empty (i.e., has no hash values), then the smallest hash value from the next bin is picked. The definition of the next bin is proposed by Shrivastava.
 5. The lowest (i.e., least significant) b-bits of each hash value are picked to form the signature of the genome.

Two genomes are compared by simply performing b XOR opeations for b bit positions, followed by (b-1) AND operations for these b bit positions. 

# Limitations:

- The k-mer size has to increase as the corresponding genome size increases. A natural solution is to use k-mer frequency instead of k-mer presence/abasence. However, this natural solution relies on a probabilistic model for k-mer frequency. Such model may be heavily genome-dependent.
- Some large-scale genome rearrangement (e.g., chromosome duplication) brings very little change to Jaccard Index and mutation distance. 

# Additional Information:

All suggestions, comments, and feature requests are welcome.

Author: XiaoFei Zhao (cndfeifei AT hotmail DOT com)  
License: Apache 2.0

# Reference

Owen Kaser and Daniel Lemire, Strongly universal string hashing is fast, Computer Journal (2014) 57 (11): 1624-1638. http://arxiv.org/abs/1202.4961

Daniel Lemire, Owen Kaser: Recursive n-gram hashing is pairwise independent, at best, Computer Speech & Language, Volume 24, Issue 4, October 2010, Pages 698-710 http://arxiv.org/abs/0705.4676

Daniel Lemire, The universality of iterated hashing over variable-length strings, Discrete Applied Mathematics 160 (4-5), 2012. http://arxiv.org/abs/1008.1715

Anshumali Shrivastava ; Proceedings of the 34th International Conference on Machine Learning, PMLR 70:3154-3163, 2017.

XiaoFei Zhao; BinDash, software for fast genome distance estimation on a typical personal laptop, Bioinformatics, , bty651, https://doi.org/10.1093/bioinformatics/bty651
