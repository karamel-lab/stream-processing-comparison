# stream-processing-comparison
This project includes all the Karamel definition files which are required to do the stream processing comparison between Apache Spark vs Apache Flink in public cloud.
 You can [feed each Karamel definition file](https://www.youtube.com/watch?v=tCIA8_2dR14)  file to Karamel to execute its task.

###Step 1 : Deploy the cluster in cloud
--------------
Definition file : [cluster-deployment.yml]()

Above definition file will deploy the following clusters.

| Cluster   | Version |
| :-------: |:-------:| 
| Spark     | 1.5.1   | 
| Flink     | 0.10.1  |  
| Hadoop    | 2.7.1   | 

####Configurations that you might want to change

* EC2 machine type and region:
```
ec2:
  region: us-west-2
  type: m3.xlarge
```

You can override the machine type, region for different node groups.
```
datanodes:
    size: 1
    ec2:
      region: us-west-2
      type: r3.2xlarge
      price: 0.5
```
* Size and bid for spot instances

Using the above configuration, size of a group can be changed with the parameter ```size:``` and ```price: 0.5``` specify using [spot instances](https://aws.amazon.com/ec2/spot/) with a bid of $0.5 per hour. But if you do not need to take the risk of using spot instances, you can remove the line ```price: 0.5``` and Karamel will automatically pick up on-demand instances.


* Memory allocations:

Spark
```
driver_memory: 8192m
executor_memory: 30g
```

Flink
```
jobmanager:
      heap_mbs: '8192'
    taskmanager:
      heap_mbs: '307200'
```
For a fair comparison, you can allocate the same amount of memory for Spark driver_memory and Flink jobmanager. And similarly for Spark executor_memory and Flink taskmanager.


###Step 2 : Run Yahoo streaming experiment
--------------
Definition file: [yahoo-streamingbench.yml](https://github.com/karamel-lab/stream-processing-comparison/blob/master/yahoo-streamingbench.yml)

This experiment generates a data stream and run Spark and Flink streaming jobs to process the generated data stream.

####Configuration changes

* IP to deploy the experiment (***required****)

You should add the public IP address of your master node in [the line](https://github.com/karamel-lab/stream-processing-comparison/blob/master/yahoo-streamingbench.yml#L24) for configuring ```ips:```
```
ips:
      - 54.203.56.51
```

* Spark master URL (***required****)
 This is the Spark master URL deployed by the [cluster-deployment.yml](https://github.com/karamel-lab/stream-processing-comparison/blob/master/cluster-deployment.yml)
```
  spark:
    master:
      url: spark://10.125.97.85:7077
 ```
If no mannual changes were done for ports, you can just edit the IP address of [this paremeter](https://github.com/karamel-lab/stream-processing-comparison/blob/master/yahoo-streamingbench.yml#L18) keeping other parts the same. Add the ***private IP*** of your master node for this configuration. This is because the communication within the cluster will happen using private IPs.

* Streaming Load

This is the number of messages per second to send to be processed and can be configured with this [configuration](https://github.com/karamel-lab/stream-processing-comparison/blob/master/yahoo-streamingbench.yml#L15)
```
attrs:
  streamingExperiment:
    ....
    load: '5000'
```
``` load: '5000'``` sends 5000 messages per second to Kafka for generating the data stream.

You can use the [collectl-monitoring tool](https://github.com/shelan/collectl-monitoring) to monitor the system level performance of these big data engines while executing the TeraSort experiment. Collectl-monitor should be started before you are running the experiment and should be stopped once the experiment is over. You can visit [here](https://github.com/shelan/collectl-monitoring#example-scenario) for an example scenario described for a quick look.

