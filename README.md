# Hurricanes and Power Outage Length

This is a class project for DSC80 at UCSD, which I completed in my third year of undergrad. 

## Introduction

The [dataset](https://www.sciencedirect.com/science/article/pii/S2352340918307182#bib6) this project is centered around contains information about power outages in the US. It includes information about the length of the power outage, the number of customers affected, the cause of the outage, and much more. <br>

The question I am aiming to answer in this project is "Do hurricanes cause longer power outages in comparison to other outages caused by severe weather?" Our daily lives are incredibly dependent on the steady availability of easily accessible power and answering this question allows both power companies and customers to prepare better for hurricane season. <br>

The original dataframe contains 1535 rows and 57 columns. Most of these columns will be scrapped in pursuit of my question. The most relevant remaining ones are `cause category`, `cause category detail`, and `outage duration`. <br>

- `cause category`: category of event that caused the power outage (ex. `severe weather')
- `cause category detail`: detailed breakdown of the cause of some power outage (ex. `heavy wind`)
- `outage duration`: the duration of the power outage, given in minutes

## Data Cleaning and EDA (Exploratory Data Analysis)

### Data Cleaning

Like most most datasets, this one has to be cleaned before it can be used. The original dataset existed as an Excel file and thus had to be converted to a .csv file in order to be read in through `pandas`. After reading in the now-csv file, I proceeded to clean the data with the following steps:

- Keeping only relevant columns. This was done by rereading in the .csv file except now with a limitation on the columns. I chose to reread the file as opposed to dropping columns as there were more columns to be dropped than kept. 
- Changing all the column names to lower case. The original dataset had all the column names in all upper case letters, along with periods in place of spaces. I replaced all the periods with white spaces and changed all the lettering to lower case and also made edits for special cases (eg `U.S._STATE` to `us state`)
- Typecasted all numerical information to float
- Combined the columns containing information about outage start time and date into one column and similarly for the restoration time. I also added a column containing the total outage time in days despite there already being an `outage duration` column. The column I added has time in days/minutes/hours format, whereas `outage duration` is given in minutes. My added column is more intuitive to understand (ex. 1 day vs 1440 minutes), but the `outage duration` column is easier to perform operations on.
- Converted the `outage duration` column to hours rather than minutes
- Dropped the now un-needed columns containing information about outage start time, outage start date, outage restoration time, and outage restoration date.

The dataset is now easier to read and work with, having only 17 columns as opposed to 57. I continue to check for missing values (-99, -, etc.) and find that there are none. There are many `NaN` values, but I was unable to replace them with 0 as 0 may also have significant context in this dataset. <br>

With this now complete, the dataset is cleaned and ready for EDA. There will be additional edits made in later sections, but this is the majority of the cleaning that will take place. Below are the first 5 rows of the cleaned dataframe. 

|    | us state   | postal code   | nerc region   | climate region     |   anomaly level | climate category   | cause category     | cause category detail   |   hurricane names |   outage duration |   demand loss mw |   customers affected |   population | start time          | restoration time    | total time      |
|---:|:-----------|:--------------|:--------------|:-------------------|----------------:|:-------------------|:-------------------|:------------------------|------------------:|------------------:|-----------------:|---------------------:|-------------:|:--------------------|:--------------------|:----------------|
|  0 | Minnesota  | MN            | MRO           | East North Central |            -0.3 | normal             | severe weather     | nan                     |               nan |             51    |              nan |                70000 |  5.34812e+06 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00 | 2 days 03:00:00 |
|  1 | Minnesota  | MN            | MRO           | East North Central |            -0.1 | normal             | intentional attack | vandalism               |               nan |              0.02 |              nan |                  nan |  5.45712e+06 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00 | 0 days 00:01:00 |
|  2 | Minnesota  | MN            | MRO           | East North Central |            -1.5 | cold               | severe weather     | heavy wind              |               nan |             50    |              nan |                70000 |  5.3109e+06  | 2010-10-26 20:00:00 | 2010-10-28 22:00:00 | 2 days 02:00:00 |
|  3 | Minnesota  | MN            | MRO           | East North Central |            -0.1 | normal             | severe weather     | thunderstorm            |               nan |             42.5  |              nan |                68200 |  5.38044e+06 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00 | 1 days 18:30:00 |
|  4 | Minnesota  | MN            | MRO           | East North Central |             1.2 | warm               | severe weather     | nan                     |               nan |             29    |              250 |               250000 |  5.48959e+06 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00 | 1 days 05:00:00 |

### EDA

The following section checks for correlations and potential relationships between variables. What causes the longest power outages? Are outage durations associated with population counts? In this section I group and aggregate this dataframe by various means and along various columns. Some interesting results will be reported below. <br>

I first want to see what the most common causes of power outages are. I decided to group by the `cause category detail` column in order to get a more precise idea of what the causes would be. 

| cause category detail     |   us state |   postal code |   nerc region |   climate region |   anomaly level |   climate category |   cause category |   hurricane names |   outage duration |   demand loss mw |   customers affected |   population |   start time |   restoration time |   total time |   missing |
|:--------------------------|-----------:|--------------:|--------------:|-----------------:|----------------:|-------------------:|-----------------:|------------------:|------------------:|-----------------:|---------------------:|-------------:|-------------:|-------------------:|-------------:|----------:|
| vandalism                 |        335 |           335 |           335 |              335 |             335 |                335 |              335 |                 0 |               325 |              146 |                  158 |          335 |          335 |                325 |          325 |       335 |
| thunderstorm              |        178 |           178 |           178 |              177 |             176 |                176 |              178 |                 0 |               176 |               91 |                  173 |          178 |          176 |                176 |          176 |       178 |
| winter storm              |        101 |           101 |           101 |              101 |             100 |                100 |              101 |                 0 |               100 |               66 |                   98 |          101 |          100 |                100 |          100 |       101 |
| hurricanes                |         74 |            74 |            74 |               74 |              74 |                 74 |               74 |                72 |                73 |               41 |                   73 |           74 |           74 |                 73 |           73 |        74 |
| heavy wind                |         61 |            61 |            61 |               61 |              61 |                 61 |               61 |                 0 |                60 |               44 |                   61 |           61 |           61 |                 60 |           60 |        61 |
| storm                     |         41 |            41 |            41 |               41 |              41 |                 41 |               41 |                 0 |                41 |               22 |                   41 |           41 |           41 |                 41 |           41 |        41 |
| sabotage                  |         32 |            32 |            32 |               32 |              32 |                 32 |               32 |                 0 |                29 |               24 |                   24 |           32 |           32 |                 29 |           29 |        32 |
| wildfire                  |         26 |            26 |            26 |               26 |              25 |                 25 |               26 |                 0 |                23 |               20 |                   18 |           26 |           25 |                 23 |           23 |        26 |
| winter                    |         23 |            23 |            23 |               23 |              23 |                 23 |               23 |                 0 |                23 |                6 |                    6 |           23 |           23 |                 23 |           23 |        23 |
| transmission interruption |         19 |            19 |            19 |               19 |              19 |                 19 |               19 |                 0 |                19 |               15 |                   17 |           19 |           19 |                 19 |           19 |        19 |

We can also plot these causes as a histogram. 

<iframe src = "assets/top10.html" width=800 height=600 frameBorder=0></iframe>

Here we see that the most common cause of power outages is actually vandalism and not a naturally occurring phenomena. This was unexpected! Severe weather (thunderstorm, winter storm, hurricanes, etc) do make up a large chunk, however. If I had plotted by `cause category` instead of `cause category detail`, it's likely that `severe weather` would have been the most common cause of power outages. <br>

Some other results of the EDA I did can be seen in the following graphs. 

<iframe src = "assets/severe weather.html" width=800 height=600 frameBorder=0></iframe>

<br>

<iframe src = "assets/hurricane.html" width=800 height=600 frameBorder=0></iframe>

These two boxplots display the range of power outage durations for power outages caused by severe weather (hurricanes included) and power outages caused by hurricanes. This tells me that my initial questions asking whether or not hurricanes cause longer power outages might be onto something. The majority of hurricane caused outages seem to lie in the 25-200 hours range, while the bulk of those caused by severe weather like in the 15-80 hour range. 






