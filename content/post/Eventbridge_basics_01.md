---
title: "Eventbridge_basics_01"
date: 2023-09-07T11:05:06+05:30
draft: false
---

# AWS EventBridge Basics (01)

AWS EventBridge is a tool for handling events in one place. This is the first post in a series where I will explore about AWS EventBridge by replicating [this setup](https://aws.amazon.com/blogs/industries/gxp-continuous-compliance-on-aws/)

## Understanding EventBridge

In this post, we'll explore what EventBridge is and how it works:

- EventBridge is an AWS service that can receive events and send them to targets based on rules.
- It can handle multiple events as input, and if an event matches a rule, it can be sent to multiple targets.

## Event Sources

Events can come from various sources:

- AWS services
- Third-party applications
- SaaS platforms

These events are received by an 'event bus' Each event can be associated with only one event bus.

## Event Bus

An event bus is a central hub for receiving events. Each event bus can have multiple event rules.

## Event Rules

Event rules define the conditions for forwarding events to targets. When an event matches a rule on the event bus, it is sent to all the targets associated with that rule.

## Versatile Targets

Targets can be:

- AWS services
- Third-party applications
- Another event bus in a different AWS account
    - This setup allows events to be routed between AWS accounts, which is useful for centralizing data.

## Let's Try It Out

Let's do a simple demonstration: when someone creates an S3 bucket, we want EventBridge to trigger a Lambda function in the same account. To set this up, follow these steps:

create below resources:
1. **Target**: AWS Lambda
2. **IAM Role for Lambda**: Ensure it has the necessary policies for Lambda and CloudWatch access.
3. **EventBridge Rule**: Make sure it listens for S3 bucket creation in CloudTrail and has permission to invoke the Lambda.

Once this setup is complete, the Lambda function will be triggered every time an S3 bucket is created.

Try it manually. I have written terraform for this. Here is the [code](https://github.com/AWS-Hands-on/eventbridge/tree/01) for reference.

Happy coding!
