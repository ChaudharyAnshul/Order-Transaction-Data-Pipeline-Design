# Order-Transaction-Data-Pipeline-Design

## Project Overview

This project aims to design a data pipeline to ingest, process, store, and analyze real-time order and return data streams from multiple sources. The goal is to produce actionable insights and establish key performance indicators (KPIs) to optimize supply chain operations.

## Problem Statement

Given AWS and access to all open source tooling, design a system for streaming order/transaction data in real-time from 30+ integrations. Each order/transaction includes Customer Name, Time, Amount, GPS location, and product. The system should process orders, define a database schema, implement potential triggers for notifications, and incorporate a large language model (LLM) for sentiment analysis on transaction comments to determine if they are Good/Bad. The goal is to produce actionable insights from the order data, such as cash flow forecasts. Additionally, establish key performance indicators (KPIs) to measure the system's effectiveness.

## Solution Design

### 1. Data Streaming

![Data Streaming](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/Data_pipeline_Design.drawio.png)

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

1. **New Order (6)**:
   - The process starts when a new order is received.

2. **AWS Managed Workflow (Airflow)**:
   - The workflow is managed by AWS Managed Workflows for Apache Airflow, which orchestrates the sequence of tasks.

3. **Lambda Functions**:
   - **Construct Order Object**: A Lambda function constructs the order object and updates the temporary instance.
   - **Check Payment Status**: Lambda function checks the payment status of the order. 
     - If the payment fails, it updates the status in DynamoDB and sends a failure notification to the customer via Amazon SES.
   - **Confirm Order**: If the payment is confirmed, this Lambda function confirms the order and sends a confirmation notification to the customer via Amazon SES.
   - **Load Order Data**: The order data is loaded into AWS Redshift for further analysis and reporting and Temporary instance is deleted.

4. **Amazon SES**:
   - Used to send confirmation or failure emails to the customer based on the order status.

5. **AWS SNS**:
   - Sends notifications to the packaging team once the order is confirmed.

6. **DynamoDB Temporary Storage**:
   - Stores temporary data and updates related to the order processing.

7. **EventBridge Scheduler**:
   - Periodically triggers a Lambda function to reprocess orders if needed.

8. **AWS Redshift**:
   - Stores the finalized order data for analysis and reporting.

![Return Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/return_order.drawio.png)

1. **Return Order (7)**:
   - The process starts when a return order is received.

2. **AWS Managed Workflow (Airflow)**:
   - The workflow is managed by AWS Managed Workflows for Apache Airflow, which orchestrates the sequence of tasks.

3. **Lambda Functions**:
   - **Construct Return Object**: A Lambda function constructs the return order object.
   - **Process Return**: Lambda function processes the return by calling a fine tuned LLM on Sagemaker.
   - **Load Return Data**: The return data is loaded into AWS Redshift for further analysis and reporting.

4. **Custom LLM Model**:
   - A custom large language model (LLM) is used to approve returns. The return status is updated based on the approval.

5. **Amazon SES**:
   - Sends the return status email to the customer.

6. **DynamoDB Temporary Storage**:
   - Stores temporary data and updates related to the return order processing.
   - The data is updated by various Lambda functions during the process.

7. **AWS Redshift**:
   - Stores the finalized return data for analysis and reporting.

![Update Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/update_order.drawio.png)

1. **Update Order (8)**:
   - The process starts when a Update order is received.

2. **AWS Managed Workflow (Airflow)**:
   - The workflow is managed by AWS Managed Workflows for Apache Airflow, which orchestrates the sequence of tasks.

3. **Lambda Functions**:
   - **Update**: A Lambda function to Update the order. Sends notification to users/shipping team based on the updated status.

4. **Amazon SES**:
   - Sends the status email to the customer.

5. **AWS SNS**:
   - Sends notifications to the Shipping team.
  
### 3. Schema

![Data Streaming](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/schema.drawio.jpg)

### 4. Actionable Insights

1. **Cash Flow Forecasts**:
   - Analyze transaction data to identify patterns in revenue and expenditures.
   - Use time-series forecasting models to predict future cash flows.

2. **Product Performance Analysis**:
   - Track the sales performance of each product.
   - Identify best-selling products and those with high profit rates.

3. **Customer Behavior Trends**:
   - Analyze purchasing patterns and customer demographics.
   - Use clustering algorithms to segment customers based on buying behavior.

4. **Return Reasons and Sentiment Analysis**:
   - Perform sentiment analysis on return comments to understand customer satisfaction.
   - Identify common issues leading to returns. Use this data to improve product quality or customer service processes.

5. **Geographical Sales Trends**:
   - Analyze GPS data to determine high-performing regions.
   - Identify geographical areas with high sales or high return rates.
  
### 5. Key Performance Indicators

1. **Total Revenue**: Measure the total revenue generated from all sales.
2. **Return Rate**: Measure the percentage of orders that are returned to identify potential product or service issues.
3. **Inventory Turnover Rate**: Calculate how often inventory is sold and replaced over a specific period to optimize stock levels
4. **Order Accuracy Rate**: Measure the percentage of orders that are fulfilled correctly to ensure quality control.
5. **Customer Satisfaction Score**: Assess customer satisfaction through surveys or feedback to understand overall customer experience.
