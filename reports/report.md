# Solution 1: EMR with Handler

Using EMR with the Epislon data cached in memory, 
distributed computing can be done to quickly calculate levenshtein distance
and return 20 features back for modelling.

From testing, calculating levenshtein distance on 3 different variables over 18918306 records.
Roughly 5 second response time.

### Constant Cost

 Server Type             |   Quantity |   EC2 Cost |   EMR Cost |   Cost per unit |   Total Cost |
:------------------------|-----------:|-----------:|-----------:|----------------:|-------------:|
 m5.xlarge               |          1 |      0.192 |      0.048 |            0.24 |         0.24 |
 c5.4xlarge              |          5 |      0.68  |      0.17  |            0.85 |         4.25 |
 kafka.m5.xlarge (kafka) |          1 |    nan     |    nan     |            0.42 |         0.42 |

This leaves us with a total consistent cost of $4.91 per hour.

It is recommended to have at least 2 clusters for redundancy and responsiveness.

### Variable Cost
#### Storage
3.2 GB storage for s3 files ($0.0736) per month

Peak of ~3000 requests per day. ($0.005 per 1000 requests)

#### Request Handling
Assuming AWS Lambda is used (will investigate Kenesis)
2gb Lambda instance. Assuming typical request takes 10 seconds to process start to finish
10000ms of compute needed per request ($0.0000033333 per 100ms)

Alternatively, Kafka can be used to handle requests with similar costs.

Approx $0.00033333 per request

# Solution 2: Apache Flink

# Solution 3: AWS Kenesis

# Solution 4: Snowflake



# Development Time
40 hours initial POC/Tech stack decision

40 hours, working dev env

80 hours, full production system