### Data Engineering Capstone Project

#### Project Summary
The project aims to answer US immigration from factors of immigrant demographics, destination city and temperature, and arrival date in the US. We extract data from two different sources, the I94 immigration dataset of 2016, and city temperature data from Kaggle. We design 3 dimension tables covering immigrant demographics spanning age, gender, and visa type, destination city and average temperature of 2013, and arrival date with flags on day of week, week of year, and month, and 1 fact table on immigration number. We use Spark for ETL jobs, and store result in parquet for downstream analysis.

#### Data Sources 
1. I94 Immigration Data: comes from the U.S. National Tourism and Trade Office and includes details on incoming international arrivals. The data includes information on country of origin, visa type, age, and port of entry [link](https://travel.trade.gov/research/reports/i94/historical/2016.html)
2. World Temperature Data: comes from Kaggle and includes temperature data in the U.S. since from 1850 to 2013 [link](https://www.kaggle.com/berkeleyearth/climate-change-earth-surface-temperature-data)

#### Conceptual Data Model

##### Staging Tables

    df_i94
        A. uuid
        B. date
        E. city_code
        F. state_code
        G. age
        H. gender
        I. visa_type
        J. count
        
    df_temp
        A. city_code
        B. city_name
        C. 2013_average_temperature

##### Dimension Tables

    df_immigrant
        A. uuid
        B. age
        C. gender
        D  visa_type
   
    df_city
        A. city_code
        B. 2013_average_temperature
        
    df_time
        A. date
        B. dayofweek
        C. weekofyear
        D. month
        
##### Fact Table

    df_immigration
        A. uuid
        B. city_code
        C. date
        D. count


### Steps to pipeline data into chosen data model

1. Clean the data on nulls, data types, duplicates, etc
2. Load staging tables for df_i94 and df_temp
3. Create dimension tables for df_immigrant, df_city, df_time
4. Create fact table df_immigration with information on immigration count, mapping uuid in df_immigrant, city_code in df_city, and date in df_time ensuring referntial integrity
5. Save processed dimension and fact tables in parquet for downstream query


### Discussions
Spark is chosen for this project as it is known for processing large amount of data fast (with in-memory compute), scale easily with additional worker nodes, with ability to digest different data formats (e.g. SAS, Parquet, CSV), and integrate nicely with cloud storage like S3 and warehouse like Redshift.

The data update cycle is typically chosen on two criteria. One is the reporting cycle, the other is the availabilty of new data to be fed into the system. For example, if new batch of average temperature can be made available at monthly interval, we might settle for monthly data refreshing cycle.

There are also considerations in terms of scaling existing solution.

###### data size increased by 100x##:<br>
We can consider spinning up larger instances of EC2s hosting Spark and/or additional Spark work nodes. With added capacity arising from either vertical scaling or horizontal scaling, we should be able to accelerate processing time.

###### dashboard updated by 7am daily##:<br>
We can consider using Airflow to schedule and automate the data pipeline jobs. Built-in retry and monitoring mechanism can enable us to meet user requirement.

###### database to be accessed by 100+ people##:<br>
We can consider hosting our solution in production scale data warehouse in the cloud, with larger capacity to serve more users, and workload management to ensure equitable usage of resources across users.
