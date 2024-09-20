# Sales Data Analysis CDC Capture
## An AWS data pipeline Optimizing Sales Data Processing: Implementing Change Data Capture for Real-Time Analysis

# Prerequisites
* A good understanding of AWS Services: DynamoDB, S3, Lambda, Amazon Kinesis or Amazon MSK (Managed Streaming for Apache Kafka), Amazon Redshift, Amazon Athena, Glue, VPC, RDS
* Good understanding of Python, and SQL.
* Experience with CI/CD pipelines.
* Knowledge of AWS security best practices, including IAM (Identity and Access Management) roles and policies.

# Project Motivation
The main motive of this project is to build and end to end real time CDC capture pipeline. The main objectives that I aim to achieve are:
* **Real-Time Data Availability:** By implementing CDC, we can stream changes in the sales database instantaneously. This ensures that the data analytics platforms have access to the most current data, enabling timely insights and actions.
* **Reduced Load on Production Systems**: CDC minimizes the need for bulk data loads, which can be disruptive and resource-intensive. This approach allows for a more efficient use of resources, reducing operational costs and system downtime.
* **To Allow quick decision making based on the real time data that is available.**

# Architecture Diagram
![Architecture Diagram](./Architecture_Diagram/CDC_Pipeline_Sales_Data_Kinesis_Dynamodb.png?raw=true)

# Architecture Diagram Steps
1. AWS Lambda Mock Sales Data Generator initiates the process by generating mock sales data.
2. The mock sales data is stored in Amazon DynamoDB Store Sales Data.
3. DynamoDB Stream will Stream the Changes made to Sales Data.
4. EventBridge Pipe captures the stream of changes from DynamoDB.
5. Kinesis Data Streams will Capture the stream of changes from EventBridge.
6. Amazon Kinesis Data Firehose batches the stream of data from kinesis data streams.
7. AWS Lambda performs necessary transformations on the batch of data.
8. AWS Lambda passes the transformed data back to Amazon Kinesis Data Firehose.
9. S3 bucket stores the streaming data from Kinesis Data Firehose.
10. AWS Glue Crawler creates a schema for the sales data files stored in the S3 bucket.
11. Glue Data Catalog stores the schema created by AWS Glue Crawler.
12. Amazon Athena allows ad-hoc querying of the data stored in the Data Catalog.
13. Amazon QuickSight provides data visualization and insights from the Data Catalog.

## Steps and Descriptions to build the pipeline
1. **Creating DynamoDB Table**
   - Initial setup of the DynamoDB table to store data.
   ![DynamoDB Table Creation](./Images_Watermarked/1_Creating_DynamoDB_Table.png?raw=true)

2. **Orders Table Created**
   - Orders Table in DynamoDB.
   ![Orders Table Creation](./Images_Watermarked/2_enabling_dynamodb_streams.png?raw=true)

3. **Enabling DynamoDB Streams**
   - Activated streams in DynamoDB for change data capture.
   ![Enabling Streams](./Images_Watermarked/3_enabling_dynamodb_streams.png?raw=true)

4. **Creating Kinesis Data Stream**
   - Setup of Kinesis stream to capture DynamoDB changes.
   ![Kinesis Data Stream Creation](./Images_Watermarked/4_creating_kinesis_data_stream.png?raw=true)

5. **Kinesis Data Streams Created**
   - Confirmation that Kinesis data streams are successfully created.
   ![Kinesis Streams Confirmation](./Images_Watermarked/5_kinesis_data_streams_created.png?raw=true)

6. **EventBridge Pipe Source Creation**
   - Setting up an EventBridge pipe source for event handling from DynamoDB streams.
   ![EventBridge Pipe Source](./Images_Watermarked/6_creating_eventbridge_pipe_source.png?raw=true)

7. **Create EventBridge pipe Target**
   - Setting up target correctly.
   ![EventBridge Pipe Target](./Images_Watermarked/7_create_pipe_target.png?raw=true)

8. **EventBridge Pipe Created**
   - Confirmation that EventBridge Pipe is successfully created.
   ![EventBridge Pipe Confirmation](./Images_Watermarked/8_Eventbridgepipe_created.png?raw=true)

9. **Python Script for Mock Data Generation**
   - Script that generates the mock data and pushes it into DynamoDB.
   ![Mock Data Generation Script](./Images_Watermarked/9_Script_for_mock_data_generation_and_publish_to_dynamodb.png?raw=true)

10. **Terminal Showing that data is inserted into the db**
    - Terminal display showing data insertion.
    ![Data Insertion Terminal](./Images_Watermarked/10_10_data_records_inserted_into_dynamodb_table.png?raw=true)

11. **Records inserted into the table**
    - AWS Console showing records properly inserted into the DynamoDB table.
    ![Records Inserted](./Images_Watermarked/11_items_inserted_successfully.png?raw=true)

12. **Data Streaming into Shard**
    - Observing how data streams into Kinesis shards based on primary key hash.
    ![Data Streaming](./Images_Watermarked/12_data_coming_into_the_shards_based_on_the_hash_for_the_primary_key.png?raw=true)

13. **Update the quantity of an item in DynamoDB and see the change captured in the stream**
    - Changed the value of quantity from 5 to 35.
    ![Quantity Update](./Images_Watermarked/13_changed_quantity_from_5_to_35_in_dynamodb.png?raw=true)

14. **Change Captured in DynamoDB Shard**
    - Visualizing how a change is captured in the DynamoDB shard.
    ![Change Capture](./Images_Watermarked/14_change_captured_in_the_dynamodb_shard.png?raw=true)

15. **Deleting an Order**
    - Deleting an item from the DynamoDB table.
    ![Order Deletion](./Images_Watermarked/15_deleting_an_order.png?raw=true)

16. **Event Capture in Kinesis**
    - Capture of delete event in Kinesis for downstream processing.
    ![Event Capture](./Images_Watermarked/16_event_captured_in_kinesis.png?raw=true)

17. **Kinesis Data Firehose Setup**
    - Creating Kinesis Data Firehose to streamline data transfer to S3.
    ![Kinesis Data Firehose Setup](./Images_Watermarked/17_creating_kinesis_data_firehose_to_capture_kinesis_stream_and_send_to_s3.png?raw=true)

18. **Lambda for Data Transformation**
    - Setting up a Lambda function for transforming data before S3 storage.
    ![Lambda Setup](./Images_Watermarked/18_Creating_lambda_for_transforming_firehose_data.png?raw=true)

19. **Lambda Code for Transformation (Part 1)**
    - Showcasing the initial part of the Lambda function code used for transforming data. We are casting the data types to the correct type.
    ![Lambda Code Part 1](./Images_Watermarked/19_lambda_code_for_transformation_1.png?raw=true)

20. **Lambda Code for Transformation (Part 2)**
    - The continuation of the Lambda function code for data transformation. We are adding the timestamp and the event type details so that we can accurately update our target.
    ![Lambda Code Part 2](./Images_Watermarked/20_lambda_code_for_transformation_2.png?raw=true)

21. **Change Execution Time out of Lambda**
    - Adjusting the execution time settings for the Lambda function to optimize performance.
    ![Lambda Execution Time](./Images_Watermarked/21_change_execution_time_of_lambda.png?raw=true)

22. **Adding the Lambda in Firehose**
    - Integrating the configured Lambda function into the Kinesis Data Firehose setup.
    ![Lambda in Firehose](./Images_Watermarked/22_adding_the_lambda_in_firehose.png?raw=true)

23. **Adding Permissions for the Lambda Role**
    - Setting up the necessary IAM permissions for the Lambda role to function within the AWS ecosystem. Ideally, in a production environment, you will never give full permission to these IAM roles or IAM users. Always follow the principle of least privilege and don't give more permissions than necessary to complete the task at hand.
    ![Lambda Permissions](./Images_Watermarked/22_adding_the_required_permissions_for_the_lambda_role.png?raw=true)

24. **Setting Up the S3 Destination in Firehose**
    - Configuring the destination S3 bucket in Kinesis Data Firehose for storing the processed data.
    ![S3 Destination Setup](./Images_Watermarked/23_adding_the_destination_s3_bucket_in_fireshose.png?raw=true)

25. **Kinesis Data Firehose Stream Creation**
    - Finalizing the creation of the Kinesis Data Firehose stream for real-time data processing and storage.
    ![Firehose Stream Creation](./Images_Watermarked/24_kinesis_data_firehose_stream_successfully_created.png?raw=true)

26. **Publishing New Data to DynamoDB**
    - Adding new data records to DynamoDB for ingestion into the S3 bucket.
    ![Publishing Data](./Images_Watermarked/25_publishing_new_data_to_dynamodb_for_ingestion_into_s3.png?raw=true)

27. **Data Ingestion into S3**
    - Monitoring the data getting captured in the S3 bucket from DynamoDB.
    ![Data Ingestion](./Images_Watermarked/26_data_getting_ingested_into_s3.png?raw=true)

28. **Ingested Data in S3**
    - Verification of data successfully loaded into the S3 bucket.
    ![Ingested Data Verification](./Images_Watermarked/27_ingested_data_in_s3.png?raw=true)

29. **Data Ingested as JSON in S3 Bucket**
    - Confirming that the data was ingested in JSON format into the S3 bucket, ready for analysis.
    ![Data as JSON](./Images_Watermarked/28_data_ingested_as_json_in_s3_bucket.png?raw=true)

30. **Creating a Glue Data Crawler**
    - Setting up a Glue Data Crawler to create a data catalog from the S3 data.
    ![Glue Data Crawler Setup](./Images_Watermarked/29_creating_glue_data_crawler_to_create_a_data_catalog.png?raw=true)

31. **Adding S3 as Data Source**
    - Configuring S3 as a data source in the AWS Glue service.
    ![S3 as Data Source](./Images_Watermarked/30_adding_s3_as_data_source.png?raw=true)

32. **Custom Classifier for Parsing JSON Data**
    - Adding a custom classifier in Glue to parse JSON data stored in S3.
    ![Custom Classifier](./Images_Watermarked/31_adding_custom_classifier_to_be_able_to_parse_json_data_in_s3.png?raw=true)

33. **Configuring Classifiers and Data Sources in Glue Crawler**
    - Final adjustments to classifiers and data sources for optimal data parsing and cataloging.
    ![Classifiers and Data Sources](./Images_Watermarked/32_adding_classifiers_and_data_sources_in_glue_crawler.png?raw=true)

34. **Complete Steps to Create Glue Crawler**
    - A comprehensive view of all steps involved in creating the Glue Data Crawler.
    ![Complete Glue Crawler Creation](./Images_Watermarked/33_all_steps_to_create_crawler.png?raw=true)

35. **Running the Glue Crawler**
    - Initiating the run of the Glue Data Crawler to process and catalog the data.
    ![Running Glue Crawler](./Images_Watermarked/34_run_the_crawler.png?raw=true)

36. **Glue Crawler Finished Running**
    - Confirmation that the Glue Crawler has successfully completed its run.
    ![Glue Crawler Completion](./Images_Watermarked/35_crawler_finished_running.png?raw=true)

37. **Glue Metadata Table Created**
    - Creation of metadata tables in AWS Glue, facilitating easier data querying and analysis.
    ![Glue Metadata Table](./Images_Watermarked/36_glue_metadata_table_created.png?raw=true)

38. **Querying Data in S3 Using Athena**
    - Using Athena to perform SQL queries on the data stored in S3, made accessible through Glue's metadata tables.
    ![Querying with Athena](./Images_Watermarked/37_querying_the_data_in_s3_using_athena.png?raw=true)
39. **We can build dashboards on top of this and the data will be updated in real time as changes are made to the corresponding DynamoDb table**
    











