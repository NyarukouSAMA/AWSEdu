With EC2 we have two options for making storage: **Elastic Block Store (EBS)** or **Elastic file system (EFS)**.
Except it we can use **EC2 Instance Store**, but it for very special cases.
Another one thing you should know is **Amazon Machine Image (AMI)** - it some sort of template mechanism for EC2 instances

# EBS Overview

- An EBS Volume is a network drive you can attach to your instances while they run. Because of they are network-communicated, there can be some latency.
- It allows your instances to persist data, even after termination.
- EBS Volume can be mounted to one instance at a time. But to one EC2 instance can be mounted several Volumes at the same time.
- They bound to a specific availability zone. It means that Volume from one AZ can not be attached to instance from another AZ. But we can move across using snapshot.
- Have a provisioned capacity (size in GBs and IOPS) - you get billed for all the provisioned capacity. You can increase the capacity of the drive over the time.

For EBS volume there is special attribute exists, which called "Delete on termination". It controls whether Volume be deleted or not when EC2 host, that Volume attached to, terminated. This can be controlled by the AWS console or AWS CLI.

## EBS snapshots

You can make backup (called snapshot) of your EBS volume. It can be helpful for some archiving purposes or for moving some batch of data between AZ or regions. It's even not necessary to detach volume for doing snapshot (but recommended).

Within snapshot function we have some features:
- EBS Snapshot Archive - it moves a snapshot to an "archive tier" that is 75% cheaper, but it takes from 24 to 72 hours for restoring the archive
- Recycle Bin for EBS Snapshots - setup rules to retain deleted snapshots so you can recover them after an accidental deletion (you can specify retention from 1 day to 1 year)

# AMI Overview

AMI are a customization of an EC2 instance. It means that you add your own software, configuration, OS, monitoring... It's an ability for making template of some EC2 pre-configured instance, what means faster boot and configuration time because all your software is prepackage and all your configuration can be already done.

It's built for a specific region, but can be copied across.

You can launch EC2 instances from:
- public AMI (AWS provided)
- your own AMI (you make and maintain them yourself)
- AWS Marketplace AMI (AMI someone else made)

AMI Process usually looks like this:
- Start EC2 instance and customize it
- Stop instance
- Build an AMI (this will also create EBS snapshots)
- Launch instance from AMI you made

# EC2 Instance Store

Sometimes we could have a need for much better disk performance (higher IOPS). For that point in AWS there exists EC2 Instance Store - some sort of in-memory hard-drive attached to instance (not network drives). It have much better I/O performance. But, EC2 Instance Store lose their storage if they're stopped (ephemeral).

So it can be good for buffer or cache or scratch data or temporary content. So we have risk of data loss if hardware fails, so backup and replication are totally your responsibility.

# EBS Volume Types
There are 6 types:
- gp2/gp3(SSD) - general purpose SSD volume that balances price and performance for a wide variate of workloads
- io1/io2 (SSD) - highest performance SSD volume for mission-critical low-latency or high-throughput workloads
- st1 (HDD) - low cost HDD volume designed for frequently accessed, throughput-intensive workloads
- sc1 (HDD) - lowest cost HDD volume designed for less frequently accessed workloads

EBS volume are characterized in size, throughput and IOPS. If you in doubt always consult AWS docs.

Only gp2/gp3 or io1/io2 can be used as boot volumes.

## General Purposed SSD gp2/gp3
- They are cost effective storage with low-latency. Usually used as system boot storage, virtual desktops, dev- and test-environment
- It could be from 1 GB to 16 TB
- gp3: newer generation of gp SSD, have baseline of 3000 IOPS and throughput of 125 MiB/s and can be increased to 16000 IOPS and throughput up to 1000 MiB/s independently.
- gp2: small volumes can burst IOPS to 3000; it is because size and IOPS are linked, the bigger size the more IOPS you can get. The limits are 3 IOPS per GB and 16000 IOP max. For max IOP storage should be at least 5334GB

## HDD st1 or sc1
- It cannot be a bott volume
- it has size from 125 MiB to 16 TiB
- st1: used for Big Data or for DWH or for Log Processing; and max throughput 500MiB/s and max IOPS 500
- sc1: they are lowest cost and usually used for archiving or other infrequently used data. Max throughput there is 250 MiB/s and max IOPS is 250

## Provisioned IOPS (PIOPS) SSD io1/io2
- PIOPS SSD used for critical bushiness apps with sustained IOPS performance or for apps, that needs more than 16000 IOPS. It greats for RTS's DB workloads (sensitive to storage perf and consistency)
- io1/io2 (4 DB - 16TB): max PIOPS 64000 for nitro EC2 instances and 32000 for other, and you can increase PIOPS independently from storage size. io2 have more durability and more IOPS per GiB
- io2 Block Express (4Gib - 64 TiB): sub-millisecond latency and max PIOPS up to 256000 with an IOPS:DiB ration of 1000:1

And it supports **EBS multi-attach** - you can attach one volume to several instances, each instance have full read/write access which mean that application must be concurrent safe. But we can achieve much higher application availability and better application productivity (e.g. Teradata).

Multi-attach can be reached only with io1/io2 family and can be made within 1 AZ. Such volume also should use cluster-aware file system (not XFS, EX4, etc...)

# Amazon EFS - Elastic File System
If we need multi-zonal availability for some storage around several EC2 instances then we should use EFS despite of EBS. It 3 times more expensive then usual EBS and you should pay per use, but it highly available and scalable. We can use it for content-management, web-serving, data-sharing, Wordpress...
- It uses NFSv4.1 protocol.
- We use Security groups to control access to EFS.
- It compatible with ONLY Linux based AMI (not Windows).
- Encryption at rest using KMS.
- It's POSIX (Linux) file system type with standard APIs
- It scales automatically, no capacity planning, but pay per use.

## EFS Scales
- 1000s of concurrent NFS clients, 10 GB+/s throughput (It's VERY SCALABLE)
- It can grow to Petabyte-scale network file system, automatically (INCONCEIVABLE SCALABLE)

## EFS Options
- Performance mode (set at EFS creation time): general purpose (default) - used for latency-sensitive use cases (web servers, CMS, etc...); max I/O - higher latency, throughput, highly parallel (big data, media processing)
- Throughput mode: Bursting (1TB = 50 MiB/s + burst of up to 100 MiB/s) - that means it will scale as much as need; and Provisioned - there you set throughput regardless of storage size, for example 1 GiB/s for 1 TB storage.
- Storage Tiers (lifecycle management feature): standard and infrequently access (EFS-IA); You enable EFS-IA with lifecycle policy, which mean that after some time, that you set-up, files will be moved to lower price storage with higher cost to retrieve. EFS-IA is cheaper and you can save some money in some cases.
- Availability and durability: standard (multi-AZ) and one zone - one zone cheaper (over 90% in cost saving), it greats for development, compatible with EFS-IA for one zone