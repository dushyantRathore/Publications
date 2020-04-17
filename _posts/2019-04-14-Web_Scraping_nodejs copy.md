---
layout:     post
title:      Building Serverless Applications using AWS Chalice
date:       2020-04-16 13:00:00
summary:    Building a Serverless Application using AWS Chalice
categories: 
---

## What exactly is a serverless application?
In simple terms, a Serverless application is one which is hosted by a third-party service, eliminating the need for server software and hardware management by the developer. Applications are broken up into individual functions that can be invoked and scaled individually. A serverless application runs in stateless compute containers which are fully manager by the cloud provider.

## What is AWS Lambda?
It is an event-driven, serverless computing platform provided as a part of the Amazon Web Services. It is a computing service that runs code in response to events and automatically manages the computing resources required by that code. AWS lambda functions serve a wide range of applications and can be integrated with other Amazon Web Services like S3, SQS, RDS and SNS etc.

## AWS Chalice
AWS Chalice is a microframework for writing serverless apps in python. It allows the creation and deployment of applications that use AWS Lambda. It provides:
* A **command line tool** for creating, deploying, and managing your app.
* A decorator based **API** for integrating with Amazon API Gateway, Amazon S3, Amazon SNS, Amazon SQS, and other AWS services.
* Automatic **IAM** (Identity Access Management) policy generation.

**The post consists of the following steps:-**
1. Setting up an account on AWS.
2. Creating an S3 bucket.
3. Coding the Chalice application and deployment.
4. Testing out the Lambda function.

Now, we'll deep dive into the steps:-

## Step 1 - Setting up an account on AWS
Sign up on [Amazon Web Services](https://aws.amazon.com/free/).

Note: AWS requires a credit card for registration, but our example will exist entirely on AWS free tier, so you won't be charged.

## Step 2 - Creating an S3 bucket
This step consists of the following steps:
* Select the **S3 option** under the **Storage** section.
![1.png](https://ucarecdn.com/d7dc3dc2-c24e-489d-aff7-59e0b7430e1b/)
* Select the **Create bucket option**.
![2.png](https://ucarecdn.com/ecba9a86-0984-494f-84a3-2abac0abc16b/)
* Give a **Bucket name** in the **General configuration** section and click on **Create Bucket** option at the bottom.
![3.png](https://ucarecdn.com/8dcca243-a37f-4596-8ada-bbab879787f9/)

## Step 3 - Coding the Chalice Application and Deployment
Once we are done with **Steps 1 and 2**, we need to perform the following sub steps as a part of **Step 3**:-
* First, we need to install Chalice and boto3 using the following commands in pip.
```pip install chalice```
```pip install boto3```
* Once Chalice and boto3 are installed, create a new Chalice project and access it using the following commands.
```chalice new-project sampleproject && cd sampleproject```
Here, we are using `sampleproject` as our project name.
* Once we are in the `sampleproject` directory, write the following code in `app.py` file.

```
# Import the neccessary Python Packages
from chalice import Chalice
import boto3

app = Chalice(app_name="<CHALICE_APPLICATION_NAME>") # Lambda Function Name
app.debug = True    #Set the debug mode

s3 = boto3.client("s3") # Initialise the S3 client

# Create the invocation for the Lambda function on S3 object creation event
@app.on_s3_event(bucket="<S3_BUCKET_NAME>", events=["s3:ObjectCreated:*"])

def handler(event):
    print("Object uploaded for bucket: %s, key: %s"
          % (event.bucket, event.key))
    print("Lamba triggered due to S3 event")
    app.log.debug("Lambda function triggered due to S3 event")

```

* Once done, deploy the chalice application from the directory using the following command.
```chalice deploy```

You should get an output similar to the following which shows that the application has been deployed successfully and the corresponding lambda function has been created.
![4.png](https://ucarecdn.com/9d6a1f28-944a-4872-9d3c-4ebd57b4fed0/)

## Step 4 - Testing out the Lambda Function
* Once the Chalice application has been successfully deployed, upload an object to the S3 bucket from the AWS console using the **upload** option. Select the file to upload and click on the **Next** option in the subsequent **widgets** generated.
![5.png](https://ucarecdn.com/d42314c1-4151-4a43-a0fc-e00d017e010c/)
* Once the object has been uploaded, select the **Lambda option** under the **Compute section** in the AWS Management Console.
![6.png](https://ucarecdn.com/480eac6e-1aa9-4148-a84c-69b171c4c80d/)
* Select the **Lambda function** deployed using Chalice and move into the **monitoring section** as shown below.
![7.png](https://ucarecdn.com/e9c20754-e9d1-46a1-bf6c-1f3bfdfcb4d5/)
* Once you scroll down, you will see a **CloudWatch log** for the **Lambda Invocation** similar to the one shown below indicating that our serverless function was successfully triggered on the S3 object creation.
![8.png](https://ucarecdn.com/7