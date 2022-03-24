# Data Engineering FAQ's

## What Is the Purpose of Data Engineering?

Data Engineering is the process of making data usable for the business, by making it accessible for data customers (BI Analysts, Data Analysts, Data Scientists, etc.) who will ultimately analyze this data for business needs. Data customers are only able to access & analyze data, after a data engineer has built a system in which they can retrieve the data from. These systems, specifically referred to as "data pipelines," are an end-to-end process that ingest raw/organized data, process it, prepare it, store it, and finally visualize the data for the customers to use). I found that the best way to begin designing any pipeline, is to begin by creating a blueprint. Below is a guide for which any data pipeline can be built upon.

![DS Blueprint](https://user-images.githubusercontent.com/74563990/159792405-7195ed58-7244-44a3-8b55-9719ba720d25.jpg)


## How Does The DS Blueprint Work

A DS Blueprint will always consist of five phases: 
* Connect: Usually business have already developed SQL DB's, Datawarehouses or API's that hold the data, so data engineers can connect 
* Buffer:
* Process:
* Store:
* Visualize:


# Before Building The Pipeline

## Selecting the Dataset
For this project I decided to select a dataset that would closely replicate data that I would work with at an actual company. Since this is my first project, I wanted to make sure to select a dataset that wasn't to small or to large to handle. So after some browsing on Kaggle, I decided to select a e-commerce dataset that was 43MB in size. Some key factors that this dataset included were descriptive column names (which are much easier to work with), customer id's, invoice number's, invoice date's, unit price, and product quantity.

Kaggle: https://www.kaggle.com/carrie1/ecommerce-data

## Selecting the Tools
In this section, we will look at the main components of our data pipeline, which model our DS Blueprint listed above.
<img src="https://user-images.githubusercontent.com/74563990/159933839-8ea40732-0159-4ad2-9823-79d41bd4e289.jpg" width="500"/>
