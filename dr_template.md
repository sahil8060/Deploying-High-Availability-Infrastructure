# Infrastructure

## AWS Zones
Identify your zones here

| Regions            | us-east-2                              | us-west-1                             |
|--------------------|----------------------------------------|---------------------------------------|
| Organzation Zones  | Primary Zone                           | Disaster Recovery (DR) Secondary Zone |
| Availability Zones | "us-east-2a","us-east-2b","us-east-2c" | "us-west-1a","us-west-1c"             |



## Servers and Clusters

### Table 1.1 Summary

#### Primary Zone us-east-2 assets


| Asset                         | Purpose                                                                                           | Size                                                                     | Qty                                                             | DR                                                                                                             |
|-------------------------------|---------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| Asset name                    | Brief description                                                                                 | "AWS size eg. t3.micro (if applicable, not all assets will have a size)" | Number of nodes/replicas or just how many of a particular asset | "Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere" |
| EC2 Instances                 | EC2 Instances are used to the Application for **Primary Zone** of organization                    | t3.micro                                                                 | 3                                                               | Replicated for us-west-1                                                                                       |
| EKS Cluster                   | Elastic Kubernetes Service (EKS) Clusters is used for monitoring **Primary Zone** of organization | t3.medium                                                                | 1 cluster with 2 nodes                                          | Replicated for us-west-1                                                                                       |
| VPC                           | Virtual Private network for multiple availability zones                                           |                                                                          |                                                                 | Replicated for us-west-1                                                                                       |
| Load Balancer                 | Load Balancers will automatically scale in and scale out the resources based on the traffic.      |                                                                          |                                                                 | Replicated for us-west-1                                                                                       |
| RDS Database Cluster  Primary | RDS Cluster is used to stored the data in **Primary Zone**                                        | db.t2.small                                                              | 1 cluster with 2 nodes                                          | Replicated for us-west-1                                                                                       |


#### Disaster Recovery (DR) Secondary Zone us-west-1 assets

| Asset                          | Purpose                                                                                             | Size                                                                     | Qty                                                             | DR                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| Asset name                     | Brief description                                                                                   | "AWS size eg. t3.micro (if applicable, not all assets will have a size)" | Number of nodes/replicas or just how many of a particular asset | "Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere" |
| EC2 Instances                  | EC2 Instances are used to the Application for **Secondary Zone** of organization                    | t3.micro                                                                 | 3                                                               | Deployed to DR                                                                                                 |
| EKS Cluster                    | Elastic Kubernetes Service (EKS) Clusters is used for monitoring **Secondary Zone** of organization | t3.medium                                                                | 1 cluster with 2 nodes                                          | Deployed to DR                                                                                                 |
| VPC                            | Virtual Private network for multiple availability zones                                             |                                                                          |                                                                 | Deployed to DR                                                                                                 |
| Load Balancer                  | Load Balancers will automatically scale in and scale out the resources based on the traffic.        |                                                                          |                                                                 | Deployed to DR                                                                                                 |
| RDS Database Cluster Secondary | RDS Database Cluster is used for replicated data from **Primary Zone** data                         | db.t2.small                                                              |                                                                 | Deployed to DR                                                                                                 |
                                                                                          |

GitHub Repository outside the Primary and Secondary Zones

| Asset             | Purpose                                                                                                                  | Size                                                                     | Qty                                                             | DR                                                                                                             |
|-------------------|--------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------|-----------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------|
| Asset name        | Brief description                                                                                                        | "AWS size eg. t3.micro (if applicable, not all assets will have a size)" | Number of nodes/replicas or just how many of a particular asset | "Identify if this asset is deployed to DR, replicated, created in multiple locations or just stored elsewhere" |
| GitHub Repository | The IaC that is used to provision the infrastructure, and run the application is stored in the Github Repository        |                                                                          |                                                                 | Just stored elsewhere than the Primary and Secondary (DR) Zones                                                |


### Descriptions

**EC2 Instances:-** We have 3 EC2 Instances for each Primary and Secondary Zones. Higher number of EC2 ensure the appication is highly available.

**EKS Cluster:-** EKS clusters are used for the monitoing of the application performance using the metrics. We always use the 4 golden signals to ensure application is highly available.
1. Availability
2. Latency
3. Throughput
4. Error Budget

**VPC:-** VPC has multiple availability zones high availability. It helps the to launch AWS resources into a virtual network.
| Availability Zones | "us-east-2a","us-east-2b","us-east-2c" | "us-west-1a","us-west-1c" |
|--------------------|----------------------------------------|---------------------------|


**Load Balancer:-** Both **Primary** and **Secondary** zones have **Load Balancers**. It is used to handle the large scale load on the application. It works on demand when there is high traffic recieved its `scale out` (increase number of instances) the instances or when the traffic is low `scale in` ((decrease number of instances) the resources.

**RDS Database Cluster:-** Both **Primary** and **Secondary** zones have **RDS Database Clusters**. Primary zones and secondary both have **5 days data retention period**. Secondary zone RDS is replicated of the Primary Zone data. If **Primary database** is failed then **Secondary** Zone RDS database will become **Primary** for high availability.


**GitHub Repository:-** The IaC that is used to provision the infrastructure, and run the application is stored in the Github Repository. This Repository can help easily to manage the resources we can remove or add another resource to deploy to the AWS using terraform.


## DR Plan
### Pre-Steps:

Ensure the infrastructure is set up and working in the DR site.
We will have two Primary and Secondary Zones. Primary Zone will be on the `us-east-2` and Secondary Zone will be on the `us-west-1` regions on AWS.


## Steps:

In case of failover the load balancer point the DNS from us-east-2 region to the us-west-1 region.

> Create a cloud load balancer and point DNS to the load balancer. This way you can have multiple instances behind 1 IP in a region. During a failover scenario, you would fail over the single DNS entry at your DNS provider to point to the DR site. This is much more intelligent than pointing to a single instance of a web server.

In case of failover on the `us-east-2` location the replicated database instances in the `us-west-1` region **Relica cluster** will become the **Regional cluster**.

> Have a replicated database and perform a failover on the database. While a backup is good and necessary, it is time-consuming to restore from backup. In this DR step, you would have already configured replication and would perform the database failover. Ideally, your application would be using a generic CNAME DNS record and would just connect to the DR instance of the database.





