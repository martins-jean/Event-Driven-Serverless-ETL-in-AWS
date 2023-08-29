# Event-Driven Serverless ETL in AWS
Used AWS services to automate the consolidation of toll plaza transactions in a data warehouse.

## Architecture Diagram

![Screenshot 2023-08-29 at 17 29 20](https://github.com/martins-jean/Event-Driven-Serverless-ETL-in-AWS/assets/118685801/729e6e8f-2662-4a56-82da-a90a5b956eb4)

## Project Overview
1. This solution uses AWS Glue workflows to manage the ingestion of data in a data warehouse. The Amazon S3 Event Notifications feature is used to invoke the AWS Glue workflow as the data arrives in the Amazon S3 landing zone bucket.
2. Raw data is collected in the Amazon S3 landing zone bucket. The data can be unstructured or structured.
3. An AWS Glue crawler can crawl multiple data stores to populate the AWS Glue Data Catalog with tables. ETL jobs, which you define in AWS Glue, use these Data Catalog tables as sources and targets.
4. An AWS Glue job encapsulates a script that connects to your source data, processes it, and then writes it out to your data target.
5. You can use the Amazon S3 Event Notifications feature to receive notifications when a PUT event happens in the S3 bucket. The feature then sends event notification messages to the AWS Lambda function.
6. In AWS Glue, you can use the 
