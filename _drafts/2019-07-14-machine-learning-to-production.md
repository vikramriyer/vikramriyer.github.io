---
title: What does deploying to production mean?
date: 2019-07-14 19:30:50
---

# Introduction and What I do, Where I do what I do.
I am a Machine Learning Engineer at [Acceldata](https://acceldata.io/); the tasks however actually comprises the trio of "Software Engineer, Machine Learning and Data Engineering". The *tagline* for Acceldata is *'Data Lake Operations Optimised'* and I help the team/company do exactly that using tools of Machine Learning, Time Series Forecasting, Anomaly Detection, etc.

I come from a Software Development background, and I can visualise all the nightmares that I or my colleagues have been through trying to productionize their code. People having worked in companies having periodic on-call activities might understand this even better.

If one closely watches the trend of how courses that teach Machine Learning are designed, it is easy to see one similar pattern across all of the courses. Developing everything using the __Jupyter Notebooks__. Though it is the best way to learn Data Science and Visualize the results, productinizing code is a different beast altogether. So, there is a clear gap when it comes to models in _Notebooks_ vs models in _Production_. In this post, I would like to address this GAP and show how we at [Acceldata](https://acceldata.io/) productionize machine learning projects.

__Note__: A forecasting problem might not be a Machine Learning problem, but since we are learning the representation of several independent variables to one dependent variable and predicting the dependent variable, I will be brave and call it a Machine Learning (like) model.

# Problem Statement

> To forecast the resource usage for different queues in [YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html).

To give you more information about this, the queues in YARN can be imagined to be similar to [queues](https://en.wikipedia.org/wiki/Queue_(abstract_data_type)) in Data Structures and used for the task of scheduling ([read more here](https://hortonworks.com/blog/yarn-capacity-scheduler/)) of jobs, tasks. So, these queues are given a certain capacity which can be imagined to be the number of tasks, jobs that can be stored in it. The queue can be partitioned into multiple sub-queues. The queue has a property which is its __'Used Capacity'__ at a point in time. Our task is to forecast how much capacity of the queue will be used at some point in the __future__.

> Using the above forecast, business users will be able to make informed decisions as to whether upscale or downscale their queues.

Let's talk a bit about the components that we will discuss throughout the post and how they are interlinked and complete the puzzle i.e. ML to production.

# Simple High Level (System) Design
![](/assets/images/machine_learning/hld_ml_to_prod.png)

Let's start going through each of the components i.e. [DocumentDB](https://en.wikipedia.org/wiki/Document-oriented_database), [TSDB](https://en.wikipedia.org/wiki/Time_series_database) and then the ML pipeline in that order.

## DocumentDB
We use DocumentDB to store log information of all the components that we support ([check here](https://trial.acceldata.dev/#/dashboard)) and also resource usage values for each of the queues in YARN. Now, to analyse and write predictive algorithms about this data, it is imperative that we import this data and then process it.

Below is a snapshot (skipped features and samples) of how the data looks like.

|_id|absMaxCapacity|numActiveApps|numPendingApps|vcoresTotal|
|---|-------------------|--------------------|---------------------|-----------|
|1|100                |75                  |75                   |1071       |
|2|15.34              |1                   |1                    |164        |
|3|28.57              |31                  |31                   |306        |
|4|38.09              |41                  |41                   |408        |
|5|18                 |2                   |2                    |193        |

Each of the rows above describe the status of a __queue__ at a particular point in time.

## TSDB
Now Most of the data that we collect at Acceldata is time-series based and it only makes sense to store it in a time series database. Since the predictions
are time-series based i.e. the forecasts are timestamps in the future displaying the queue usage, all the predictions are written to TSDB. We acknowledge the fact that no one can be perfect. So along with the predictions, we make sure to predict the upper and lower bounds of the queue usage so as to give the business user a perspective of how much variance one can expect.

## ML Pipeline
- Clean and Preprocess the data and get it ready to pass to a time-series model.
- Train the model and based on error criteria ([discussed below](#evaluation-of-the-models)), choose the best model
- Forecast predictions, upper bound as well as lower bounds
- Prepare data to write into TSDB

A detailed explanation for each of the Machine Learning Steps involved is discussed [later](#looking-at-the-machine-learning-pipeline-in-detail) in this post.

## Dashboard
Finally, time to see the results! A Data Science project is incomplete without visualisation. The predicted data that is pumped into TSDB is rendered to the UI and below is how the predictions look like.

The below images show 2 queues i.e. DEFAULT and LLAP which are sub-queues under root queue. The first image merges the __actual queue usage__ and __predicted queue usage__ for the current day. The second image is the predictions for the next day. The number of days to predict in the future is a configurable param that is present in the [data model section](#data-model).

![](/assets/images/machine_learning/capacity_prediction_1.png)
![](/assets/images/machine_learning/capacity_prediction_2.png)

# Looking at the Machine Learning Pipeline in detail

This is the most important section and actually explains what the __title__ promised. Let's discuss what all are the parts when stitched together help us get a Machine Learning model to production.

## Software Engineering

Software Engineering is an art of abstracting tech stuff so that even users who do not understand code can use it to get the results they desire. Below are some of the tasks that fall under the software engineering category and important to complete the pipeline.

  - Designing Database Access Layers also called DAO <br>
  Since we are dealing with multiple sources of data, we cannot expect the business user to specify how to query the sources for data. We expect them to specify where the data is located. Writing code to extract data from different sources lies in the hands of the Software Engineers that design the these systems
  - Web App layer to get details about the params for the Data Ingestion sources <br>
  The business users request data through this layer that stands as an interface between the Machine Learning models and them. For this to happen, a usual practice is to design an API or Data Model that can be exposed via a REST/web layer discussed in the above point

## Data Engineering

There are blog posts that discuss how Data Engineers extracted data from different data sources and warehouses, and merged them and pushed to a SQL or NOSQL db or even CSV files so that the Data Scientists would be able to work seamlessly with the data. I will simplify it and discuss how I performed data engineering to get data into the Machine Learning models.

  - In most cases where devs are dealing with multiple data sources and various business users, the data engineering pipelines get complicated. Data storage and retrieval becomes a huge headache and choices of SQL vs NOSQL vs warehouses, Batch vs Stream processing, etc kick in design discussions about such cases are topics for another post.
  - For us fortunately (as of now), the data sources are TSDB and DocumentDB only. Using the abstracted tools from the Software Engineering [section](#software-engineering), we can get this data in the format that is desired using various db clients. Most of the code for this is written in Python and most of API clients have a provision to get this data in the [DataFrame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) format that is best suited for Machine Learning models.

Now that we have our data extracted and ready for Machine Learning model, let's head into seeing what are the various parts in Machine Learning.

## Machine Learning

### Import Data
Once the data engineering (simply data ingestion) module is ready, data is imported, merged and mutated into a DataFrame which is easier to work with Data Science workflows. The ML module now has the queue data in DataFrames format.

### Clean Data and Feature Engineering
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
  - Normalising/Scaling Data <br>
    I have seen this data preparation technique improve results almost all the times. It is just easy for the model to comprehend data that fit in the same scale.
  - Sample Data <br>
    The data captured from Hadoop and other components and system metrics is too frequent (seconds) to be used directly for modelling and hence sampling into higher time measures is very important using aggregations like mean, median, min, max depending on the use-case. This way a lot of noise is excluded from entering the model.

### Fit Data and Forecast
  - Now that we have the data ready to be run model on, we choose the train and test data and use multiple algorithms to see which one of the Algorithms, hyper-params and params give best results.
  - The best model is chosen and forecasts are then generated. Our models are based on Facebook's [Prophet](https://facebook.github.io/prophet/),
  [LSTM](https://colah.github.io/posts/2015-08-Understanding-LSTMs/) and [VAR](https://en.wikipedia.org/wiki/Vector_autoregression).

### Prepare Data for writing results to DB <br>
  Data preparation is not only necessary to train the model. When we often write back data to some database, some design choices have to be made so that the end/business users can make appropriate use of the data. Tasks can be something like:
  - adding time as a field or even making it a primary key
  - writing data keeping in mind the indexes for the tables/documents/measurements
  - adding extra incremental fields in very rare cases that might be required by the charting libraries to render and display on the

  This task is (mostly) closely related to Data Engineering but the current use-case is simple and hence to not complicate the task, I avoided separating from the ML module. But, it is absolutely recommended to separate it from the ML logic. There is a high probability that I might have actually implemented this by the time you stumble upon this post (:wink:).

# Evaluation of the Models
Evaluation or Error Calculation is the measure that decides which model works better and should be chosen for forecasts on production data. Since dealing with absolute values, we have used [MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) as the error measure.

# Data Model
This is where software engineering again comes into picture. The business users have better idea about the _sampling, frequency_ and _prediction periods_ (remember we mentioned earlier accepting information from business users). So, it makes sense to accept these kind of params from the users and prepare our data or run our models based on them. This can be highly debated but suited our use case. Below is a sample of the request payload.

```json
{  
   "job_name":"queue_usage_forecaster",
   "job_description":"Get predictions for next 'k' days/hours/months",
   "user_name":"Bob",
   "project":"Capacity Forecasting",
   "owner":"ui-service",
   "data":{  
      "source":{  
         "name":"documentdb",
         "document":"<replace_with_apt_name>",
         "collection":"<replace_with_apt_name>"
      },
      "destination":{  
         "name":"tsdb",
         "database":"<replace_with_apt_name>",
         "measurement":"<replace_with_apt_name>"
      }
   },
   "aggregation_time_period":"1h",
   "data_ingestion_time_period":"5d",
   "future_prediction_time_period":"3d"
}
```

This can be persisted in a db to keep an account of the usage but again for brevity I will skip discussing about it.

# Conclusion
We have seen how to productionize ML pipelines and how Software Engineering, Data Engineering and Machine Learning all play a role in the Productionization of such pipelines.
