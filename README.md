# Data Warehouse Project - Azure
---

## STEP 1 - Defining the problem

The most important part of solving a problem is first, to define the problem, and define it right, and in this part, we are going to define the problematic to which we are trying to build a data warehouse.

Since we are living through this corona epidemic then it definitely seems like there is no better subject to try to analyse its data other than the famous covid-19. and since its data is more of a historical data, a data warehouse would be just perfect to store its data, in a dimensional/facts view and then try and draw insights from it by running analysis, business intelligence, artificial intelligence on it.

So my objective out of this project should be, to create a data warehouse, which would help me ask questions about corona in Morocco, and also be able to compare its answers with other parts of the world, in a way that "hopefully" might draw more insight to Morocco's situation with comparison to its neighbor countries and the world. And if possible, on top of the descriptive analytics be able to run predictive analytics or the data.

## STEP 2 - Getting the data

### 2.1. Finding the right Data

After going through many resources, and searching all over the internet I was able to find many interesting datasets on (Kaggle, World Health Organization, ...), the very best one i could find is at under this url : [https://www.who.int/emergencies/diseases/novel-coronavirus-2019/situation-reports](https://www.who.int/emergencies/diseases/novel-coronavirus-2019/situation-reports), but it was difficult to scrap the data, or save it in a readable format, since it was embedded inside pdf reports.

So the next best one I was able to find was this API → ([https://corona.lmao.ninja/](https://corona.lmao.ninja/)). 

It's open source, JSON formatted so it s easy to check and read, they claim that their dataset is up to date (updates every 10 minutes) and also multi-Sourced which gives it more credibility.

- List of the sources used :
    - https://www.worldometers.info/coronavirus/
    - https://github.com/CSSEGISandData/COVID-19/tree/master/csse_covid_19_data/csse_covid_19_time_series
    - https://github.com/nytimes/covid-19-data
    - https://github.com/ActiveConclusion/COVID19_mobility
    - https://www.canada.ca/en/public-health/services/diseases/2019-novel-coronavirus-infection.html
    - https://github.com/pcm-dpc/COVID-19
    - https://www.rki.de/DE/Content/InfAZ/N/Neuartiges_Coronavirus/Situationsberichte/Gesamt.html
    - https://info.gesundheitsministerium.at/
    - https://www.mohfw.gov.in/
    - https://covid19.ncdc.gov.ng/
    - https://github.com/openZH/covid_19/
    - https://coronavirus.data.gov.uk

### 2.2. Understanding the API

One can access the data by sending a **GET** request to one of the following links. 

A link can be built using the following url elements :

	**DEFAULT :**
<aside>
💡 Get global stats: cases, deaths, recovered, time last updated, and active cases. Data is updated every 10 minutes

</aside>

|Host Name|Endpoint|Useful|Reason|
|---|---|---|---|
|https://corona.lmao.ninja/|/v2/all|No|It is too general for what we need sinse it gives us the total updates in the world when we want much more granular details than that|
|https://corona.lmao.ninja/|/v2/states|No|Can only show today and/or yesterday's data and only for US states|
|https://corona.lmao.ninja/|/v2/states/{states}|No|same as the one before but one can specify the desired states to query|

**Data from Worldometers :**
<aside>
💡 Can be used to get details for our geological dimension, [and also new/Actual data for all countries]

</aside>

|Host Name|Endpoint|Useful|Reason|
|---|---|---|---|
|https://corona.lmao.ninja/|/v2/continents|Yes|Get All Continents Totals for Actual and Yesterday Data has information that can be reconstructed from historical data but it can be used for the "continent/and its countries" information|
|https://corona.lmao.ninja/|/v2/continents/{query}|No|Get the same data from the /v2/continents endpoint but filter down to a specific continent.|
|https://corona.lmao.ninja/|/v2/countries|Yes|Contains extra information for each country like (latitude/longitude ISO codes and a link to the country flag)|
|https://corona.lmao.ninja/|/v2/countries/{query}|No|Get the same data from the /countries endpoint but filter down to a specific country.|
|https://corona.lmao.ninja/|/v2/countries/{country\|country\|...}|No|Get the same data from the /countries endpoint but filter down to multiple specific countries.|

**Data from Johns Hopkins University :**
<aside>
💡 Get time series info from the JHU CSSE Data Repository. Every date since 1/22/20 has an entry tracking deaths, cases, and recoveries for each country.

</aside>

|Host Name|Endpoint|Useful|Reason|
|---|---|---|---|
|https://corona.lmao.ninja/|/v2/jhucsse|No|Get now's/Actual confirmed cases deaths recovered and coordinates. for each country with the update date.|
|https://corona.lmao.ninja/|/v2/jhucsse/counties|No|US data only|
|https://corona.lmao.ninja/|/v2/jhucsse/counties/countyName|No|US data only|
|https://corona.lmao.ninja/|/v2/historical|Yes|Every date since 1/22/20 has an entry tracking deaths cases and recoveries for each country. Updated each day at 23:59 UTC. defaults to 30 days "?lastdays=all" can get us all the saved data|
|https://corona.lmao.ninja/|/v2/historical/usacounties|No|US data only|
|https://corona.lmao.ninja/|/v2/historical/usacounties/{state}|No|US data only|
|https://corona.lmao.ninja/|/v2/historical/{query}|No|too general can be calculated/rebuilt|
|https://corona.lmao.ninja/|"/v2/historical/{country\|country\|...}"|No|too general can be calculated/rebuilt|
|https://corona.lmao.ninja/|/v2/historical/{query}/{province}|No|too general can be calculated/rebuilt|
|https://corona.lmao.ninja/|"/v2/historical/{query}/{province\|province\|...}"|No|too general can be calculated/rebuilt|
|https://corona.lmao.ninja/|/v2/historical/all|No|Return time series data globally.|

**Data from New York Times :**
<aside>
💡 Return all NYT state data or individual state data if specified. Each entry returned represents data for a given day, only has data about US's states.

</aside>

|Host Name|Endpoint|Useful|Reason|
|---|---|---|---|
|https://corona.lmao.ninja/|/v2/nyt/states|No|
|https://corona.lmao.ninja/|/v2/nyt/counties|No|
|https://corona.lmao.ninja/|/v2/nyt/usa|No|

**Mobility data from Apple :**
<aside>
💡 Has new data : driving, transit, and walking data with an associated number of how many percentages it is up or down since January 13th, interesting but it s doesn't really help us achieve our goal stated in the beginning of the report

</aside>

|Host Name|Endpoint|Useful|Reason|
|---|---|---|---|
|https://corona.lmao.ninja/|/v2/apple/countries|No|Get list of supported countries for Apple mobility data|
|https://corona.lmao.ninja/|/v2/apple/countries/{country}|No|Get list of supported sub-regions for specific country in the Apple mobility data set|
|https://corona.lmao.ninja/|/v2/apple/countries/{country}/{sub-regions}|No|Each entry has driving transit and walking data with an associated number of how many percentages it is up or down since January 13th|

**Government specific data :**
<aside>
💡 Only supports the following countries : [  "Germany",  "Italy",  "Canada",  "India",  "Switzerland",  "Nigeria",  "New Zealand", "Vietnam",  "Austria",  "Colombia"], so pretty much unusable in our case

</aside>

|Host Name|Endpoint|Useful|Reason|
|---|---|---|---|
|https://corona.lmao.ninja/|/v2/gov/|No|Return a list of supported country names|
|https://corona.lmao.ninja/|/v2/gov/{country}|No|doesn't support morroco and the we already have an alternative for getting the other countries's data|

## STEP 3 - Modelling the Data warehouse

### 3.1. Data Dictionary

|Field Name|Data Type|Data format|Source|Description|
|---|---|---|---|---|
|continent|varchar||/v2/continents|the name of the continent|
|countries|varchar[ ]||/v2/continents|a list of varchar contries for each continent|
|countryInfo/iso2|varchar|"XX"|v2/countries|ISO country code in 2 chars|
|countryInfo/iso3|varchar|"XXX"|v2/countries|ISO country code in 3 chars|
|countryInfo/lat|float||v2/countries|Latitude of the country|
|countryInfo/long|float||v2/countries|longitude of the country|
|countryInfo/flag|varchar|"https://disease.sh/assets/img/flags/XXXX.png"|v2/countries|png image of the flag of the country|
|population|int||v2/countries|current population of the country|
|timeline|date|5/30/20|/v2/historical|date of the declared numbers|
|cases|int||/v2/historical|cumulative number of the cases for  a given date|
|deaths|int||/v2/historical|cumulative number of the deaths for a given date|
|recovered|int||/v2/historical|cumulative number of the recovered for a given date|

### 3.2. Defining the Object/Business Process

At the end of this project, we would like to be able to answer the following descriptive analysis questions :
- Be able to plot the development of both (new and cumulative) number of cases for each continent on a time line.
- Be able to see the state of Covid-19's development in Morocco and compare it with other countries.
- Check how a country's population impacts the spread of the virus
- and also if possible try and run predictive ML models on our data, that's why i m keeping the latitude and longitude of countries, since it might play a role here.

### 3.3. Identifying the Grain level

To Be able to answer the questions stated above, we would need at least a level of granularity that is at the countries level, since some countries' data is separated into provinces, we'll have to aggregate it before Loading it into our Data Warehouse, and measurements should be at the granularity level of the "day" level.

### 3.4. Identifying the dimensions

<aside>
💡 How do we describe the data that is being measured ⁉️

</aside>

By trying to answer this question, while taking into consideration our objectives and grain level, we can come to the conclusion that there are two dimensions for our DW :
1. Geographical dimension with the following attributes :
    - continent
    - country
    - iso_code_2
    - iso_code_3
    - Latitude
    - Longitude
    - flag
2. Temporal dimension with the following attributes :
    - date
    - year
    - month
    - month_name
    - season_name
    - day
    - day_name

### 3.5. Identifying the facts

<aside>
💡 What is the process measuring ⁉️
</aside>

By trying to answer this question, we can come to the conclusion that we only need one fact table :

- Reports Fact table with the following attributes, for each contery and day of the year :
    - new cases
    - cumulative cases
    - new deaths
    - cumulative deaths
    - new recovered
    - cumulative recovered
    - population

#### 3.5.1. Star schema

![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled.png)

#### 3.5.2. Snowflake schema

![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%201.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%201.png)

## STEP 4 - Implementing a modern Data warehouse  :

Since at this day and age we have the hardware to support heavy calculation, we are evolving more and more in our data analysis from descriptive to include diagnostic , predictive, cognitive and prescriptive analysis. And with that the Data Warehouse is evolving too into a more modern version of itself, that is on the cloud, which comes with all of the cloud benefits that we've become familiar with, like its elastic architecture since an enterprise can scale it up and down with a click of a button and more.

And since this is supposed to be a Data Warehouse project, then why not try and get the most out of it by getting on the new wave of DW modernisation and learn some new concept that have a great chance to prove useful in the future.

In the rest of the chapter we are going to be implementing a modern Data warehouse on Microsoft Azure Synapse.

### 4.1. Preliminary Definitions

**Big data :**
- Extremely large data sets that may be analysed computationally to reveal patterns, trends, and associations, especially relating to human behavior and interactions.

**Data Lake :**
- Raw data repository whose purpose is not yet determined and is left in-place until needed. Highly accessible and quick to update.

**Batch processing :**
- The process of extracting source data, processing it in-place by a parallelized job, and loading it to an analytical data store (ETL).
    
    Batch Processing in Azure :
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%202.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%202.png)
    
- Analyse previously stored data.

**Stream processing :**
- Analyse incoming data in real time.

**Azure Data Lake Storage Gen2 / ADLS Gen2 :**
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%203.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%203.png)

- Data Lake Storage Gen2 makes Azure Storage the foundation for building enterprise data lakes on Azure. Designed from the start to service multiple petabytes of information while sustaining hundreds of gigabits of throughput, Data Lake Storage Gen2 allows you to easily manage massive amounts of data.
- ADLS Gen2 is the result of converging the capabilities of two existing storage services, **Azure Blob storage** and **Azure Data Lake Storage Gen1**.
- Has features from Azure **Data Lake Storage Gen1**, such as **file system semantics**, **directory**, and **file level security** and **scale.**
- Has capabilities from **Azure Blob storage**. such as **low-cost**, **tiered storage**, high availability, **disaster recovery.**

**Hadoop :**

- Apache Hadoop is a collection of open-source software utilities that facilitate using a network of many computers to solve problems involving massive amounts of data and computation.
- It provides a software framework for distributed storage and processing of big data using the MapReduce programming model.

**Apache Spark :**

- Open-source distributed cluster computing framework
- Successor to Apache Hadoop and its algorithm MapReduce
- In-memory processing as opposed to disk-based processing, which means it s faster than Hadoop.

**Databricks :**

- Company founded by the original creators of Apache Spark
- A cloud hosted Apache Spark platform
- Operates very much like  IPython-style notebook (Jupiter)

**PolyBase :**

- A very performant way to load - external - data from Azure Storage to Azure Synapse.
- PolyBase enables the same query to also join the data from Hadoop and SQL Server.
- In SQL Server, an external table or external data source provides the connection to Hadoop.
- PolyBase pushes some computations to the Hadoop node to optimize the overall query.

### 4.2. Introducing MICROSOFT AZURE Synapse Analytics

#### 4.2.1. Introduction

Today enterprise analytics requires operating at massive scale on any kind of data, whether raw, refined, or highly curated. In the past, building these kinds of analytics solutions required enterprises to stitch together big data and data warehousing technologies such as Spark and SQL. Next, they'd need to integrate them into rich data pipelines that work across data in relational stores and data lakes. Solutions like this are difficult to build, configure, secure, and maintain, which delays the swift extraction of intelligent insight. 

Synapse Analytics (formally knows as SQL Data Warehouse) is a very powerful engine that allows us to analyse big data. A simplified illustrating picture of what it look like would be as follows : 

- **Azure Data Lake Storage** : it Lets us store a lot of different data types inside of one location/Lake.
- **Azure SQL Data Warehouse** : A cloud-based enterprise data warehouse (EDW) that uses massively parallel processing (MPP) to run complex queries across petabytes of data quickly.
- **Azure Analytics**

---

**Illustrating picture of Azure Synapse:**
![Illustrating picture of Azure Synapse](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%204.png)

#### 4.2.2. Benefits

- Limitless scale : (at a Petabyte level and a world wide infrastructure)
- Useful Insights : (insights from PowerBI and A.I.)
- Unified Experience : (End-to-End analytic solution within the same interface)
- Code-free Ability : (Even tho one can still use it, it still allow us to focus one the data)
- Data Security : (always on data encryption, and row level security)
- Enterprise Data Warehousing

### 4.3. MICROSOFT AZURE Synapse Analytics (workspaces)

#### 4.3.1. Introduction

Azure Synapse is an integrated analytics service that accelerates time to insight from all data at any scale, across data warehouses and big data analytics systems. It brings together the best of the SQL technologies used in enterprise data warehousing, Spark technologies used in big data analytics, and Pipelines to orchestrate activities and data movement.

And interestingly it comes with a "Preview" web-native Studio user experience that provides a single experience and model for management, monitoring, coding, and security.

#### 4.3.2. Set Up

We start off by searching for and selecting the "Azure Synapse Analytics (workspaces preview)" service, then "Add" a new workspace by filling out the necessary details, at the summary it is stated that we have "SQL On-demand" Created automatically, which "as stated" costs "5.00 USD/TB".

After the creation :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%205.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%205.png)

And with that we have our service created, a visit to the workspaces look like this : 
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%206.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%206.png)

And by launching the "Synapse Studio" we get greeted by the following interface :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%207.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%207.png)

#### 4.3.3. Introducing Azure Data Factory

A cloud-based data integration service that allows you to orchestrate and automate data mouvement and data transformation. A process to this exists and it is called the data factory process, and it has 4 main components.

1. **Connect and collect data:**
    That is connecting to the data stores, and collecting that data, and at this step we would be doing the following :
    - Define a dataset that lives either in a "Store" or a "compute ressource", these two are being called using a "**linked service".**
    - A "**Linked service" is** an azure data factory object that allows you to connect either to "Data Lake Stores" or Compute ressources "Data bricks"
    - The dataset feeds data into an "activity" that performs action on the data.
    - Usually we have multiple activities collectively grouped into a "Pipeline"
    - Pipeline is also the data factory object used to "schedule or trigger" the excecution to get the mouvement of the data through the dataset
    - Dynamic capabilities can be added using "paramaters"
    - Integration runtime defines the envirement for moving from cloud to cloud
    
    The following diagram shows the relationships among pipeline, activity, dataset, and linked service in Data Factory: ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%208.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%208.png)
    
2. **Transform and Enrich**:
    This is the part were we can perform Data cleaning, transformation, aggregations, etc, build data flows... .
    We have multiple methods for transforming in Azure Data Factory, and we can do that either by: 
    - Calling Compute resources : like (AzureHD insight, SQL Server, ...)
    - SSIS packages : which we used previously in the TP.
    - Mapping Data Flow  : Code free data transformation
3. **Publish:**
    As in publishing into Synapse Analytics that can act as a serving layer for concurrent query access that can go up to 10 000 concurrent connections instead on hundreds for Microsoft SQL Server.
4. **Monitor:**
    Monitoring and handling errors and outputting them into quarantine areas to do human checks on them.

### 4.5. Modern Data Warehouse Architectures

With the new cloud services and the need for better DW solutions, comes a change in the DW architecture too for example an usual DW architecture would look like this : 
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%209.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%209.png)

We get a cloud solution that looks like this :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2010.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2010.png)

And here are two reference architectures for Modern data warehouses :

1. This one loads raw data to the and SQL server (SQL pool) then transforms the data there (ELT) :
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2011.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2011.png)
    
2. This one has an Azure Databrick and an intermediate for processing the data before loading it into SQL SERVER (SQL pool).
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2012.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2012.png)
    

### 4.6. Ingesting Data with Azure Data Factory

In this part i will be using Azure Data Factory (ADF)'s "**Copy Activity process**" to collect the API data and store it into the Data Lake Store as ".json" files, this part plays the same role as the "Extract" part is the ETL process.
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2013.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2013.png)

But first, we must check the integrity of the data.

#### 4.6.1. Verifying the **Data's Integrity**

Data Integrity is an umbrella term that refers to the consistency, accuracy, and correctness of data stored in a database. In the following I'll be checking the integrity of the data queried Json objects from 3 different "End points" of  the used API.

The following table shows the lines in which there are differences between the 3 files. Some of the following mappings are simple and only shows the different names used to describe the same Country, but some are more complicated and politically based, for example some of these countries/provinces are considered independent Islands but still follow a main country politically, and in the following I'll be aggregating them into the main country :

|/v2/continents|/v2/countries|/v2/historical|Comment|
|---|---|---|---|
|<span style="color:green;">Palestine</span>|<span style="color:green;">Palestine</span>|<span style="color:red;">West Bank and Gaza</span>||
|<span style="color:red;">Turks and Caicos Islands</span>|<span style="color:red;">Turks and Caicos Islands</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Montserrat</span>|<span style="color:red;">Montserrat</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Isle of Man</span>|<span style="color:red;">Isle of Man</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Gibraltar</span>|<span style="color:red;">Gibraltar</span>|<span style="color:green;">UK</span>||
|<span style="color:red;">Falkland Islands (Malvinas)</span>|<span style="color:red;">Falkland Islands (Malvinas)</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Channel Islands</span>|<span style="color:red;">Channel Islands</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Cayman Islands</span>|<span style="color:red;">Cayman Islands</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">British Virgin Islands</span>|<span style="color:red;">British Virgin Islands</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Bermuda</span>|<span style="color:red;">Bermuda</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Anguilla</span>|<span style="color:red;">Anguilla</span>|<span style="color:green;">UK</span>|Sovereign state: United Kingdom|
|<span style="color:red;">Sint Maarten</span>|<span style="color:red;">Sint Maarten</span>|<span style="color:green;">Netherlands</span>|Sovereign state: Kingdom of the Netherlands|
|<span style="color:red;">Curaçao</span>|<span style="color:red;">Curaçao</span>|<span style="color:green;">Netherlands</span>|It became an autonomous country within the Kingdom of the Netherlands in 2010|
|<span style="color:red;">Caribbean Netherlands</span>|<span style="color:red;">Caribbean Netherlands</span>|<span style="color:green;">Netherlands</span>|The Caribbean Netherlands are the three special municipalities of the Netherlands that are located in the Caribbean Sea.|
|<span style="color:red;">Aruba</span>|<span style="color:red;">Aruba</span>|<span style="color:green;">Netherlands</span>|Sovereign state: Kingdom of the Netherlands|
|<span style="color:red;">—</span>|<span style="color:red;">MS Zaandam</span>|<span style="color:red;">MS Zaandam</span>|a cruise ship owned and operated by Holland America Line named for the city of Zaandam|
|<span style="color:green;">Serbbia</span>|<span style="color:green;">Serbbia</span>|<span style="color:red;">Kosovo</span>|self-declared independent country in the Balkans region of Europe. Although the United States and most members of the European Union (EU) recognized Kosovo's declaration of independence from Serbia in 2008 Serbia, Russia and a significant number of other countries—including several EU members—did not.|
|<span style="color:red;">Holy See (Vatican City State)</span>|<span style="color:red;">Holy See (Vatican City State)</span>|<span style="color:green;">Holy See</span>||
|<span style="color:red;">Saint Pierre Miquelon</span>|<span style="color:red;">Saint Pierre Miquelon</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">St. Barth</span>|<span style="color:red;">St. Barth</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">New Caledonia</span>|<span style="color:red;">New Caledonia</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">Réunion</span>|<span style="color:red;">Réunion</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">Saint Martin</span>|<span style="color:red;">Saint Martin</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">Mayotte</span>|<span style="color:red;">Mayotte</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">Martinique</span>|<span style="color:red;">Martinique</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">French Guiana</span>|<span style="color:red;">French Guiana</span>|<span style="color:green;">France</span>|it is technically part of France|
|<span style="color:red;">Guadeloupe</span>|<span style="color:red;">Guadeloupe</span>|<span style="color:green;">France</span>||
|<span style="color:red;">French Polynesia</span>|<span style="color:red;">French Polynesia</span>|<span style="color:green;">France</span>|Sovereign state: France|
|<span style="color:red;">—</span>|<span style="color:red;">Diamond Princess</span>|<span style="color:red;">Diamond Princess</span>|British-registered cruise ship owned and operated by Princess Cruises.|
|<span style="color:red;">Faroe Islands</span>|<span style="color:red;">Faroe Islands</span>|<span style="color:green;">Denmark</span>||
|<span style="color:red;">Macao</span>|<span style="color:red;">Macao</span>|<span style="color:green;">China</span>|Macau is an autonomous region on the south coast of China|
|<span style="color:red;">Hong Kong</span>|<span style="color:red;">Hong Kong</span>|<span style="color:green;">China</span>||
|<span style="color:green;">Myanmar</span>|<span style="color:green;">Myanmar</span>|<span style="color:red;">Burma</span>|formerly Burma|

**<span style="color:red;">RED</span>** : TO BE DROPPED 
**<span style="color:green;">GREEN</span>** : TO BE KEPT

#### 4.6.2. Adding Linked services

Linked services are much like connection strings, which define the connection information needed for Azure Synapse Analytics to connect to external resources, and we'll be creating them through the "Manage" space.

- We define a HttpCoronaServer, in which we define the main URL part, and the type of the request that we going to be using
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2014.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2014.png)

- A data link we can use to connect to the Data Late Storage Gen2 that is created by default while we were creating the Azure Synapse workspace.
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2015.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2015.png)

#### 4.6.3. Creating the PipeLines :

We go to the orchestration space and create two pipelines : 
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2016.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2016.png)

- **INIT_PIPE** : this pipeline should do the get the data from the API to the DATA Lake, then perform transformations on it, and in the end apply the changes to the Data warehouse (SQL pool). It is supposed to only run once to Initialize our Data warehouse.
- **UPDATE_PIPE** : this pipe should be scheduled to run each day, to update the DATA Warehouse with the new reported cases .. and the new country's population.

#### 4.6.4. Ingesting the API's Data

We ll be adding Copy Activities to our two main pipelines. Each Copy Activity process defines a Dataset source, that gets its Json formatted Data from : 
- the Http Server Linked Service defined previously + the relative URL
which adds up to the URL, that specified the location of the JSON object.

- Ingestion for INIT_PIPE :
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2017.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2017.png)
    
- Ingestion for UPDATE_PIPE :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2018.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2018.png)

The result outcome of running the pipelines creates these json file in our ADLS's filesystem, I have separated them into two folder, 

- **INIT :** where initialization files go
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2019.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2019.png)
    
- **UPDATE :**  where update files go
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2020.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2020.png)
    

### 4.7. Transforming Data with Azure Data Factory

there are multiple ways for transforming Data in Azure Data Factory such as : 
- Calling Compute resources : like (AzureHD insight, SQL Server, ...)
- SSIS packages : which we used previously in the TP.
- Mapping Data Flow  : Code free data transformation

In the following I'll be implementing a "Mapping Data Flow" solution to transform my json data into csv files stored in a Azure Data Lake Gen2 ADL, so as not to get the report to long I m going to skip some explanations, and get right to the point where it works :
- The init pipeline looks like this :    
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2021.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2021.png)
    
with the data flow activity looking like this:     
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2022.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2022.png)
    
What it does is, it takes the json files from the 3 sources, apply the necessary changes to flatten them and make them look like this : 
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2023.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2023.png)
    
- As for the UPDATE pipeline :
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2024.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2024.png)
    
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2025.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2025.png)
    
    ![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2026.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2026.png)
    

And then we schedule a trigger for this pipeline to update the file each and every day :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2027.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2027.png)

At the end of this process we get two .csv files stored into our Azure Data Lake Storage as follows :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2028.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2028.png)

### 4.8. Loading The data into the SQL pool

I connected into the DW using Microsoft SQL server Management Studio : 
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2029.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2029.png)

ran scripts that creates some staging tables : 
```sql
IF OBJECT_ID('dbo.StagingTable', 'U') IS NOT NULL
    DROP TABLE dbo.StagingTable
GO

CREATE TABLE dbo.StagingTable
(
	country varchar(150) NOT NULL,
    "date" date NOT NULL,
    "day" int NOT NULL,
	day_of_week int NOT NULL,
	"month" int NOT NULL,
	"year" int NOT NULL,
	iso2 varchar(2) NOT NULL,
	iso3 varchar(3) NOT NULL,
	latitude float NOT NULL,
	Longitude float NOT NULL,
	flag varchar(100) NOT NULL,
    continent varchar(100) NOT NULL,
	population int NOT NULL,
	cumul_cases int NOT NULL,
	cumul_deaths int NOT NULL,
	cumul_recovered int NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
GO

IF OBJECT_ID('dbo.StagingTable_update', 'U') IS NOT NULL
    DROP TABLE dbo.StagingTable_update
GO

CREATE TABLE dbo.StagingTable_update
(
	country varchar(150) NOT NULL,
    "date" date NOT NULL,
    "day" int NOT NULL,
	day_of_week int NOT NULL,
	"month" int NOT NULL,
	"year" int NOT NULL,
	iso2 varchar(2) NOT NULL,
	iso3 varchar(3) NOT NULL,
	latitude float NOT NULL,
	Longitude float NOT NULL,
	flag varchar(100) NOT NULL,
	population int NOT NULL,
	cumul_cases int NOT NULL,
	cumul_deaths int NOT NULL,
	cumul_recovered int NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
)
GO
```

And then a added a copy data activity that would load the data from the prepared csv file into the staging table in the SQl pool:
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2030.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2030.png)

A simple select on the table shows the following : 
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2031.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2031.png)

And then followed it by doing the same thing for the UPDATE Pipeline:
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2032.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2032.png)

Then i created some views on the staging tables and made a copy from them onto the dim and fact tables, the reason i did this is because the fact table requires the PK values that are created when data is loaded onto the Dim tables .

After all is done , the two pipelines took like this :
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2033.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2033.png)

![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2034.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2034.png)

with the data loaded into the DW dimensions and fact tables.

### 4.9. Reporting

![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2035.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2035.png)
![Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2036.png](Data%20Warehouse%20Project%20-%20Azure%20ea4f3385291944e6b4572998926a0a58/Untitled%2036.png)

## Conclusion
In general, this has been a great learning experience in which I was able to learn so many new things.