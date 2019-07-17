---
title: What does deploying to production mean?
date: 2019-07-14 19:30:50
categories: machine_learning
permalink: /:categories/ml_to_prod
tags: ml
---

## Introduction and What I do, Where I do what I do.
I am a Machine Learning Engineer at [Acceldata](https://acceldata.io/); the tasks however actually comprises the trio of "Software Engineer, Machine Learning and Data Engineering"; though not at a Google scale. The *tagline* for Acceldata is *'Data Lake Operations Optimised'* and I help the team/company do exactly that using tools of Machine Learning, Time Series Forecasting, Anomaly Detection, etc. Out of interest and to make some extra bucks, I also mentor students at Udacity for Data Engineering and Data Analyst Nanodegrees.

## Problem Statement
As I mentioned above, I come from a Software Development background, and I can visualise all the nightmares that I or my colleagues have been through trying to productionize their code/idea. People having working in companies having periodic on-call activities might understand this even better. So, what does it actually mean when people say "Productionizing Machine Learning Projects"? :rocket: :rocket: :rocket: :rocket:

If one closely watches the trend of how courses that teach Machine Learning are designed, it is easy to see one similar pattern across all of the courses. Developing everything using the __Jupyter Notebooks__. Though it is the best way to learn Data Science and Visualize the results, productinizing code is a different beast altogether. So, there is a clear gap when it comes to models in _Notebooks_ vs models in _Production_. In this post, I would like to address this GAP and show how we at [Acceldata](https://acceldata.io/) productionize machine learning projects.

Writing a Tech Spec documentation almost always helps. I did this as part of the engineering team and am very much used to it. Here are a few resources which talk about writing good tech specs. The content would be slightly different in case of a machine learning project, but the need for the tech spec remains the same. Getting an idea of the project, the features and the scope.

So, let's focus on what the the problem we have at hand and learn more about how we tackled it. Below is the explanation of how I take ML Models to production.

## Requirements
> To forecast the resource usage for different queues in [YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html).

To give you more information about this, the queues in YARN can be imagined to be similar to [queues](https://en.wikipedia.org/wiki/Queue_(abstract_data_type)) in Data Structures and used for the task of [scheduling](https://hortonworks.com/blog/yarn-capacity-scheduler/) of jobs, tasks. So, these queues are given a certain capacity which is the number of tasks, jobs that can be stored in it. The queue can be partitioned into multiple sub-queues, but for brevity I will consider a single queue. At any point in time, the queue has a property which is its __'Used Capacity'__. Our task is to forecast how much capacity of the queue will be used at some point in the future.

> Using the above forecast, business users will be able to take actions as to upscale or downscale their queues so as to meet the needs at times in the future.

## Simple High Level Design
![](/assets/images/machine_learning/hld_ml_to_prod.png)

Let's start going through each of the components in detail.

### MongoDB
We use MongoDB to store log information and also resource usage values for each of the queues in YARN. Now, to analyze and write predictive algorithms about this data, it is imperative that we import this data and then process it.

### InfluxDB
Most of the data that we collect at Acceldata is time-series based and it only makes sense to store it in a time series database. The debate about why InfluxDB and not any other TSDB is best left for another post. So finally, I too choose to pump in time-series based predictions to InfluxDB.

### ML Pipeline
- Clean and Preprocess the data and get it ready to push to a time-series model.
- Train the model based on multiple hyper-params and params and choose the best model
  -

### Dashboard
Though this component is developed by the UI team, it only makes sense to add it here to show how the end result looks like. A Data Science project is incomplete without visualisation. The predicted data that is pumped into InfluxDB is rendered to the UI and below is how the predictions look like. The below image shows 2 queues i.e. DEFAULT and LLAP which are sub-queues under root queue. The first image is a merge of the actual queue usage and predicted queue usage for the current day. The second image is the predictions for the next day.

![](/assets/images/machine_learning/capacity_prediction_1.png)
![](/assets/images/machine_learning/capacity_prediction_2.png)

## Looking at the Machine Learning Pipeline in detail

### Software Engineering
  - Designing Database Access Layers (importing and exporting data into various databases by abstracting business logic)
  - Abstracted Object Oriented Design for exposing Models to the business users (in this case the UI Dashboard)
  - Designing Data Model (for choosing the hyper-params and other model specific params to suit business needs)

### Data Engineering
The important point to note is that, using fancy tools to accomplish a task is not software or data engineering. Making correct (close to correct :bowtie:) choices to ease/fasten production usage keeping scaling in mind are signs of a good engineer.
  - As the current pipeline stands, getting data from multiple sources (MongoDB and InfluxDB) is easy for me with a few configurations so that the Machine Learning pipeline can kick in sooner.
  - In most cases where devs are dealing with multiple data sources and various business users, the data engineering pipelines get complicated. Data storage and retrieval becomes a huge headache and choices of SQL vs NOSQL dbs, Row vs Columnar dbs, etc kick in design discussions about such cases are topics for another post.

### Machine Learning
  - Import Data
  Once the data engineering (simply data ingestion) module is ready, data is imported, merged and mutated into a DataFrame which is easier to work with Data Science workflows. The ML module now has the queue data in DataFrames format.

  - Clean Data and Feature Engineering
  As you might have heard more than a thousand times and almost every one discusses this as the most important aspect of Data Science, the current workflow also uses this aspect very well. Some of the techniques used are:
    - Cleaning Unwanted Columns
      - highly correlated data (noisy data)
      - extra columns that may be passed by the data source
    - Developing Useful Time Features
      - day of the week/hour/month
      - week of the month/year
      - month/quarter of the year
      - holiday
      - whether weekend or not
      - proximity to weekend
    - Scaling Data <br>
      I have seen this data preparation technique improve results almost all the times. It is just easy for the model to comprehend data that fit in the same scale.
    - Sample Data <br>
      The data captured from Hadoop and other components and system metrics is too frequent (seconds) to be used directly for modelling and hence sampling into higher time measures is very important using aggregations like mean, median, min, max depending on the use-case. This way a lot of noise is excluded from entering the model.
  - Fit Data and Forecast
    - Now that we have the data ready to be run model on, we choose the train and test data and use multiple algorithms to see which one of the Algorithms, hyper-params and params give best results.
    - The best model is chosen and forecasts are then generated. Our models are based on Facebook's [Prophet](https://facebook.github.io/prophet/),
    [LSTM](https://colah.github.io/posts/2015-08-Understanding-LSTMs/) and [VAR](https://en.wikipedia.org/wiki/Vector_autoregression).
  - Prepare Data for writing results to DB
    Data preparation is not only necessary to train the model. When we often write back data to some database, some design choices have to be made so that the end/business users can make appropriate use of the data. Tasks can be something like:
    - adding time as a field or even making it a primary key
    - writing data keeping in mind the indexes for the tables/documents/measurements
    - adding extra incremental fields in very rare cases that might be required by the charting libraries to render and display on the

    This task is (mostly) closely related to Data Engineering but the current use-case is simple and hence to not complicate the task, I avoided separating from the ML module. But, it is absolutely recommended to separate it from the ML logic. There is a high probability that I might have actually implemented this by the time you stumble upon this post (:wink:).

## Evaluation of the Models
Evaluation or Error Calculation is the measure that decides which model works better and should be chosen for forecasts on production data. Since dealing with absolute values, we have used [MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) as the error measure.

## Data Model
This is where software engineering again comes into picture. The business users have better idea about the sampling frequency and prediction periods. So, it makes sense to accept these kind of params from the users and prepare our data or run our models based on them. So, there is a simple web app that routes this data from user to our forecasting app. Below is a sample of the request payload.
```json
{  
   "job_name":"Resource Forecasting",
   "job_description":"Get predictions for next 'k' days/hours/months",
   "user_name":"Bob",
   "project":"capacity forecasting",
   "owner":"ui-service",
   "data":{  
      "source":{  
         "name":"mongodb",
         "document":"document_name",
         "collection":"collection_name"
      },
      "destination":{  
         "name":"influxdb",
         "database":"db_name",
         "measurement":"measurement_name"
      }
   },
   "aggregation_time_period":"1h",
   "data_ingestion_time_period":"5d",
   "model":"prophet",
   "future_prediction_time_period":"3d"
}
```

This can be persisted in a db to keep an account of the usage but again for brevity I will skip discussing about it.

## How to get going when you are on your own?

This section has got nothing to do with productionizing ML pipelines. Here are some tips that can help you to get work done in case there is less help available.

1. The Rubber Duck Methodology <br>
  Read more about the [technique](https://en.wikipedia.org/wiki/Rubber_duck_debugging) here. However, in short, it just means to explain the problem and the solution to yourself and convince that this is correct. Remember that along with just completing a task, you are learning something that might come in handy in the future. So, try to grill yourself as much as you can to make sure what you are doing it right. However, do not fall into the trap of perfecting your work. There is a fine line between the two.
2. Read <br>
  There is clearly a lot of information available about how to productionize ML models. Read through engineering/data science blogs from good companies that do not shy away from sharing their experiences online. Read and reread to grasp the stuff and try to implement what suits your needs.
3. Write <br>
  There is a huge community out there on Twitter, LinkedIn, Medium where you can share your work and get suggestion/tips. However, do talk to your seniors to know if it is okay to share the content publicly before doing it. I have a green flag from my CEO to give you a perspective.
