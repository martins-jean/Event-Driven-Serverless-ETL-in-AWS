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
6. In AWS Glue, you can use workflows to create and visualize complex ETL activities that involve multiple crawlers, jobs, and triggers.
7. Each workflow manages the run and monitoring of all of its jobs and crawlers. As a workflow runs each component, it records progress and status.
8. AWS Glue jobs issue COPY statements against Amazon Redshift to achieve maximum throughput. These commands require that the Amazon Redshift cluster access Amazon S3 as a staging directory.
9. You can connect to the Amazon Redshift database using the built-in query editor, SQL client tool or built-in Data API.

## Step-by-step Guidelines
<details>
  <summary>Required setup</summary>
  1. Download the query included in the "create_table.txt" file. <br>
  2. In S3, create a landing bucket and a staging bucket. <br>
  3. Upload the "sample_data_toll_application.json" file to your landing bucket. <br>
</details>

<details>
  <summary>Configure an Amazon S3 event notification to invoke an AWS Lambda function</summary>
  Click on your S3 staging bucket
</details>

<details>
  <summary>Create an AWS Glue crawler to read schema from Amazon Redshift</summary>
</details>

<details>
  <summary>Create an AWS Glue ETL job to move data from an S3 bucket to Amazon Redshift</summary>
</details>

<details>
  <summary>Create AWS Glue workflows to crawl raw data from the S3 bucket, and then use an AWS Glue ETL job</summary>
</details>

<details>
  <summary>Query the data using the Amazon Redshift query editor</summary>
</details>
