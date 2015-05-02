## Status
GraphLab PowerGraph is no longer in active development by the founding team. GraphLab PowerGraph is now supported by the community at [http://forum.dato.com/](http://forum.dato.com/).  

## Building

The current version of GraphLab PowerGraph was tested on Ubuntu Linux 64-bit 10.04,  11.04 (Natty), 12.04 (Pangolin) as well as Mac OS X 10.7 (Lion) and Mac OS X 10.8 (Mountain Lion). It requires a 64-bit operating system.

# Dependencies

To simplify installation, GraphLab PowerGraph currently downloads and builds most of its required dependencies using CMake’s External Project feature. This also means the first build could take a long time.

There are however, a few dependencies which must be manually satisfied.

* On OS X: g++ (>= 4.2) or clang (>= 3.0) [Required]
  +  Required for compiling GraphLab.

* On Linux: g++ (>= 4.3) or clang (>= 3.0) [Required]
  +  Required for compiling GraphLab.

* *nix build tools: patch, make [Required]
   +  Should come with most Mac/Linux systems by default. Recent Ubuntu version will require to install the build-essential package.

* zlib [Required]
   +   Comes with most Mac/Linux systems by default. Recent Ubuntu version will require the zlib1g-dev package.

* Open MPI or MPICH2 [Strongly Recommended]
   + Required for running GraphLab distributed. 

* JDK 6 or greater [Optional]
   + Required for HDFS support 

## Satisfying Dependencies on Mac OS X

Installing XCode with the command line tools (in XCode 4.3 you have to do this manually in the XCode Preferences -&gt; Download pane), satisfies all of these dependencies.

## Satisfying Dependencies on Ubuntu

All the dependencies can be satisfied from the repository:

    sudo apt-get update
    sudo apt-get install gcc g++ build-essential libopenmpi-dev openmpi-bin default-jdk cmake zlib1g-dev git

# Downloading GraphLab PowerGraph

You can download GraphLab PowerGraph directly from the Github Repository. Github also offers a zip download of the repository if you do not have git.

The git command line for cloning the repository is:

    git clone https://github.com/graphlab-code/graphlab.git
    cd graphlab


# Compiling and Running

```
./configure
```

In the graphlabapi directory, will create two sub-directories, release/ and debug/ . cd into either of these directories and running make will build the release or the debug versions respectively. Note that this will compile all of GraphLab, including all toolkits. Since some toolkits require additional dependencies (for instance, the Computer Vision toolkit needs OpenCV), this will also download and build all optional dependencies.

We recommend using make’s parallel build feature to accelerate the compilation process. For instance:

```
make -j4
```

will perform up to 4 build tasks in parallel. When building in release/ mode, GraphLab does require a large amount of memory to compile with the heaviest toolkit requiring 1GB of RAM.

Alternatively, if you know exactly which toolkit you want to build, cd into the toolkit’s sub-directory and running make, will be significantly faster as it will only download the minimal set of dependencies for that toolkit. For instance:

```
cd release/toolkits/graph_analytics
make -j4
```

will build only the Graph Analytics toolkit and will not need to obtain OpenCV, Eigen, etc used by the other toolkits.

## Compilation Issues
If you encounter issues please post the following on the [GraphLab forum](http://forum.graphlab.com).

* detailed description of the problem you are facing
* OS and OS version
* output of uname -a
* hardware of the machine
* utput of g++ -v and clang++ -v
* contents of graphlab/config.log and graphlab/configure.deps

# Writing Your Own Apps

There are two ways to write your own apps.

* To work in the GraphLab PowerGraph source tree, (recommended)
* Install and link against Graphlab PowerGraph (not recommended)


## 1:  Working in the GraphLab PowerGraph Source Tree

This is the best option if you just want to try using GraphLab PowerGraph quickly. GraphLab PowerGraph
uses the CMake build system which enables you to quickly create
a C++ project without having to write complicated Makefiles. 

1. Create your own sub-directory in the apps/ directory. for example apps/my_app
   
2. Create a CMakeLists.txt in apps/my_app containing the following lines:

    project(GraphLab) 
    add_graphlab_executable(my_app [List of cpp files space separated]) 

3. Substituting the right values into the square brackets. For instance:

    project(GraphLab) 
    add_graphlab_executable(my_app my_app.cpp) 

4. Running "make" in the apps/ directory of any of the build directories 
should compile your app. If your app does not show up, try running

    cd [the GraphLab API directory]
    touch apps/CMakeLists.txt


## 2: Installing and Linking Against GraphLab PowerGraph

To install and use GraphLab PowerGraph this way will require your system
to completely satisfy all remaining dependencies, which GraphLab PowerGraph normally 
builds automatically. This path is not extensively tested and is 
**not recommended**

You will require the following additional dependencies
 - libevent (>=2.0.18)
 - libjson (>=7.6.0)
 - libboost (>=1.53)
 - libhdfs (required for HDFS support)
 - tcmalloc (optional)

Follow the instructions in the [Compiling] section to build the release/ 
version of the library. Then cd into the release/ build directory and 
run make install . This will install the following:

* include/graphlab.hpp
 +   The primary GraphLab header 
*  include/graphlab/...
 +   The folder containing the headers for the rest of the GraphLab library 
*  lib/libgraphlab.a
 +   The GraphLab static library.
    
Once you have installed GraphLab PowerGraph you can compile your program by running:

```
g++ -O3 -pthread -lzookeeper_mt -lzookeeper_st -lboost_context -lz -ltcmalloc -levent -levent_pthreads -ljson -lboost_filesystem -lboost_program_options -lboost_system -lboost_iostreams -lboost_date_time -lhdfs -lgraphlab hello_world.cpp
```
    
If you have compiled with MPI support, you will also need

   -lmpi -lmpi++ 
   
# Tutorials
See [tutorials](TUTORIALS.md)

# Datasets
The following are data sets links we found useful when getting started with GraphLab PowerGraph.

##Social Graphs
* [Stanford Large Network Dataset (SNAP)](http://snap.stanford.edu/data/index.html)
* [Laboratory for Web Algorithms](http://law.di.unimi.it/datasets.php)

##Collaborative Filtering
* [Million Song dataset](http://labrosa.ee.columbia.edu/millionsong/)
* [Movielens dataset GroupLens](http://grouplens.org/datasets/movielens/)
* [KDD Cup 2012 by Tencent, Inc.](https://www.kddcup2012.org/)
* [University of Florida sparse matrix collection](http://www.cise.ufl.edu/research/sparse/matrices/)

##Classification
* [Airline on time performance](http://stat-computing.org/dataexpo/2009/)
* [SF restaurants](http://missionlocal.org/san-francisco-restaurant-health-inspections/)

##Misc
* [Amazon Web Services public datasets](http://aws.amazon.com/datasets)
  
# Release Notes
##### **map_reduce_vertices/edges and transform_vertices/edges are not parallelized on Mac OS X**

These operations currently rely on OpenMP for parallelism.

On OS X 10.6 and earlier, gcc 4.2 has several OpenMP bugs and is not stable enough to use reliably.

On OS X 10.7, the clang
++ compiler does not yet support OpenMP.

##### **map_reduce_vertices/edges and transform_vertices/edges use a lot more processors than what was specified in –ncpus**

This is related to the question above. While there is a simple temporary solution (omp_set_num_threads), we intend to properly resolve the issue by not using openMP at all.

##### **Unable to launch distributed GraphLab when each machine has multiple network interfaces**

The communication initialization currently takes the first non-localhost IP address as the machine’s IP. A more reliable solution will be to use the hostname used by MPI.
