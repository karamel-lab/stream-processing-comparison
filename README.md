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
    size: 2
    ec2:
      region: us-west-2
      type: i2.4xlarge
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