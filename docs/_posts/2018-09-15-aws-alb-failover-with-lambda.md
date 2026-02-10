---
layout: post
title: "AWS ALB Failover with Lambda"
date: 2018-09-15 12:00:00 -0600
---

## Abstract

The article explains how to automatically switch to an alternative target group for an AWS Application Load Balancer (ALB) when the primary target group becomes unhealthy using Lambda functions.

## Requirements

To implement ALB failover with Lambda, you need:

1. Primary Target Group
2. Alternative Target Group

## Setup Components

**SNS Topic:** Create an SNS topic that will trigger the Lambda function when alarms are activated.

**CloudWatch Alarm:** Configure an alarm monitoring target group health, set to trigger when fewer than 1 healthy hosts exist. The alarm should send notifications to your SNS topic.

**Lambda Function IAM Permissions:** The function requires elasticloadbalancing permissions. The required IAM policy grants full ELB access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "VisualEditor0",
      "Effect": "Allow",
      "Action": "elasticloadbalancing:*",
      "Resource": "*"
    }
  ]
}
```

## Lambda Function Code

The provided Python template demonstrates the failover mechanism:

```python
from __future__ import print_function

import boto3
print('Loading function')
client = boto3.client('elbv2')

def lambda_handler(event, context):
    try:
        response_80 = client.modify_listener(
            # HTTP (port 80) listener
            ListenerArn = 'arn:aws:elasticloadbalancing:region:id:listener/app/alb/id/id',
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': 'arn:aws:elasticloadbalancing:region:id:targetgroup/id/id'
                },
            ]
        )
        response_443 = client.modify_listener(
            # HTTPS (port 443) listener
            ListenerArn='arn:aws:elasticloadbalancing:region:id:listener/app/alb/id/id',
            DefaultActions=[
                {
                    'Type': 'forward',
                    'TargetGroupArn': 'arn:aws:elasticloadbalancing:region:id:targetgroup/id/id'
                },
            ]
        )
        print(response_443)
        print(response_80)
    except Exception as error:
        print(error)
```

## Result

When the primary target group becomes unhealthy, the CloudWatch alarm triggers an SNS notification, which activates the Lambda function to redirect traffic to the backup target group.
