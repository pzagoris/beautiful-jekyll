---
layout: post
title: Flume 
subtitle: An introductory tutorial to Apache Flume
tags: [Flume, data ingestion]
image: /img/flumeIcon.PNG
---

### Introduction

In this tutorial, we would try present [Apache Flume](https://flume.apache.org/FlumeUserGuide.html). It is a distributed, reliable, and available system for efficiently collecting, aggregating and moving large amounts of data (log data, traffic data, social-media-generated data, email messages, ...) from many different sources to a centralized data store.

Sqoop and Flume are usually used for [data ingestion](https://whatis.techtarget.com/definition/data-ingestion) (transfer data from other applications and databases to a hadoop cluster). Both are open source technologies developed and maintained by THE APACHE SOFTWARE FOUNDATION Flume and Sqoop as part of the Hadoop ecosystem.

#### System Requirements
- 1) Java 1.8 or later 
- 2) _Sufficient memory for configurations used by sources, channels or sinks_ 
- 3)  _Sufficient disk space for configurations used by channels or sinks_ 
- 4) Read/Write permissions for directories used by agent 

### Flume Vs Sqoop

In the following table, we would try to highlight the differences of the 2 technolodgies.

| Flume | Sqoop | 
|-------|--------|
| works with streaming data | work with structured data sources |
| data loading is event-driven | data loading is not event-driven| 
| collecting and aggregating data because of its distributed nature | parallel data transfers  |
| agent-based architecture |  connector based architecture  |

More information can be found in these two articles [[3]](https://data-flair.training/blogs/apache-sqoop-vs-flume/),[[4]](https://www.dezyre.com/article/sqoop-vs-flume-battle-of-the-hadoop-etl-tools-/176).

### Architecture

![useful image](/assets/flumeDataFlow.PNG)

- **Flume event** : Event is a unit of data flow.
- **Flume agent** : It is a JVM process that hosts the components through which events flow from an external source to the next destination (hop).
- **Source** : Source consumes events delivered to it by an external sources
- **Channel** : The channel is a passive store that keeps the event until it’s consumed by a Flume sink.
- **Sink** : The sink removes the event from the channel and puts it into an external repository like HDFS (via Flume HDFS sink) or forwards it to the Flume source of the next Flume agent (next hop) in the flow.

A flume agent can connect to any number of resources and data stores. Moreover, a flume agent represents only one hope for the data.As a result if data need to be transfered through more than one hop, then we can use a chain of flume agents.

For more information [[1](https://blogs.apache.org/flume/entry/flume_ng_architecture)][[2](https://blog.cloudera.com/blog/2011/12/apache-flume-architecture-of-flume-ng-2/)]

#### Souce
Flume supports many types of [Sources](https://flume.apache.org/releases/content/1.9.0/FlumeUserGuide.html#flume-sources). 

- **Kafka Source**: Kafka Source is an Apache Kafka consumer that reads messages from Kafka topics.
- **Spooling Directory**: A directory to which files are written by an application
- **Syslog** : Log messages written in the syslog format
- **Exec**: Standard output for any Unix command
- ...

The data from the Souce are pushed then to the Channel.

#### Channel
The Channel acts like a buffer(queue) where data stays there until the time that Sink reads it out. Therefore, its capacity is integral to be configured as per the rates of the Source and the Sink, because if a Channel is filled up then it would stop accepting any more data from the Source.

There are many types of [channels](http://flume.apache.org/releases/content/1.9.0/FlumeUserGuide.html#flume-channels): 

- Memomy
- JDBC
- File
- Kafka
- ...


| Memory | File | 
|-------|--------|
| data buffered in memory | data buffered in disk |
| it doesn't guarantee data loss | it guarantees no data loss | 
| fast read/write | slower read/write  |

#### Sink

The Sink will keep to read and remove data from the Channel. However, the data would be removed completely from the Channel only when are written to the end point and Sink inform Channel component for that action.

Flume supports many types of [Sinks](http://flume.apache.org/releases/content/1.9.0/FlumeUserGuide.html#flume-sink-processors)
- HDFS Sink
- Hive 
- Logger
- ElasticSearch
- HTTP
....

{: .box-warning}
**Note:** Except from the in-built Flume components (Source, Channel, Sink), we can create our own custom components.


### Starting an agent

In order to start a flume agent, we need to use the following command:
```markdown
flume-ng agent --conf-file <<Properties_fileName>> --name <<agent_name>>
```
The <<agent_name>> should be inside the <<Properties_fileName>>.

#### Example
For example, lets say that we want to run a flume agent, that would write to the log system and would read from a spool directory.

![useful image](/assets/spool_to_loggerDiagram.PNG)


```markdown
flume-ng agent --conf-file Spool_to_Logger.properties --name AgentSpoolLogger
```

![useful image](/assets/spool_to_loggerPropertiesFile.PNG)
