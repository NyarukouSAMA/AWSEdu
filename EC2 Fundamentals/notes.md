# What is EC2

EC2 it is Elastic Compute Cloud and this means Infrastructure as a Service.
Infrastructure as a Service consists not only from EC2:
- renting virtual machine (EC2)
- storing data on virtual drives (EBS)
- distributing load across machines (ELB)
- scaling the services using an auto-scaling group (ASG)

But the basement on Infrastructure as a Service is EC2. Knowing of EC2 is fundamental to understand how Cloud works.

# EC2 sizing and config options

When you have a deal with EC2 you can choose or configure next things:
- operating system (OS): Linux, Windows, Mac OS
- how much compute power and cores (CPU)
- how much random-access memory (RAM)
- how mach storage space and type of storage (it could be network-attached, like EBS & EFS, or hardware, like EC2 Instance Store)
- network card: speed of card, Public IP address
- firewall rules: **security group**
- bootstrap script (configure at first launch): EC2 User Data

# EC2 User data

It's possible to bootstrap our instances using an EC2 User data scripts. Bootstrapping means launching commands when a machine starts.

EC2 User data scripts is only run once at the instance first start. This scripts runs with root user.

EC2 user data is used to automate boot tasks such as:
- installing updates
- installing software
- downloading common files from the internet
- anything you can think of

# EC2 instance types

There are several different types of instances that are optimized for different purposes. All instance types sorted by purposes can be watched there (https://aws.amazon.com/ec2/instance-types/). All instance types can be categorized in 5 categories:
- general purposes - great for a diversity of workloads such as web servers or code repositories because of balance between compute, memory and networking
- compute optimized - great for compute intensive tasks that require high performance processors:
  - batch processing workloads
  - media transcoding
  - high performance web servers
  - high performance computing (HPC)
  - scientific modeling & machine learning
  - dedicated gaming servers
- memory optimized - fast performance for workloads that process large data sets in memory. Use cases:
  - high performance, relational/non-relational databases
  - distributed web scale cache stores
  - in-memory databases optimized for BI (business intelligence)
  - applications performing real-time processing of big unstructured data
- accelerate computing - use hardware accelerators and co-processors to perform functions, such as floating point number calculations, graphics processing or data pattern matching more efficiently then with the help of CPU. These are GPU-based instances. It's great for:
  - machine learning training
  - high performance computing in the cloud
- storage optimized - great for storage intensive tasks that require high, sequential read and write access to large data sets on local storage. It's great for^
  - high frequency online transaction processing (OLTP) systems
  - relational and NoSQL dbs
  - cache for in-memory dbs
  - data warehousing apps
  - distributed file system

All instances have there own names and all named with the special name convention. For example: **m5.2xlarge**
- m - the first letter is an instance class
- 5 - number after first letter is a generation
- 2xlarge - words after dot means size within the instance

# Introduction to security groups

Security groups provide security to EC2 instances. They control inbound and outbound internet traffic. Security groups contains only allow rules. It means, that by default no traffic allowed, and you decide what traffic can be allowed.

When you create new group, by default for inbound traffic all denied. And you add some allowing rules. For outbound traffic all allowed. You can switch off this rule and create new rules to allow only that types of traffic that you want. Security groups can be referenced by IP or by other security groups. Security groups are acting as firewall. They regulate:
- access to ports
- IP ranges
- control on inbound network
- control of outbound network

Some tips about security groups:
- can be attached to multiple instances
- multiple groups can be attached to 1 instance 
- they locked down to region / VPC combination (it means that security groups exists only inside region\VPC where they were created, in another region or VPC you need to create them from the very beginning)
- security groups live outside the EC2 - is traffic is blocked the EC2 instance won't see it
- it's good to maintain one separate group for SSH access
- if your app is not accessible (time out), then in most cases it is security group issue
- if your application gives a "connection refused" error, then it's an application error or it's not launched
- all inbound traffic is blocked by default
- all outbound traffic is authorized by default

When you make security group reference to other security groups (or even security group reference to itself) it means that all instances, that belongs to referenced security groups can have configured access to instance belongs to that security group they are referencing.

Classic ports to know:
- 22 SSH and SFTP
- 21 FTP
- 80 HTTP
- 443 HTTPS
- 3389 RDP (Windows instances)

# Service interacting
For different purposes it can be needed to grant permissions to EC2 for interacting with other services. For this we use IAM Roles. We create roles, that we need, and after it we should add this role to that one instance that we need. For this we:
- open EC2 "Instances" dashboard
- checked-in interested us instance
- push "Actions" -> "Security" -> "Modify IAM role"
- add or delete roles to our instance
- click "Apply" btn

# EC2 Instances Purchasing Options

There are different payment options for purchasing EC2 instances:
- On-Demand Instances - short workload, predictable pricing, pay by second
- Reserved (1 & 3 years)
  - Reserved Instances - long workloads
  - Convertible Reserved Instances - long workloads with flexible instances
- Saving Plans (1 & 3 years) - commitment to an amount of usage, long workload
- Spot Instances - short workloads, cheap, can lose instances (less reliable)
- Dedicated Hosts - book an entire physical server, control instance placement
- Dedicated Instances - no other customer will share your hardware
- Capacity Reservations - reserve capacity in a specific **availability zone (AZ)** for any duration

## EC2 On Demand
- Pay for what you use: Linux or Windows billing per second from the first minute, all other OS billing per hour
- Has the highest cost but no upfront payment
- No long-term commitment

Recommended for short-term and un-interrupted workloads, where you can't predict how application will behave.

## EC2 Reserved Instances
- Around 70% discount compared to On-demand
- You reserve a specific instance attributes (Instance Type, Region, Tenancy, OS)
- Reservation period 1 or 3 years, the more you take the bigger discount you get
- Payment options can be "No Upfront", "Partial Upfront", "All Upfront"
- Reserved Instance's Scope - Regional or Zonal (reserve capacity in an AZ)
- You can buy and sel in the Reserved Instance Marketplace
- **Convertible Reserved Instance** - You can change the EC2 instance type, instance family, OS, scope and tenancy, around 65% discount.

Recommended for steady-state usage applications (think database).

## EC2 Saving Plans
- Discount around 70%
- Commit to a certain type of usage ($10/hour for 1 or 3 years) - it means that you pay some money for 1 or 3 years, and for  this mony you can use any instance you want within 1 hour cost. It locked to a specific instance family & AWS region, but flexible across: instance size, OS and tenancy (host, dedicated, default)
- Usage beyond EC2 Saving Plans is billed at the On-Demand price

## EC2 Spot Instances
- Can get a discount of up to 90% compared to On-demand
- Instances that you can "lose" at any point of time if your max price is less than the current spot price
- the MOST cost-efficient instances in AWS

It can be useful for workloads that are resilient to failure, like batch jobs, data analysis, image processing, distributed workloads, workloads with a flexible start and end time. But, **it is not suitable for critical jobs or databases**.

## EC2 Dedicated hosts
- A physical server with Ec2 instance capacity fully dedicated to your use
- Allows you **address compliance requirements** and **use your existing server-bound software licenses (per-socket, per-core, per-VM software licenses)**.
- Purchasing options: On-demand (pay per second for active host) and reserved (1 to 3 years with all kind of payment - No Upfront, Part-Upfront, All Upfront).
- The most expensive option

Useful fpr software that have complicated licensing model or for companies that have strong regulatory or compliance needs.

## EC2 Dedicated Instances
- Instances run on hardware that's dedicated to you
- May share hardware with other instances in the same account
- No control over instance placement (can move hardware after Stop/Start)

## EC2 Capacity Reservations

- Reserve On-Demand instances capacity in a specific AZ for any duration
- You always have access to EC2 capacity when you need it
- No time commitment and no billing discounts
- Combine with Regional Reserved Instances and Savings Plans to benefit from billing discounts
- You are charged at On-Demand rate whether you run instance or not

Suitable for short-term, uninterrupted workloads that needs to be in a specific AZ.