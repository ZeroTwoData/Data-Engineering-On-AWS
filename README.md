# E-Commerce Data Engineering Project Using AWS

# Introduction
This project was a means to test my data engineering knowledge through the planning & construction of data pipelines, using one the most popular webservices known as "AWS." If you have no clue what I'm talking about 😂, then your in luck! This Github repo was specifically designed to explain exactly what data engineering is and it's purpose in the real world to readers with little to no technical knowledge.😉

## What Is the Purpose of Data Engineering?

Data Engineering is the process of making data usable for the business, by making it accessible for "data customers (BI Analysts, Data Analysts, Data Scientists, etc.)" who will ultimately analyze this data for business needs. Data customers are only able to access & analyze data, after a data engineer has built a system in which they can retrieve the data from. These systems, specifically referred to as "data pipelines," are an end-to-end process that ingest raw(unorganized)/organized data, process it, prepare it, store it, and finally visualize the data for the customers to use. I found that the best way to begin designing pipelines, is to begin by creating a blueprint. Below is a blueprint for which data pipelines can be built upon.

![DS Blueprint](https://user-images.githubusercontent.com/74563990/159792405-7195ed58-7244-44a3-8b55-9719ba720d25.jpg)


## How Does The DS Blueprint Work

A DS Blueprint will always consist of five phases: 
* Connect: SQL DB's, Datawarehouses, Web Apps, or API's that hold the data, so data engineers can easily connect to these sources to ingest it for their pipelines 
* Buffer: 
* Processing:
* Store:
* Visualize:


# Contents

- [The Dataset](#the-dataset)
- [Platform Design](#platform-design)
  - [Connect](#connect)
  - [Buffer](#buffer)
  - [Processing](#processing)
  - [Storage](#storage)
  - [Visualization](#visualization)
- [Data Pipelines (Concepts)](#data-pipelines-(concepts))
  - [Data Ingestion Pipeline](#data-ingestion-pipeline)
  - [Stream To Raw Storage Pipeline](#stream-to-raw-storage-pipeline)
  - [Stream To DynamoDB Pipeline](#stream-to-dynamodb-pipeline)
  - [Visualization API Pipeline](#visualization-API-pipeline)
  - [Visualization Warehouse Pipeline](#visualization-warehouse-pipeline)
  - [Batch Processing Pipeline](#batch-processing-pipeline)
- [Buidlding Ingestion Data Pipeline](#building-data-ingestion-pipeline)
- [Demo](#demo)
- [Conclusion](#conclusion)
- [Follow Me On](#follow-me-on)


# The Dataset

## Selecting the Dataset
For this project I decided to select a dataset that would closely replicate data that I would work with at an actual company. Since this is my first project, I wanted to make sure to select a csv dataset (to reduce the number of columns that I had to work with) that wasn't too small or too large to handle. So after some browsing on Kaggle, I decided to select a e-commerce dataset that was 43MB in size. Some key factors that this dataset included were descriptive column names (which are much easier to work with if you are using a relational SQL database), customer id's, invoice number's, invoice date's, unit price, and product quantity.

Dataset Link: https://www.kaggle.com/carrie1/ecommerce-data

## Defining the Purpose of the Dataset
Before building a pipeline it is essential to understand what the business requires it for. Will the visualized date be used for business goals or business intelligence goals. Business goals can include processsing transactions from various stores in a single location or give customers access to his/her purchase history. Business intelligence goals can include obtaining data such as average sales, most sold products, and most valuable products per hour/day/month/year. Understanding what results you want you from the data is key to developing the optimal data pipeline.

## Storage Possibilities
The data can either be stored in a relational database or a NoSQL database. If we wanted to create relationships between the columns of data and that data is already strucutred, then it would make sense to use a relational database. Although if there are plans to scale (adding hundreds or thousands of columns to the dataset), then it would make more sense to develop a NoSQL database, so as to avoid the relational database from becoming too complex to understand. Although it would be optimal to design a relational database for this particular dataset, I plan to develop a NoSQL database for my dataset. Creating a NoSQL database will prepare myself for having to develop databases at scale in the future.

If I were to use a relational database this is how it would begin to look:

NoSQL database planning:

<br />

# Platform Design

## Selecting the Tools
In this section, we will look at the main components of our data pipeline, which model our DS Blueprint listed above.
<img src="https://user-images.githubusercontent.com/74563990/159989202-57605a99-9fe8-4e85-9958-135a6cd5604d.jpg" width="300"/>

## Client
Usually this doesn't need to be created because there would a system or device (a.k.a the client) that is going to send data to the API at a company. But in my case, since there isn't a device that can accomplish that task, I have created a Python Client using AWS SDK (a.k.a Boto3) that takes the csv and it selects data either on a row basis, basis of number of lines, or date that is going to be sent to my API. The Python Client will also transform each of my rows into a JSON string before sending, becuase JSON is more of an organized format to analyze the data.

## Connect
My client is sending data to the API Gateway that is hosting a URL. Once the data is sent there, living in the background is a Lambda function that is getting triggered by the API Gateway and is processing the JSON that we have. Ultimately the Lambda function will send it into some system, such as a database or database buffer. In my case the data will be sent to a database buffer first.

<img src="https://user-images.githubusercontent.com/74563990/160052778-18b56aac-705b-4b5d-86b2-aea2b9f40389.png" width="600"/>

## Buffer
Kinesis, also known as a message queue (consist of two parts: a producer which sends data into the message queue and a consumer which takes data out of the message queue). In my case the producer is the Lambda function that sits behind the API Gateway becuase it is ultimately "producing" (or sending) my processed data into the message queue. On the consumer end can be either another Lambda function or a tool such as Kinesis firehose which takes the data back out.

<img src="https://user-images.githubusercontent.com/74563990/160053165-b92a0869-5ad5-48ae-bd11-67449cecdbd3.png" width="600"/>

## Processing
There are two ways of processing data. Stream processing or Batch processing.

Stream processing is a continuous process that begins with a <b>source</b> that is sending data into <b>processing</b>, in which then it is processed, and sent to it's <b>destination</b>. In my case the source would begin with Kinesis, that is sending data into the processing (Lambda function) and sends it into it's destination.

<img src="https://user-images.githubusercontent.com/74563990/160053624-b35b734d-9c72-40bb-9609-156f9f1a7a43.png" width="600"/>

Batch processing begins with the <b>scheduler</b> that activates the <b>processing</b>, which then connects to the data <b>source</b>, and ultimately writes it to it's <b>destination</b>. In my case the source would begin with a scheduler tool such as "CloudWatch" or "Airflow" that activates the processing (Lambda function), which then connects to the data source Kinesis and ultimately writes it to it's destination.

<img src="https://user-images.githubusercontent.com/74563990/160053663-e6eb5068-9b4a-405d-80c3-721763821f06.png" width="600"/>

## Store
For my file storage I'm going to use AWS S3 becuase it is widely used and simple to understand. For my NoSQL database, which is going to store my transactions, I'm going to use DynamoDB. For my analytical layer, I will be using the datawarehouse AWS Redshift, because it allows for distributed storage (which is great for data that scales).

## Visualize
For visualization purposes I will be using the business intelligence tool Tableau becuase it is popular and easily connects to AWS Redshift. 

<br />

# Data Pipelines (Concepts)

## Data Ingestion Pipeline
The Python Client is going to send csv rows as JSON into the API Gateway. The Lamda function will process the JSON file and send it into Kinesis.

<img src="https://user-images.githubusercontent.com/74563990/160485088-a7aeaa81-5bf1-4806-918d-e049332e72dd.png" width="600"/>

## Stream To Raw Storage Pipeline
Data that is lying in the Kinesis stream will trigger another Lambda function, which takes the data and dumps it into a S3 bucket. The main use case for this is to put data into a data lake or another storage that can be used later. 
*To prevent a lot of files from being dumped into the S3 bucket at one time, I configured the Lambda function to wait for a specified amount of time after each transaction. 

<img src="https://user-images.githubusercontent.com/74563990/160485547-57b446d5-4fa6-4998-aca9-b06d5b88a6c1.png" width="600"/>

## Stream To DynamoDB Pipeline
Kinesis insert triggers the Lambda function for DynamoDB. The Lambda is responsible for reformatting/preprocessing the data, writing the customer data (customer + invoices), and writing invoice data (invoice + stockcode)

<img src="https://user-images.githubusercontent.com/74563990/160489461-4ade4019-f8ad-4f7a-9e9a-d379904cf733.png" width="600"/>

## Visualization API Pipeline
Created an API as the client's UI. It is responsible for querying Items from an Invoice. Since the data rests in the DynamoDB table "Invoices," the client can request Items for the "InvoiceNo (request parameter)." Lambda triggered by the API queries Dynamo with InvoiceNo

<img src="https://user-images.githubusercontent.com/74563990/160490552-15de3b59-5474-4588-bfac-9305cfb8a9c3.png" width="600"/>

## Visualization Datawarehouse Pipeline
Kinesis Firehose Delivery Stream connects to the Kinesis Data Stream. Once connected, Kinesis Firehose stores data into an S3 Bucket for intermediate storage. Firehose then copies data from the Bucket into the Redshift Table. Once in Redshift, Tableau can connect and be open for analyst to use.

<img src="https://user-images.githubusercontent.com/74563990/160491466-bf878116-48c1-4616-a622-54b651b7f0be.png" width="600"/>

## Batch Processing Pipeline
A bulk of files will be stored in S3 and a Lambda function will take the data and move it into the DynamoDB tables or the Redshift table. Before all this can happen, Cloudwatch (a scheduler) will trigger the process.

<img src="https://user-images.githubusercontent.com/74563990/160494091-ac1fa5d1-2c85-4fae-9b5e-365fb7543903.png" width="600"/>


# Building Data Ingestion Pipeline

## Creating Lambda Functions
I created 3 Lamda functions using AWS Lamda. Those three are:
* Read-Write-To-Kinesis (USING THIS FUNCTION ONLY FOR INGESTION PIPELINE)
* Write-To-S3
* Read-Write-To-DynamoDB

<img src="https://user-images.githubusercontent.com/74563990/161111353-ea1cbd77-35e5-49cb-9b5a-a0068e12afea.png" width="600"/>

## Setting Up Kinesis
Created a Kinesis Stream to take in my data named APIData

<img src="https://user-images.githubusercontent.com/74563990/161132891-1a48292a-4420-4e07-a606-07077a074827.png" width="600"/>

## Established IAM Roles For Function
Read-Write-To-Kinesis Roles:
* FULL ACCESS Read Functions to Kinesis
* Write Functions: PutRecord (write one request), PutRecords (write multiple request); Allowed writing access to APIData Kinesis Stream
<img src="https://user-images.githubusercontent.com/74563990/161111128-5c76e629-f2b8-4f74-a9a1-cb2781949201.png" width="600"/>

## Creating API Gateway
Created an API named "E-commerce", created a resource (consists of a url, an HTTP method, a response type, a JSON Schema describing the input(s) to the API Resource, and a JSON Schema describing the output) named "e-commerce", and created two HTTP methods: GET (to read the data from DynamoDB table later) & POST (to write the data to Kinesis)

<img src="https://user-images.githubusercontent.com/74563990/161112549-07254b3a-4a45-4046-82dd-5c6409c992dc.png" width="600"/>

Set Mapping (API Gateway uses mapping templates to transform requests before they are sent)

<img src="https://user-images.githubusercontent.com/74563990/161141341-00eccce9-4f41-4d45-bafd-8f0dd27a36ec.png" width="600"/>

## Lamda Code
Developed Code for the Lambda Function where I create the Boto3 client and use an if/elif/else decision structure to decide what to do if it is a GET or POST request.

<img src="https://user-images.githubusercontent.com/74563990/161139810-2eda0049-8fea-4f96-b276-a207973186af.png" width="600"/>

Created a test event that mimicks the JSON that I want the output to display

<img src="https://user-images.githubusercontent.com/74563990/161140169-f8ef12cc-0c61-430b-91ca-c2dd8b8246b6.png" width="600"/>

## Python Script To Send Data
Created a python script which uses a for loop to iterate through my "TestSample" (which includes the first 9 rows of my csv file)

TestSample:

<img src="https://user-images.githubusercontent.com/74563990/161142153-e7b2f0c6-205d-44e5-9922-0c1ab2fcaeea.png" width="600"/>

Python Script:

<img src="https://user-images.githubusercontent.com/74563990/161141954-7acd3621-ddd5-4c78-9436-743dd21c704b.png" width="600"/>

## Testing the Pipeline
After running the Python Script, you can assume from the Response 200's that the data was sent into Kinesis as a JSON

<img src="https://user-images.githubusercontent.com/74563990/161142673-e50edc8c-0f13-4fd3-b7b0-6791d9e5c214.png" width="600"/>

But I wanted to make sure, so I checked the log in CloudWatch & Kinesis activity graph:

<img src="https://user-images.githubusercontent.com/74563990/161143420-6d77ba90-0488-495b-af12-f4c7d2071c32.png" width="600"/>

<img src="https://user-images.githubusercontent.com/74563990/161143732-d1baad15-24c6-448d-b002-26c763efe5e5.png" width="600"/>

# Stream To Raw Storage Pipeline

## Setup S3 bucket
Created an S3 bucket named e-comm bucket

## IAM Roles for Write-To-S3 Function
* FULL ACCESS to S3
* FULL ACCESS Read Functions to Kinesis

## Create Lambda Code for S3 Insert

<img src="https://user-images.githubusercontent.com/74563990/161152740-9c76804a-3e54-4181-a027-40fbd29af0c6.png" width="600"/>

## Testing the Pipeline
Created a new test event called myS3TestEvent to test my code.

<img src="https://user-images.githubusercontent.com/74563990/161152988-b4a9ddf3-f1e9-411a-8abe-539ddc25b289.png" width="600"/>



