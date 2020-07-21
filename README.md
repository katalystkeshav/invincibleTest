Invincible Brand

Q1. Set up a basic cloud formation template for a EC2-AutoScalingGroup to accommodate the following requirements:

Ans. I have added asg.yml to the repo. I have used target tracking policy, so I didn't need some parameters which were metioned in the task. 

Q2. One of our main challenges is to accommodate huge spikes of traffic, for example - thousands of users online within a couple of minutes. How would you suggest to setup our AutoScalingGroup in order to serve as little 50x errors as possible when scaling aggressively?

Ans. AWS has designed AutoScaling to handle gradual increase in load over a period of time. In our current prod environment we had this type of situation where our capacity was to handle around 2000 req/min but sometimes the no of requests reaches upto 10k to 20k req/min (kind of DDOS). 
This led to downtime but since we were using kubernetes so our applications was able to self heal and started working properly once the spike was over. There is no way to scale capacity to 10 or 20 times in just a few minutes.
Following are the methods we can use to reduce no of 5xx while using AutoScaling -

- By overprovisioning our cost will increase but this is the best method to decrease no of 5xx seen by the users. Even websites like Amazon and Flipkart over provision during sales to handle huge amounts of requests.

- We can use Spot instances to overprovision our AutoScaling group. This reduces our cost upto 70%.

- If we are aware of the time period of the spikes then we can schedule more instances in our AutoScaling group during the time period.

- All the packages must be available in ami except for the artifact.

- We can use Target Tracking policies for scale up and scale down. We can use ASGAverageCPUUtilization and ALBRequestCountPerTarget as our scaling policies.

- By doing load testing on a single instance and whole cluster we can figure out how many requests a single instance and whole cluster will be able to handle.

Q3. Define and describe a method in detail to use a Redis Cluster via ElastiCache as central PHP Session storage for the AutoScalingGroup above.

Ans. FYI currently most of our clients are only using standalone Redis ElastiCache in high availability mode (master slave) and I have mostly worked on applications written in java, python, golang and javascript. Working of one python application is such that it first checks for the data in redis. If data is present then it retrieves the data otherwise it hits the database and then stores the value in redis. Any change made in the database also gets reflected in redis as well.

Compared to standalone redis the redis cluster provides sharding capability and better high availability. We will use cloudformation to create a redis cluster and we can create no of master and slaves as per the requirement. Ideally we can have 3 masters and each master will be in a different availability zone. For each master we will have 2 replicas in different availability zones than its master, our cluster will consist of 9 nodes. We can map  a private domain to configuration endpoint and then we can use that domain in our application.
