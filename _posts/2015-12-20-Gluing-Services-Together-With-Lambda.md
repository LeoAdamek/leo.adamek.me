---
layout: post
title: "Gluing Services Together With Lambda"
date: 2015-12-20 15:18:00 +0000
tags: lambda aws nodejs system-architecture
comments: false
---

Recently I've been running some experiments with AWS Lambda, a service that provides the
ability to run functions of code in multiple supported runtimes, paying only for the time your function spends running.

Another excellent feature of this service is its event-driven integrations, allowing the function to be called based on
multiple types of event, including several events built into other AWS services, as well as being able to connect these functions
directly to the web with API Gateway.

The AWS service events you can use to trigger a lambda function include:

* Creating / Deleting objects in S3
* Creating / Updating records in DynamoDB
* Pulling events from a Kenisis Stream
* IoT Events
* Cognito Sync Events
* Cloudwatch Log Events
* Scheduled Evens
* SNS Notifications

Using these event types it is possible to use Lambda to implement all kinds of glue-logic to connect services and events together.
This is especially useful when working within a service orienteted architecture.

While the [Documentation][aws-lambda-docs] provide great coverage of how Lambda works, charges your account and integrates with
other AWS services, it only provides a few basic examples, so some experimentation will definately be required.


## Top Tips for Lambda Usage

I've been working with AWS Lambda now for about a week and so far Ive noticed the following patterns emerge as best tips for
creating your functions.


## 1. Always build your own IAM policies for Lambda Functions

Lambda provides several 'one-click' policies to be used for Lambda functions, to grant the function access to other AWS services.
As is the case wich such 'one-click' setups, these policies are far too permissive for general use. A role policy should be created
for your lambda function, providing it only the access that particular function needs to perform its actions.

For example, if you have a function which reads image files from an S3 bucket, extracts the EXIF data and stores it in a DynamoDB table,
then the following rules should be applied to the AWS policy:

* Allow `GetObject` access to the S3 bucket the function will be triggered by.
* Allow `PutItem` access to the DynamoDB table the function will write the EXIF data too.

Thus the policy document for such a role would look like the following:


````javascript

[
  {
    "Statement" : "AllowGetObjectForImageBucket",
    "Effect" : "Allow",
    "Actions" : [
      "s3:GetObject"
    ],
    "Resource" : "arn:aws:s3:::images-bucket/*"
  },
  {
    "Statement" : "AllowPutItemAccessForExifTable",
    "Effect" : "Allow",
    "Actions" : [
    "dyanmodb:PutItem"
    ],
    "Resource" : "arn:aws:dynamodb:REGION:accountID:exifDataTable"
    }
]

````

Note: I've not actually verified the validity of this policy document, I think the ARN values for Dynamodb tables, and action
for `PutItem` may be different to what is shown here, this is an illustrative guide only.

This document now means that the function cannot read objects from other S3 buckets, nor can it write to other DynamoDB tables,
or update/delete existing rows from the table, this means that the function can now only perform the AWS actions that it should require.


This is a best practice for all IAM policies, especially those used with resource roles.


## 2. Decouple your Lambda function handler and logic

In the documentation examples, all the function logic is implemented in the Lambda handler function, this is a design pattern
which I personally disagree with, unless the given function is trivial in nature.

Better is to implement the Lambda handler function separately from the actual logic of what it will do, passing in the Lambda event and context
from the handler function to the function logic. This allows the function to be tested outside the Lambda environment.

Its also a good idea to have the minimum of startup code, as Lambda is billed in intervals of 100ms, so startup code will impact both the time
your function takes, as well as the cost of running it.


[aws-lambda-docs]: http://docs.aws.amazon.com/lambda/latest/dg/welcome.html
