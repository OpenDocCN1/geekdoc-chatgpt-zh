```
packageJobJar: [] [/usr/hdp/2.6.5.3004-13/hadoop-mapreduce/hadoop-streaming-2.7.3.2.6.5.3004-13.jar] /tmp/streamjob2764990629848702405.jar tmp-Dir=null
...
18/12/05 16:46:25 INFO mapred.FileInputFormat: Total input paths to process : 1
18/12/05 16:46:26 INFO mapreduce.JobSubmitter: number of splits:2
...
18/12/05 16:46:26 INFO mapreduce.JobSubmitter: The url to track the job: http://hn0-paulte.y3nghy5db2zkehav5m0opgrjxb.cx.internal.cloudapp.net:8088/proxy/application_1543953844228_0025/
...
18/12/05 16:46:35 INFO mapreduce.Job: map 0% reduce 0%
18/12/05 16:46:43 INFO mapreduce.Job: map 50% reduce 0%
18/12/05 16:46:44 INFO mapreduce.Job: map 100% reduce 0%
18/12/05 16:46:48 INFO mapreduce.Job: map 100% reduce 100%
18/12/05 16:46:50 INFO mapreduce.Job: Job job_1543953844228_0025 completed successfully
18/12/05 16:46:50 INFO mapreduce.Job: Counters: 49
File System Counters
FILE: Number of bytes read=156411
FILE: Number of bytes written=813764
...
Job Counters
Launched map tasks=2
Launched reduce tasks=1
...
Map-Reduce Framework
Map input records=5260
Map output records=25956
Map output bytes=104493
Map output materialized bytes=156417
Input split bytes=346
Combine input records=0
Combine output records=0
Reduce input groups=19
Reduce shuffle bytes=156417
Reduce input records=25956
Reduce output records=2519
Spilled Records=51912
Shuffled Maps =2
Failed Shuffles=0
Merged Map outputs=2
GC time elapsed (ms)=193
CPU time spent (ms)=4440
Physical memory (bytes) snapshot=1942798336
Virtual memory (bytes) snapshot=8463282176
Total committed heap usage (bytes)=3177185280
...
18/12/05 16:46:50 INFO streaming.StreamJob: Output directory: /example/wordlengthsoutput
```

# 查看单词计数

Hadoop MapReduce将其输出保存到了HDFS，所以要查看实际的单词计数，需要执行以下命令，查看集群中HDFS的文件：

```
hdfs dfs -text /example/wordlengthsoutput/part-00000
```

下面是上述命令的结果：