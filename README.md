# Order-Transaction-Data-Pipeline-Design

## Project Overview

This project aims to design a data pipeline to ingest, process, store, and analyze real-time order and return data streams from multiple sources. The goal is to produce actionable insights and establish key performance indicators (KPIs) to optimize supply chain operations.

## Problem Statement

Given AWS and access to all open source tooling, design a system for streaming order/transaction data in real-time from 30+ integrations. Each order/transaction includes Customer Name, Time, Amount, GPS location, and product. The system should process orders, define a database schema, implement potential triggers for notifications, and incorporate a large language model (LLM) for sentiment analysis on transaction comments to determine if they are Good/Bad. The goal is to produce actionable insights from the order data, such as cash flow forecasts. Additionally, establish key performance indicators (KPIs) to measure the system's effectiveness.

## Solution Design

### 1. Data Streaming

![Data Streaming](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/Data_pipeline_Design.drawio.png)


### 2. Order Processing

![New Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/new_order.drawio.png)

![Return Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/return_order.drawio.png)

![Update Orders](https://github.com/ChaudharyAnshul/Order-Transaction-Data-Pipeline-Design/blob/main/design%20image/update_order.drawio.png)
