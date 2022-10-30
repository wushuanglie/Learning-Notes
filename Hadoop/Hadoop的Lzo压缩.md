---
title: Hadoop的Lzo压缩
date: 2022-02-04 08:21:44
category:
- Hadoop
---

# core-site.xml 增加配置：

```xml
<configuration>
    <property>
        <name>io.compression.codecs</name>
        <value>
            org.apache.hadoop.io.compress.GzipCodec,
            org.apache.hadoop.io.compress.DefaultCodec,
            org.apache.hadoop.io.compress.BZip2Codec,
            org.apache.hadoop.io.compress.SnappyCodec,
            com.hadoop.compression.lzo.LzoCodec,
            com.hadoop.compression.lzo.LzopCodec
        </value>
    </property>

    <property>
        <name>io.compression.codec.lzo.class</name>
        <value>com.hadoop.compression.lzo.LzoCodec</value>
    </property>
</configuration>

```

# 压缩



```shell
$hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount \
-Dmapreduce.output.fileoutputformat.compress=true \
-Dmapreduce.output.fileoutputformat.compress.codec=com.hadoop.compression.lzo.LzopCodec \
/input /output
```

# 读取Lzo压缩文件



```shell
#Lzo文件存储在HDFS的/input目录下
$hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.1.3.jar wordcount \
-Dmapreduce.job.inputformat.class=com.hadoop.mapreduce.LzoTextInputFormat \
/input /output
```

# 对Lzo文件创建索引



Lzo文件创建索引之后，便可支持切片

```shell
$hadoop jar share/hadoop/common/hadoop-lzo-0.4.20.jar \
com.hadoop.compression.lzo.DistributedLzoIndexer \
/input/bigtable.lzo

```

