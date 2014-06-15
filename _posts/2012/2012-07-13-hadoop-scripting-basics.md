---
layout: post
name: hadoop-scripting-basics
title: Hadoop scripting basics (draft)
time: 2012-07-13 10:56:00 +08:00
categories: [SysAdmin]
tags:
- Hadoop
- Streaming
---

Links:

[Hadoop Website](http://hadoop.apache.org/)

[Hadoop at Yahoo!](http://www.slideshare.net/acarlos1000/hadoop-basics-presentation)

Setup
======

TODO

Command line basics
======

ENVIRONMENT VARIABLES
------

in ~/.bash_profile

    # User specific environment and startup programs
    JAVA_HOME=/opt/java
    HADOOP_HOME=/opt/hadoop
    HIVE_HOME=/opt/hive
    
    PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HIVE_HOME/bin:$PATH:$HOME/bin
    
    export PATH JAVA_HOME HADOOP_HOME


HDFS - Walk around
------

    hadoop fs -ls /
    hadoop fs -du -h /data/
    hadoop fs -dus /user/_myname_/
    hadoop fs -tail /user/_myname_/my-test-file.txt

Submit Map/Reduce Job(jar)
------

Job Tracker
------

TODO

Streaming
======
Streaming(script)
------

Usage:

     hadoop jar $STREAMING_JAR \
     -mapper "$MAPPER" -file $MAPPER_FILE \
     -reducer "$REDUCER" -file $REDUCER_FILE \
     -input $RAW -output $SUMMARY \
     -numReduceTasks 1

    $STREAMING_JAR, $MAPPER_FILE, $REDUCER_FILE: files in local path.

    $MAPPER，$REDUCER: commands run by hadoop.

    $RAW, $SUMMARY: files in HDFS.

Streaming examples
------
EXAMPLES: streaming/wc-demo.sh
