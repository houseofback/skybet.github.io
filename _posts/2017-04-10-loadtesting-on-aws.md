---
layout:     post
title:      Load testing on the Cloud
date:       2017-04-10 10:00
summary:    Replacing our on-premise environment with the AWS Cloud
author:     andrew_backhouse
image:      jan-tech-talks.jpeg
category:   Cloud
tags:       AWS, Cloud, Loadtesting
---

Here at Sky Betting and Gaming we're making changes to the way we host our services and applications. One such service I helped look at is our load testing environment which is used to ensure our applications and services can handle peaks of customer traffic and scale accordingly.

Our existing load test environment was hosted on-premise with fixed resources due to the upfront cost and lead time to add new hardware. This also included manual logging into individual servers to patch, hardcoding IP addresses in scripts and generally treating them like *pets*. :dog:

I wanted to approach this with a fresh design so that we can take full advantages of the AWS cloud services. Specifically dynamically provisioned servers and start treating the servers and other components as temporary resources, like *cattle*. :cow:

These are our goals;
- IT assets become programmable resources
- Unlimited capacity
- Security built in
- Improved automation tooling
- Optimised for cost
- Ability to test more accurate customer traffic patterns

## Here's the design

To start, we invested time in getting our Terraform configuration setup as this helped integrate with Platform Services who help provide shared services for commonly used integrations such as log-shipping, metric analysis, LDAP authentication and DNS discovery ([AWS and Consul post](/posts/2016-05-05-aws-and-consul.md)). This enabled the team to get started quickly knowing the essential services were in place.

Starting with the AWS Virtual Private Cloud (AWS VPC), we needed to protect our servers in private subnets meanwhile enabling internet connectivity for testing. Using AWS NAT Gateways meet this requirement and had the added benefit of only using a few public IPs. This meant our load injectors avoided looking like a DDoS attack on ourselves! AWS NAT Gateways are managed by AWS so we didn't need to worry about scalability, only availability, resolved by spreading load across each Availability Zone (AZ).

With the AWS VPC setup now for the fun bit, provisioning servers! Using auto-scaling groups we can dynamically provision an unlimited number of servers (within AWS service limits) at the touch of a button or API call. Also the beauty of auto-scaling groups is if a server becomes unhealthy it will provision a replacement. The load injector servers run JMeter which needs a shared file system to reference scripts and data attributes. AWS Elastic File Store (AWS EFS) fitted the requirement and we automated the mounting of it when provisioning a server.

We now had an environment that can be scaled up multiple times, all using the latest server operating system, JMeter application setup and ready to generate load. Using Terraform meant we can easily tear down the environment and bring our costs down to near 0 when not in use. This is important because the load testing environment is only used for short periods of very high intensity work. It poses as an ideal workload for the cloud.

## In summary

Looking back at our goals, I can say;

- Using Terraform infrastructure-as-code we improved automation of the service to a few clicks and resulted in IT assets that were programmable resources.
- As we encountered, AWS Service Limits are there to help prevent you from accidently provisioning too many resources.
- Thanks to the temporary nature of the resources we no longer have servers lasting for more than a few hours, meaning security patching is no longer a concern.
- We're now treating our servers like cattle with tooling enhancements that remove hardcoded IPs and scripts that handle failure.
- There is always more ways to reduce our costs on AWS. We already clean down the entire infrastructure when not in use, however we can make further improvements by using more cost-optimised resources.
- Lastly, our ability to test loads that are accurate to what a customer experiences has been made possible by introducing increased latency between API calls because we can leverage AWS's global infrastructure and deploy into a different region.

This is just one example of our transition into cloud infrastructure. As a fast paced and innovative company Sky Betting and Gaming is looking to use AWS more aggressively. If you like the sound of this, we're hiring!

Thanks for reading.

-- Andrew Backhouse, Solution Architect
