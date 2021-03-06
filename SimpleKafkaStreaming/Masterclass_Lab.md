# A Simple Spark Streaming example using Apache Kafka

In this example you will send strings of text (space separated words) to a Kakfka Topic and use Spark Streaming to do a simple wordcount on this text. In order to have som variation, you will us an existing document from which to get random sentences.

The Spark streaming job will read the text events from the topic, calculate the word counts and print te results.

## Step 1 - Set up a Kafka topic to stream from 


**This Step 1 is optional: A global Kafka topic has been prepared for you so you can use the information provided by the instructor to connect to that Kafka broker.**
 
### Create a Kafka topic if you don't have one yet:

    su - kafka
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh \
        --create --zookeeper localhost:2181 \
        --replication-factor 1 \ 
        --partitions 1 \ 
        --topic sparkstreaming

You can check if the topic now exists:

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper localhost:2181


### Send text to the Kafka topic


    su - kafka
    wget http://www.gutenberg.org/cache/epub/2600/pg2600.txt -O War_and_Peace.txt
    ./loop.sh ./War_and_Peace.txt | \
        /usr/hdp/current/kafka-broker/bin//kafka-console-producer.sh \
        --broker-list 192.168.2.53:6667 \ 
        --topic sparkstreaming


This command will run until you press Ctrl-C. It does not print anything on the terminal.


## Step 2 - Start the Spark Streaming job

Use the [direct_kafka_wordcount.py](https://raw.githubusercontent.com/anset/SparkDemos/master/SimpleKafkaStreaming/direct_kafka_wordcount.py) python script that has the pyspark code for this example.

    su - spark
    spark-submit --packages org.apache.spark:spark-streaming-kafka_2.10:1.5.2 \
                 --master yarn-client \
                 ./direct_kafka_wordcount.py 192.168.2.53:6667 sparkstreaming

**Note:** Make sure that the ip address and port on the last part of this command points the Kafka broker 

Once started, this script will continue to run until you press Ctrl-C. After the spark context is created, verbose logging is disabled so the real output can easily be seen:

```
...
16/02/25 08:45:52 INFO Client: 
	 client token: N/A
	 diagnostics: N/A
	 ApplicationMaster host: 192.168.2.56
	 ApplicationMaster RPC port: 0
	 queue: default
	 start time: 1456407941242
	 final status: UNDEFINED
	 tracking URL: http://server02.anset.org:8088/proxy/application_1456392465190_0007/
	 user: spark
16/02/25 08:45:52 INFO YarnClientSchedulerBackend: Application application_1456392465190_0007 has started running.
16/02/25 08:45:52 INFO Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 37885.
16/02/25 08:45:52 INFO NettyBlockTransferService: Server created on 37885
16/02/25 08:45:52 INFO BlockManagerMaster: Trying to register BlockManager
16/02/25 08:45:52 INFO BlockManagerMasterEndpoint: Registering block manager 192.168.2.52:37885 with 530.0 MB RAM, BlockManagerId(driver, 192.168.2.52, 37885)
16/02/25 08:45:52 INFO BlockManagerMaster: Registered BlockManager
16/02/25 08:45:52 INFO YarnHistoryService: Application started: SparkListenerApplicationStart(PythonStreamingDirectKafkaWordCount,Some(application_1456392465190_0007),1456407929435,spark,None,None)
16/02/25 08:45:52 INFO YarnHistoryService: About to POST entity application_1456392465190_0007 with 3 events to timeline service http://server02.anset.org:8188/ws/v1/timeline/
16/02/25 08:45:58 INFO YarnClientSchedulerBackend: Registered executor: AkkaRpcEndpointRef(Actor[akka.tcp://sparkExecutor@server06.anset.org:46283/user/Executor#1389738519]) with ID 2
16/02/25 08:45:59 INFO BlockManagerMasterEndpoint: Registering block manager server06.anset.org:46957 with 530.0 MB RAM, BlockManagerId(2, server06.anset.org, 46957)
16/02/25 08:46:01 INFO YarnClientSchedulerBackend: Registered executor: AkkaRpcEndpointRef(Actor[akka.tcp://sparkExecutor@server05.anset.org:58707/user/Executor#-1987030888]) with ID 1
16/02/25 08:46:01 INFO YarnClientSchedulerBackend: SchedulerBackend is ready for scheduling beginning after reached minRegisteredResourcesRatio: 0.8


======================================
= Batch Result @ 2016-02-25 08:46:20 =
======================================

Number of distinct words found = 82

Top 100 words counted list:

[(u'the', 9), (u'', 5), (u'he', 5), (u'of', 4), (u'not', 3), (u'at', 3), (u'that', 3), (u'his', 3),
(u'and', 2), (u'had', 2), (u'as', 2), (u'was', 2), (u'but', 2), (u'pistol', 2), (u'"Father', 2), 
(u'He', 2), (u'pupils,', 1), (u'words', 1), (u'being', 1), (u'over', 1), (u'raised', 1), 
(u'aimed.', 1), (u'best', 1), (u'brightness,', 1), (u'looking', 1), (u'her', 1), (u'muddy', 1), 
(u'Natasha,', 1), (u'him.', 1), (u'behind', 1), (u'freshly', 1), (u'trigger,', 1), (u'contrary', 1), 
(u'varnished.', 1), (u'unnatural,', 1), (u'unpleasant,', 1), (u'merry', 1), (u"gypsies',", 1), 
(u'CHAPTER', 1), (u'with', 1), (u'change', 1), (u'a', 1), (u'sister', 1), (u'strength.', 1), 
(u'did', 1), (u'exceptionally', 1), (u'Kamenski"', 1), (u'Down', 1), (u'replaced', 1), (u'fact', 1), 
(u'house', 1), (u'it', 1), (u'an', 1), (u'held', 1), (u'recollection', 1), (u'in', 1), (u'girlish', 1),
(u'seen', 1), (u'clearly', 1), (u'before', 1), (u'Sometimes', 1), (u'no', 1), (u"Rostovs'", 1), 
(u'whom', 1), (u'unaccustomed', 1), (u'therefore', 1), (u'recognize', 1), (u'hand--a', 1), (u'gave', 1), 
(u'road.', 1), (u'though', 1), (u'who', 1), (u'heard', 1), (u'how', 1), (u'Otradnoe.', 1), 
(u'below,', 1), (u'by', 1), (u'cries', 1), (u'VII', 1), (u'glittering', 1), (u'were', 1), (u'result', 1)]

======================================

...

```

## Step 3 - Monitor job progression in the Spark History Server UI

First make sure you have the correct Hostory server: Use the "quick Links" on the YARN page in Ambari to go to the "Resource manager UI". Find the spark jib in the list and click on the application ID to access the application details page. In this page, you can click on the "ApplicationMaster link" which will connect you to the correct Spark History Server instance for your streaming job.

 You will notice the presence of a "Streaming" tab where you can follow the execution of the streaming job.
 
 ![Spark History Server Streaming Statistics](https://raw.githubusercontent.com/anset/SparkDemos/master/SimpleKafkaStreaming/HistoryServerStreamingStatistics.png)

