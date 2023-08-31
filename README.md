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
  3. Create several Lambda functions using the boto3 Python code I uploaded above: <br>
  - "start_workflow_function.py" <br>
  - "toll_plaza_application.py" <br>
  4. Upload the "sample_data_toll_application.json" file manually to your landing bucket or run the "toll_plaza_application.py" Lambda function to automatically create and send randomly generated toll data to your landing bucket. <br>
  5. Create a Redshift cluster with the following configurations: <br>
  - Node type: dc2.large <br>
  - Number of nodes: 1 <br>
  6. Create a secret in the AWS Secrets Manager called tollclusterSecret associated with the Redshift cluster. <br>
  7. Create a VPC with the following configurations: <br>
  - IPv4 CIDR block: 10.0.0.0/16. <br>
  - Tenancy: default. <br>
  - No IPv6. <br>
  - 4 subnets and 5 route tables across two availability zones in the same region. <br>
  - DNS options: enable DNS hostnames and enable DNS resolution. <br>
  8. Create a Redshift security group with the following configurations: <br>
  - inbound rule type: Redhshift / source: custom 0.0.0.0/0 / description: Allow access to tcp port 5439. <br>
  - inbound rule type: All traffic / source: custom sg-00d547bf7a5580bab / description: Allow access to self SG. <br>
  - outbound rule type: All traffic / source: custom 0.0.0.0/0 / description: Allow all outbound traffic by default. <br>
  9. Create an IAMGlueServiceRole.
</details>

<details>
  <summary>Configure an Amazon S3 event notification to invoke an AWS Lambda function</summary>
  1. Click on your S3 staging bucket and go to the Properties tab. <br>
  2. Scroll down to Event Notifications and click on "create event notification". <br>
  3. Use the following configurations: <br>
  - Name: s3Events <br>
  - Suffix: .json <br>
  - Under Object Creation, choose Put. <br>
  - Under Destination, choose Lambda function. <br>
  - Select the "start_workflow_function" from the dropdown menu. <br>
  - Save the changes and finish this step.
</details>

<details>
  <summary>Create an AWS Glue crawler to read schema from Amazon Redshift</summary>
  1. Open Redshift and select the cluster you created earlier. <br>
  2. Copy the JDBC URL of your Redshift cluster. <br>
  3. Open the query editor inside your Redshift cluster. <br>
  4. Click "Connect to Database" and use the following configurations: <br>
  - Under Connection, choose "Create new connection" <br>
  - Under Authentication, choose Temporary credentials <br>
  - Under Cluster, select the one you created for this project <br>
  - Under Database name, type "toll_db" <br>
  - Under Database user, select "admin". <br>
  - Click connect and finish this step. <br>
  5. Under Resources on the left, make sure "toll_db" and "public" are selected. <br>
  6. Open the "create_table.txt" file and paste the content of the query to the query editor on the right. <br>
  7. Run the query and review that the table was created by checking it on the left side. <br>
  8. Navigate to the AWS Secrets Manager, click Secrets on the left and then click on the secret you created earlier. <br>
  9. Under Secret Value, click on "Retrieve secret value". <br>
  10. Copy the password and keep it available in a text editor. <br>
  11. Navigate to the AWS VPC console, click on the VPC you created earlier and then copy the ID one of the subnets that starts with database. <br>
  12. Navigate to AWS Glue and under Data Connections, create a connection with the following configurations: <br>
  - Name: redshift_conn. <br>
  - Connection type: JDBC. <br>
  - JDBC URL: the JDBC URL you copied from the Redshift cluster page. <br>
  - Credential type: Username and password. <br>
  - Username: admin. <br>
  - Password: the password you copied earlier. <br>
  - Expand Network Options, under VPC: the VPC you created earlier. <br>
  - Subnets: choose the subnet ID you copied earlier. <br>
  - Create the create connection. <br>
  13. In AWS Glue, under the Data Catalog section, select Crawlers. <br>
  14. Click Create Crawler and input the following configurations: <br>
  - Name: Redshift-Crawler. <br>
  - Data Source Configuration: Not yet. <br>
  - Click Add a data source and input the following configurations: <br>
  - Data Source: JDBC. <br>
  - Connection: redshift_conn. <br>
  - Include path: toll_db/public/%. <br>
  - Click add a JDBC source. <br>
  - Click Next. <br>
  - Choose the IAMGlueServiceRole. <br>
  - Click Next. <br>
  - Under Output Configuration, choose toll-raw-db as the target database. <br>
  - Under Crawler schedule, within Frequency choose On-demand. <br>
  - Click Next and then Create Crawler. <br>
  15. Refresh the crawler page and then run the crawler. <br>
  16. Once the status of the crawler run displays "completed", click on Data Catalog / Databases / Tables. <br>
  17. Review the two available tables and their respective classifications to finish this step.
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
