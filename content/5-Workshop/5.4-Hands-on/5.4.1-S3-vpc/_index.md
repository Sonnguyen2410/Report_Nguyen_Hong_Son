---
title : "Access S3 from VPC"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 5.4.1. </b> "
---

#### Using Gateway endpoint

In this section, you will create **a Gateway eendpoint** to access **Amazon S3** from **an EC2 instance**. **The Gateway endpoint** will allow upload an object to S3 buckets without using **the Public Internet**. To create an endpoint, you must specify the VPC in which you want to create the endpoint, and the service (in this case, S3) to which you want to establish the connection.


#### Content

- [Create gateway endpoint](3.1-create-gwe/)
- [Test gateway endpoint](3.2-test-gwe/)