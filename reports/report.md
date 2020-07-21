#Test Case
Assuming that ZIP has to matched exactly. Main dataset is filtered down to zip level. Within that subset, fuzzy matching
is done on 3 different columns and sorted to find the best match. 20 features are returned from the dataset.

First pass will be done using the zip code 96162 with 1300 records, a second pass will be
done using the zip 94533 which has 35630 records.

## Solution 1: EMR with Handler

Using EMR with the Epislon data cached in memory, 
distributed computing can be done to quickly calculate levenshtein distance
and return 20 features back for modelling.

From testing, calculating levenshtein distance on 3 different variables over 18918306 records.
Roughly 1.5 second response time.

Solution would be advisable if fuzzy matching was done on a larger dataset, given the smaller data volumes after 
filtering on zip. This would not be advisable.

#### Constant Cost

 Server Type             |   Quantity |   EC2 Cost |   EMR Cost |   Cost per unit |   Total Cost |
:------------------------|-----------:|-----------:|-----------:|----------------:|-------------:|
 m1.large               |          1 |      0.175 |      0.048 |            0.223 |         0.24 |
 c5.4xlarge              |          1 |      0.68  |      0.17  |            0.85 |         0.85 |
 kafka.m5.xlarge (kafka) |          1 |    nan     |    nan     |            0.42 |         0.42 |

This leaves us with a total consistent cost of $1.51 per hour.

It is recommended to have at least 2 clusters for redundancy and responsiveness.

#### Variable Cost
##### Storage
3.2 GB storage for s3 files ($0.0736) per month

Peak of ~3000 requests per day. ($0.005 per 1000 requests)

##### Request Handling
Assuming AWS Lambda is used (will investigate Kenesis)
2gb Lambda instance. Assuming typical request takes 10 seconds to process start to finish
10000ms of compute needed per request ($0.0000033333 per 100ms)

Alternatively, Kafka can be used to handle requests with similar costs.

Approx $0.00033333 per request

## Solution 2: Snowflake
Same setup as AWS EMR test. Using the 'large' type warehouse, returns 20 columns
and sorted by Levenshtein distance in ~1-3 seconds. 

Cost 1 credits/hour, roughly equates to $3 per hour. 

Storage costs are similar to EMR.

A lambda function will be used to handle the request

## Solution 4: Redshift
Similar process to Snowflake, except all processing will be done inside AWS Lambda function
dc2.large instances will be used at $0.25 per hour.

4 core instance will be $1 per hour making monthly costs $720


## Solution 5: ElastiCache (Redis)
cache.m4.xlarge	can be used as a starting point. $0.311 per hour. Having 2 instances opens up 8 processing cores with 
28GB of memory. 

Running cost at $0.622 per hour.


## Development Time
40 hours initial POC/Tech stack decision

40 hours, working dev env

80 hours, full production system