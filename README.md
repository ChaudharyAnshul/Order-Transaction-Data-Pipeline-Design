# Order-Transaction-Data-Pipeline-Design

## Project Overview

This project aims to design a data pipeline to ingest, process, store, and analyze real-time order and return data streams from multiple sources. The goal is to produce actionable insights and establish key performance indicators (KPIs) to optimize supply chain operations.

## Problem Statement

Given AWS and access to all open source tooling, design a system for streaming order/transaction data in real-time from 30+ integrations. Each order/transaction includes Customer Name, Time, Amount, GPS location, and product. The system should process orders, define a database schema, implement potential triggers for notifications, and incorporate a large language model (LLM) for sentiment analysis on transaction comments to determine if they are Good/Bad. The goal is to produce actionable insights from the order data, such as cash flow forecasts. Additionally, establish key performance indicators (KPIs) to measure the system's effectiveness.

## Solution Design

### 1. Data Streaming

![Data Streaming](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/Data_pipeline_Design.drawio.png)

This diagram illustrates a data streaming pipeline using AWS services. Let's break down the components and the flow step by step:

1. **Data Source**:
   - Represents the input sources for the data. Multiple sources are funneling data into the pipeline.

2. **EC2 Producer**:
   - An Amazon EC2 instance is used to produce and send data to the AWS Kinesis Data Stream. This instance acts as the producer in the data pipeline.

3. **AWS Kinesis Data Stream**:
   - A managed service that scales elastically for real-time processing of streaming data. The data stream is divided into multiple shards, allowing parallel processing.

4. **AWS Lambda Consumer**:
   - AWS Lambda functions are used to consume data from the Kinesis Data Stream. Each shard has a dedicated Lambda function to process the data concurrently.

5. **Trigger Task Based on Data**:
   - Depending on the type of data or event, different tasks are triggered by the Lambda functions. There are three types of tasks illustrated:
     - **New Order** (6)
     - **Return Order** (7)
     - **Update Order** (8)

6. **DynamoDB Temporary Storage**:
   - Processed raw data is stored temporarily in an Amazon DynamoDB table. This NoSQL database service provides low-latency data access.

7. **EventBridge Scheduler**:
   - Amazon EventBridge is used to schedule the Lambda function that checks for failures. 

8. **AWS SNS (Simple Notification Service)**:
   - If a failure is detected, a notification message is pushed to the AWS SNS topic.

9. **Email Authorities**:
    - The SNS topic is configured to send an email to the authorities, alerting them about the failure in the pipeline.

### 2. Order Processing

![New Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/new_order.drawio.png)

![Return Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/return_order.drawio.png)

![Update Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/update_order.drawio.png)
