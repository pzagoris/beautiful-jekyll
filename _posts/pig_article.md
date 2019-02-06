
It is a a platform for analyzing large data sets (big data).

Infrastructure layer 
  Consists of a compiler that produces sequences of Map-Reduce programs
  
 Language layer 
  Consists of a textual language called Pig Latin, which has the following key properties
  
  Execution engine:
    - Map reduce
    - Tez
    - Spark
    
    
    Pig provides an engine for executing data flows in parallel on Apache Hadoop.
It includes a language, Pig Latin, for expressing these data flows.


Pig Latin is a data flow language. This means it allows users to describe    
how data from one or more inputs should be read, processed, and then
stored to one or more outputs in parallel.


Pig can currently run on MapReduce and Tez, with work ongoing to make it run on Spark.


When Pig is running MapReduce as the execution engine, it compiles the Pig Latin scripts 
that users write into a series of one or more MapReduce jobs that it then executes


Pig is intended to be a language for parallel data processing. It is not tied to one particular parallel framework. 
It has been implemented first on Hadoop, but we do not intend that to be only on Hadoop.


///////////////////////////

Pig’s data types can be divided into two categories: scalar types, which contain a single value, and complex types, 
which contain other type


load a schema

1) tableName = load '.....' as
    (colname:coltype, ...);

2) tableName = load '....' as (coltype);
the data type is assumed to be bytearray
