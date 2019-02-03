---
layout: post
title: Sqoop
subtitle: An introductory tutorial to Apache Sqoop
tags: [Sqoop, data ingestion]
image: /img/hello_world.jpeg
---


## Introduction
In this tutorial, we would try present Apache sqoop. It is an Open source tool that is supported by the Apache Software Foundation. According to the project's [pages](http://sqoop.apache.org/) :

_"Sqoop is a tool designed for efficiently transferring bulk data between Apache Hadoop and structured datastores such as relational databases."_

![useful image](/assets/sqoop1.PNG)

Both Sqoop and Flume are usually used for [data ingestion](https://whatis.techtarget.com/definition/data-ingestion). _"With Sqoop, you can import data from a relational database system or a mainframe into HDFS."_ Moreover, it supports many different relational databases.

![useful image](/assets/sqoop.PNG)

## Architecture
![useful image](/assets/sqoopArchit.PNG)

Sqoop consists of the following components: 
- connector
- map-reduce controller  
- metadata

The  metadata  stores information about tables. Connectors component is responsible for the establishing the connection to the databases, while the import/export  of  the  data  is  done through map-reduce job. 

## Sqoop tools

Sqoop is a collection of related tools. According to the latest stable release 1.4.7:
- **import** <br> 
_"Imports an individual table from an RDBMS to HDFS. Records can be stored as text files (one record per line), or in binary representation as Avro or SequenceFiles."_

- **import-all-tables** <br> 
_"Imports a set of tables from an RDBMS to HDFS. Data from each table is stored in a separate directory in HDFS."_

- **import-mainframe** <br>
_"Imports all sequential datasets in a partitioned dataset(PDS) on a mainframe to HDFS."_

- **export** <br>
_"Exports a set of files from HDFS back to an RDBMS. The target table must already exist in the database."_ 

- **validation** <br>
_"Validate the data copied, either import or export by comparing the row counts from the source and the target post copy."_

- **job** 
- **metastore**
- **merge**
- **codegen**
- **create-hive-table**
- **eval**
- **list-databases**
- **help**
- **version**


More information for each of the above tools could be found in the [documentation](http://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).


## Import

Syntax:
```markdown
sqoop import (generic-args) (import-args)
```
According to the documentation of the latest stable [release 1.4.7](http://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html), the common(generic) arguments are:

![useful image](/assets/commonargs_sqoop.PNG)

While the toll specific arguments (import-args) are:
![useful image](/assets/genericarguments_sqoop.PNG)

_**Examples:**_

_Import specific columns from a mysql table to Hadoop HDFS_
```markdown
sqoop import \
	-- connect jdbc:mysql://... \
	--username <<username>>	\
	--password <<password>> \
	--table <<mysql_table_name>> \
	--columns "<<column_name_1>>, <<column_name_2>>" \
	--target-dir <<target_directory_in_HDFS>> \
	--driver <<Driver>>
```

_Import the result of a mysql query to Hadoop HDFS_
```markdown
sqoop import \
	-- connect jdbc:mysql://... \
	--username <<username>>	\
	--password <<password>> \
	--table <<mysql_table_name>> \
	--columns "<<column_name_1>>, <<column_name_2>>" \
	--target-dir <<target_directory_in_HDFS>> \
	--driver <<Driver>>
	--query "select .... and \$CONDITIONS order by ..."
```
_Import all tables from MySQL to Hadoop HDFS_

```markdown
sqoop import-all-tables \
	--connect jdbc:mysql://.. \
	--username <<username>> \
	--password <<password>> \
	--warehouse-dir <<directory>> \
	--exclude-tables "<<table_name1>>,<<table_name2>>" \
	--dirver <<driver>>

```

_Importing data into a Hive table_
```markdown
sqoop import-all-tables \
	--connect jdbc:mysql://.. \
	--username <<username>> \
	--password <<password>> \
	--table <<table_name>>
	--hive-import \
	--hive-database  <<hive_db>> \
	--hive-table <<hive_db_table>> \
	--dirver <<driver>>

```
The hive database should already exist before we run the above command. However, the data are not exported in ORC format. 


_Importing data into Hive in ORC format_
```markdown
sqoop import-all-tables \
	--connect jdbc:mysql://.. \
	--username <<username>> \
	--password <<password>> \
	--table <<table_name>>
	--hcatalog-database <<hive db>> \
	--hcatalog-database <<hive_db_table_>> \ 
	--create-hcatalog-table \
	--hcatalog-storage-stanza "stored as orcfile"\
	--dirver <<driver>>
```


## Export

Syntax:
```markdown
sqoop export (generic-args) (import-args)
```
According to the documentation of the latest stable [release 1.4.7](http://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html), the common(generic) arguments are:

![useful image](/assets/exportgeneric_args.PNG)

While the toll specific arguments (import-args) are:
![useful image](/assets/exportspecific_args.PNG)

_**Examples:**_

_Export a Hive table to Mysql table. The Nysql table should already exist._
```markdown
sqoop export \
	--connect <<connection_msql_string>> \
	--username <<username>> \
	--password <<password>> \
	--export-dir <<directory_in_hadoop>> \
	--table <<mysql_table>> \
	--input-fields-terminated-by '<<delimiter_character>>' \
	--driver <<driver_name>>
```


_Export specific columns from a Hive Table to Mysql table._
```markdown
sqoop export \
	--connect <<connection_msql_string>> \
	--username <<username>> \
	--password <<password>> \
	--export-dir <<directory_in_hadoop>> \
	--table <<mysql_table>> \
	--columns "column3_name_hive_table, column1_name_hive_table" \  *
	--input-fields-terminated-by '<<delimiter_character>>' \
	--driver <<driver_name>>
```
The name of the columns in Hive and Mysql table should match. Moreover in line with the asterisk, we specify the order of the columns.
In case the MySQL table has more columns, then the values of those cells would be NULL.


## Sqoop jobs

_Create a job_
```markdown
sqoop job -- create <<job_name>> 
	....
 ```

_Delete a job_
```markdown
sqoop job --delete <<job_name>> 
```

_Run a job_
```markdown
sqoop job --exec <<job_name>> 
```

When we execute an incremental load job we dont need to keep track of the last updated value, cause this is kept is sqoop metastore and it is done automatically.


_Get a list of all available jobs_
```markdown
sqoop job --list 
```

_Show details for a job_
```markdown
sqoop job --show <<job_name>> 
```

[OOzie](http://oozie.apache.org/) can be used for sqoop jobs scheduling.

{: .box-warning}
**Warning:** 
_Sqoop metastore doesn't store passwords. In order to unable sqoop asking passwords for subsequent executions, we need to update the *sqoop.metastore.client.password property* by setting its value to true._


