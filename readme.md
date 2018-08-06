## Big Data Properties:
+ amount
+ size
+ inifinity
+ structure
+ complexity

## Data Processing Patterns
+ Batch Processing ==> MapReduce, Pig
+ Stream Processing ==> Storm, Spark
+ interactive processing/SQL ==> Impala, Hive
+ Iterative processing ==> Spark
+ Search ==> Solr

## Hadoop cluster
+ Master Nodes: NameNode(two) and ResourceManager(two)

## HDFS
+ files are divided into blocks of 128MB. Each block is replicated and stored on 3 different datanodes (on different racks)
+ Files are written-once (no random write)
+ HDFS is optimized streaming reads of large files (no random reads)

## MapReduce Concepts
+ Record Reader produces key-value pairs from raw input data
+ Shuffle & Sort ensures that all values for a particular intermediate key go to the same Reducer
+ Output writer produces output folders and files in HDFS
+ A Map/Reduce task process is also called a Mapper/Reducer
+ A task is the execution of one Mapper or Reducer over a slice of data
+ Each Map/Reduce task executes one or more map/reduce functions
+ Map and Reduce phases are executed parallelly
+ Mappers ofen parse, filter and transform data. Reducers often aggregate data using statistical/numeric functions.
+ one Map task per chunck and Reduce tasks in Hadoop is 1 by default (or set by developers).

## Node Failure
+ Compute Node fails
    + Map tasks fail ==> all map tasks of that compute node need to be redone
    + Reduce tasks failes =>= reschduled
+ Master Controller fails ==> MR job has to be restarted

## MapReduce V2
4 types of daemons:
+ ResourceManager: job scheduling
+ ApplicationMaster: task progress monitoring
+ NodeManager: run tasks and send progress reports
+ Job History: archives job metrics and metadata

## Job Execution
+ execute driver on client
+ ResourceManager invokes ApplicationMaster
+ ApplicationMaster invokes NodeManager for task execution

## Data Locality
+ when possible Map tasks are executed on the node where the block of data to be processed is stored
+ when impossible Map tasks transfer the data accross the network
+ Map tasks store their output on local disk
+ No data locality for shuffle & sort
    + Intermediate data is transferred accross the network
    + Reduce Tasks write directly to HDFS

## Shuffle & Sort
+ If possible, in-memory sort using QuickSort
+ else,
    + subsets are sorted in-memory(partition, sort)
    + spill to disk
    + merge on disk

## MR Bottleneck
+ Reduce tasks can only start when all Map tasks are completed
+ Every reduce task has to expect data from every map task but data transfer

## Combiner
+ **GOAL**: reduce amount of intermediate data produced by Mappers
+ act as a mini-reducer
+ run locally on the output of Mappers running on the same compute node, as part of Map phase
+ Some reducers may be used as combiners and some may not

## YARN (ResourceManager)
+ manage all jobs submitted to the cluster
+ invokes ApplicationMaster for each job

## MR Program
+ A hadoop MR program consists of the Mapper, the Reducer and a program called the driver
+ driver
    + run on the client
    + configure the job
    + submit it to the cluster
+ Job class
    + set input and output format
    + set input and output location

## Test MR Program
+ Hadoop can run MR in a single, local process (test locally using localjobrunner)
    + benefit: test incremental changes quickly
    + limitations:
        + Distributed Cache doesn't work
        + the job can only specify a single reducer
        + some beginner mistakes may not be caught
+ LocalJOBRUNNER
    + set in driver
    + use Eclipse
    + use ToolRunner and command line arguments

## Setup & Cleanup
+ Setup
    + **GOAL**: Mapper/Reducer should execute some code once before the map or reduce methods are called
    + the setup method is executed before the first call of map/reduce method
+ Cleanup
    + **GOAL**: perform some actions after all the records have been processed by the Mapper/Reducer
    + the cleanup method is executed before the Mapper/Reducer terminates
    + can also be used for emits

## Partitioner
+ the Partitioner determines to which Reducer each intermediate key and its associated values go
+ default is HashPartitioner which guarantee all pairs with the same key go to the same Reducer
+ Global Sort:
    + single Reducer:
        + easist
        + not always applicable (Not enough disk space and no parallel execution)
    + fixed number of Reducer and for k1 < k2, let partition(k1) <= partition(k2)
        + fixed partitions do not reflect the key distribution ==> skew
    + fixed number of Reducer + sorted output

## Secondary Sort
+ **GOAL**: Sort the values for each key
+ use a composite key as a intermediate key
    + Sort Comparator: compare primary key first, if equal, compare secondary key
    + Group Comparator: compare primary key only and determine which keys and values are passed in a single call to the reducer

## Communication Cost
+ **GOAL**: measure the efficiency of MR algorithm
+ **DEFINITION**: number of key-value pairs that are input to all tasks of the entire MR workflow
+ communication cost per job: 
    + number of key-value pairs that are Mapper input
    + number of key-list-of-value pairs that are Reducer input
+ pairs and stripes: (k^2 - k) / 2 and k-1

## Co-occurrence Recommendation
+ Pros:
    + reflect preferences of other users
    + the item a user is interested in has some similarity with the recommend items
    + same computation for all users
    + same algo for Frequently bought together
+ Cons:
    + only popular items will be recommend
    + same recommendation for the same item for every user
    + not user personalized

## Collaborative Filtering
+ **GOAL**: predict missing ratings & recommend items with high predicted ratings
+ Similarity metrics:
    + Jaccard similarity:
    + Cosine similarity:
    + Pearson similarity:

## Pig
+ data flow language
+ high level data processing
+ Pig is a scripting language for exploring large datasets
+ Use case:
    + help extract valuable info from Web server log files
    + sampling can help you explore a representative portion of a large data set
    + Pig is also widely used for Extract, Transform, and Load(ETL) processing

## Work Flow
+ **GOAL**: submit jobs to the cluster in the correct sequence
+ **Oozie**:
    + work flow engine for MR jobs
    + defines dependencies between jobs
    + works for DAGs
    + use forks and joins
    + write workflows in XML

## Hadoop MR limitation
+ strict frame work
+ slow
+ not interactive
+ Java only
+ little support for processing streaming
+ no ml library

## Apache Spark
+ flexible
+ keep data in memory
+ higher-level abstraction than MR
+ implement jobs in Java, Python and Scala
+ fast
+ interactive shell

## RDD - Resilient Distributed Datasets
+ Data is automatically partitioned
+ RDDs are immutable
+ RDDs can hold any types of element
+ Pair RDDs -> key-value pairs, Double RDDs -> numeric data
+ **Action** return values, **Transformation** defines a new RDD based on the current one
+ **Transformation**: map/flatMap/flatMapValues, keyBy, groupByKey/sortByKey
+ **Action**: take/count/first/saveAsTextFile

## Fast Execution
+ Lazy Execution
+ Pipelining

## Apache Flume
+ Apache Flume is a high-performance system for data collection
+ Benefits:
    + Horizontally-scalable
    + Extensible
    + Reliable

## Stages & Tasks
+ Stages are operations that can run on the same data partitioning in parallel across executors/nodes
+ Tasks within a stage are operations executed by one executor/node that are pipelined together

## Best Tool
+ Java MR or Spark: when you are good at programming and need a flexible framework
+ Impala or SparkSQL: when you need real time reponse with structured data
+ Hive or Pig: when you need support for custom file types or complex data types
+ Big Data Processing:
    + Ingest: Flume
    + Process: Spark, MR, Hive, Pig
    + Analyze: Impala, Spark
    + ML: Spark, MR

## Apache Hive
+ Apache Hive is a high-level abstraction on top of MapReduce
    + use SQL-like language called HiveQL
    + Generates MapReduce jobs that run on the Hadoop cluster
    + turn queries into MR jobs

## Apache HBase
+ A NoSQL distributed database built on HDFS
+ Scales to support very large amounts of data and high throughput

## Apache Impala
+ Impala is a high-performance SQL engine
    + runs on hadoop cluster
    + data stored in HDFS
    + low latency
    + ideal for interactive analysis

