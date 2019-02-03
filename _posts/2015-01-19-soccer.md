---
layout: post
title: hive
subtitle: introductory tutorial
---

## Introduction
In this tutorial, we would try present Apache Hive. It is an open source technology that is maintained by THE APACHE SOFTWARE FOUNDATION. 

[_"The Apache Hive ™ data warehouse software facilitates reading, writing, and managing large datasets residing in distributed storage using SQL. Structure can be projected onto data already in storage. A command line tool and JDBC driver are provided to connect users to Hive."_](https://hive.apache.org/)

We need to highlight that Hive is not a database system, but a layer on top of hadoop that allows users to access data on a Hadoop cluster running sql-queries (HiveQL). An sql query is transformed to a map-reduce job that run on the entire cluster.
It is not designed for online transaction processing (OLTP) workloads. It is best used for traditional data warehousing tasks.


### Architecture
![useful image](/assets/system_architecturehive.png)

- UI: The user interface (command line, Web based GUI) for users to submit queries and other operations to the system. 
- Driver: The component which receives the queries. This component fetch APIs modeled on JDBC/ODBC interfaces.
- Compiler: The component that parses the query and generates an execution plan with the help of the table and partition metadata looked up from the metastore.
- Metastore:  The component that stores all the structure information of the various tables and partitions in the warehouse (including column, column type information, the serializers and deserializers, where data are stored).
- Execution Engine: The component which executes the execution plan created by the compiler. The plan is a DAG of stages. 

For more information [[1](https://cwiki.apache.org/confluence/display/Hive/Design)], [[3](https://data-flair.training/blogs/apache-hive-architecture/)].

#### Execution Engine
Hive scripts can be executed in the following 3 script engines:
- map reduce (default)
	hive query is transformed in a series of map reduce jobs
	```markdown
	configuration properties:
	set hive.execution.engine = mr;
	```

- spark
	performs operations in memory and all data transformation happens in memory and not in the disk
	```markdown
	set hive.execution.engine = spark;
	```

- [tez](https://community.hortonworks.com/questions/83394/difference-between-mr-and-tez.html)
	```markdown
	set hive.execution.engine = tez;
	```

### How to Connect to Hive.

ftiakse ena pinaka me tis diafores: 
http://dwgeek.com/difference-between-hive-cli-and-beeline-client-hive-vs-beeline.html/

There are 4 different ways to connect to Hive:
- Hive Command line Interface
- Beeline sql client
- JDBC
- HUE(Hadoop User Experience) via the Beeswax application
 
 Hive command line interface (Hive CLI)
  - Interactive mode
  - Run sql statement from a file
    hive -f query.sql 
  - execute hive and sql queries all at once
    hive -e "<<sql_query>>"


Using Beeline client:    
```markdown
beeline -u jdbc::hive2://<<host_name>>:<<port_number>>
```

For more information about the difference of beeline and HCLI please advise the following [ https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-JDBCClientSampleCode]article().
   

### Data units
Hive data is organized into:

- Databases
- Tables
- Partitions
	Each Table can have one or more partition Keys which determines how the data is stored.
- Buckets
	Data in each partition may in turn be divided into Buckets based on the value of a hash function of some column of the Table. 
	
### Data types
More information about the datatypes could be found in the project documentation wiki [pages](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types). For a short description on the data types you can also check [2](https://www.tutorialspoint.com/hive/hive_data_types.htm)

```markdown
***Important Note:***
	Hive supports complex data types like arrays, maps and structs
```

### Hive Query Language
In this article, we are going to present just the basic commands. For more information about the HIVEQL, please refer to the official [documentation](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

Hive 'Cheat Sheet' from [hortonworks](https://hortonworks.com/blog/hive-cheat-sheet-for-sql-users/)

#### Table
A Table in Hive is a combination of a directory in HDFS and some information in Hives metastore (HCatalog).
 
_Create table_
```markdown
create table <<table_name>> ([<<column_name>> <<column_type>>, ...])
	Location '<<location of the table in hdfs>>';
```

_Display the columns of table_
```markdown
describe <<table_name>>;
```

_Display the statement that was used for the table creation_	
```markdown
show create table <<table_name>>;
```

_Get more information about a table_
```markdown
describe extended <<table name>>;
```

_Get formatted information about a table and where is the location of the table in HDFS_
```markdown
describe formatted <<table name>>:
```
*Important Note*: Every file that is placed in the hdfs location of a hive table is content of that table


_Create an external table_
```markdown
create external table <<table_name>> ([<<column_name>> <<column type>>, ... ])
	Location '<<location of the table in hdfs>>';
```

_What is the difference between an external table and a normal table in Hive?_
When we drop an external-table in Hive only the metadata are dropped, not the directory in HDFS where the data of the table are storred.

_Drop a table_
```markdown
DROP TABLE IF EXISTS <<table_name>>;
```
   
#### Load Data
There are 3 different ways to load data to a table in Hive:

Transfer data files from your local disk to the HDFS directory of the table: 
```markdown
hadoop fs -put <<local_files_in_the_disk>> <<directory location in the hdfs>>
```
Copy data from a local path to the hive table 
```markdown
LOAD DATA LOCAL INPATH '<<file_name>>' into table <<table name>>;
```

Move data from one table to another
```markdown
LOAD DATA  INPATH '<<hdfs_directory_of_source_table>>' into table <<table name>>;
```

check this!!!!!!!!!!	
Hive default record format -----------: check this more
Input data to hdfs directory shouldnt contain crl+A (not printed character) characters
	this character is used as a dilemeter
!!!!!!!!!!!!!!!!!!!!


#### Select statement

```markdown
SELECT [ALL | DISTINCT] select_expr, select_expr, ...
  FROM table_reference
  [WHERE where_condition]
  [GROUP BY col_list]
  [ORDER BY col_list]
  [CLUSTER BY col_list
    | [DISTRIBUTE BY col_list] [SORT BY col_list]
  ]
 [LIMIT [offset,] rows]
 ```

HiveQL supports "distinct", "group by","union", aggregate functions that sql supports (avg, count,min, max, etc). Moreover, it support equality join (not non-equal joins), inner as well as outer (left, right) join. All these functions requires a reduce phase.

More information can be found [here](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Select).

#### Insert Data
The result of sql queries in Hive could be written in a HDFS.

_Append data_
```markdown
insert into table <<table_name>>
	select .... ;
```

_Overwrite data_
```markdown
insert overwrite table <<table name>>
	select .... ;
```
 
The number of directories that are created in the hdfs directory would be as many as the number of reducers that are used for the execution of a query.

_set the number of reducers_
```markdown
set mapred.job.reduces= 4;
```	

_get the hive variables and their values_
```markdown
set -v 
hive -e 'set -v'
```

 
#### Views	 
Hive does not support materialized [views](https://en.wikipedia.org/wiki/Materialized_view) (views that are updated when the tables change).

_Create a View_ 
```markdown
CREATE VIEW [IF NOT EXISTS] <<view_name>> [(<<column_name>> <<column_type>>, ...)]
AS SELECT ...;
```
In HDFS is not created any directory for the view.

_Drop a View_
```markdown
DROP VIEW <<view_name>>;
```
#### Partitions

Hive supports partitioning in table. In the below table are the [configuration properties](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML) that needed to be set.

![useful image](/assets/hive_partioning.PNG)

Data partioning can lead to better performance depending on the query that we need to execute more often.
Moreover, when we perform partion it has as a result the creationg of subdirectories in HDFS under the main folder of the table.
	
#### Functions

##### In build

Display available functions in Hive:
```markdown
show functions;
```

read the description of a function	
```markdown
describe function <<function_name>>;
```

##### User defined functions

Alternatively, Hive gives the opportunity to the users to create their own functions (UDF or UDAF).
A UDF processes one or several **columns of one row** and outputs one value. While UDAF processes **one or several columns of several input rows** and outputs one value. For more information you can advise the [official documentation](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) and this [article](https://blog.dataiku.com/2013/05/01/a-complete-guide-to-writing-hive-udf).

