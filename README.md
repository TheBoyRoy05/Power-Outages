# Predicting Power Outage Severities
## By Issac Roy

---
## Introduction

Before I begin, I'd like to point out that I had not one, but two power outages at home while working on this. Ironic, isn't it? Well, I guess that just means more data ¯\\_(ツ)_/¯. It did make me realize how frustrating it is not know when the power would come back, so I decided to use the data to predict the durations of outages.

The data is from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure (LASCI). Let's read in the data to take a look at what we're working with. I'll display the first 5 rows and first 5 columns to see what we need to clean up.

```py
raw = pd.read_excel(Path("data") / "outage.xlsx")
print(raw.head().to_markdown(index=False))
```

| Major power outage events in the continental U.S.                                                           | Unnamed: 1   | Unnamed: 2   | Unnamed: 3   | Unnamed: 4   | Unnamed: 5   | Unnamed: 6   | Unnamed: 7     | Unnamed: 8    | Unnamed: 9       | Unnamed: 10       | Unnamed: 11       | Unnamed: 12             | Unnamed: 13             | Unnamed: 14    | Unnamed: 15           | Unnamed: 16     | Unnamed: 17     | Unnamed: 18    | Unnamed: 19        | Unnamed: 20   | Unnamed: 21   | Unnamed: 22   | Unnamed: 23   | Unnamed: 24   | Unnamed: 25   | Unnamed: 26   | Unnamed: 27   | Unnamed: 28   | Unnamed: 29   | Unnamed: 30   | Unnamed: 31   | Unnamed: 32   | Unnamed: 33   | Unnamed: 34     | Unnamed: 35   | Unnamed: 36   | Unnamed: 37   | Unnamed: 38      | Unnamed: 39    | Unnamed: 40    | Unnamed: 41       | Unnamed: 42   | Unnamed: 43   | Unnamed: 44   | Unnamed: 45   | Unnamed: 46   | Unnamed: 47   | Unnamed: 48   | Unnamed: 49   | Unnamed: 50   | Unnamed: 51   | Unnamed: 52   | Unnamed: 53   | Unnamed: 54   | Unnamed: 55   | Unnamed: 56      |
|:------------------------------------------------------------------------------------------------------------|:-------------|:-------------|:-------------|:-------------|:-------------|:-------------|:---------------|:--------------|:-----------------|:------------------|:------------------|:------------------------|:------------------------|:---------------|:----------------------|:----------------|:----------------|:---------------|:-------------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:----------------|:--------------|:--------------|:--------------|:-----------------|:---------------|:---------------|:------------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:--------------|:-----------------|
| Time period: January 2000 - July 2016                                                                       | nan          | nan          | nan          | nan          | nan          | nan          | nan            | nan           | nan              | nan               | nan               | nan                     | nan                     | nan            | nan                   | nan             | nan             | nan            | nan                | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan             | nan           | nan           | nan           | nan              | nan            | nan            | nan               | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan              |
| Regions affected: Outages reported in this data file affected a single U.S. state at the time of occurrence | nan          | nan          | nan          | nan          | nan          | nan          | nan            | nan           | nan              | nan               | nan               | nan                     | nan                     | nan            | nan                   | nan             | nan             | nan            | nan                | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan             | nan           | nan           | nan           | nan              | nan            | nan            | nan               | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan              |
| nan                                                                                                         | nan          | nan          | nan          | nan          | nan          | nan          | nan            | nan           | nan              | nan               | nan               | nan                     | nan                     | nan            | nan                   | nan             | nan             | nan            | nan                | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan             | nan           | nan           | nan           | nan              | nan            | nan            | nan               | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan              |
| nan                                                                                                         | nan          | nan          | nan          | nan          | nan          | nan          | nan            | nan           | nan              | nan               | nan               | nan                     | nan                     | nan            | nan                   | nan             | nan             | nan            | nan                | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan             | nan           | nan           | nan           | nan              | nan            | nan            | nan               | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan           | nan              |
| variables                                                                                                   | OBS          | YEAR         | MONTH        | U.S._STATE   | POSTAL.CODE  | NERC.REGION  | CLIMATE.REGION | ANOMALY.LEVEL | CLIMATE.CATEGORY | OUTAGE.START.DATE | OUTAGE.START.TIME | OUTAGE.RESTORATION.DATE | OUTAGE.RESTORATION.TIME | CAUSE.CATEGORY | CAUSE.CATEGORY.DETAIL | HURRICANE.NAMES | OUTAGE.DURATION | DEMAND.LOSS.MW | CUSTOMERS.AFFECTED | RES.PRICE     | COM.PRICE     | IND.PRICE     | TOTAL.PRICE   | RES.SALES     | COM.SALES     | IND.SALES     | TOTAL.SALES   | RES.PERCEN    | COM.PERCEN    | IND.PERCEN    | RES.CUSTOMERS | COM.CUSTOMERS | IND.CUSTOMERS | TOTAL.CUSTOMERS | RES.CUST.PCT  | COM.CUST.PCT  | IND.CUST.PCT  | PC.REALGSP.STATE | PC.REALGSP.USA | PC.REALGSP.REL | PC.REALGSP.CHANGE | UTIL.REALGSP  | TOTAL.REALGSP | UTIL.CONTRI   | PI.UTIL.OFUSA | POPULATION    | POPPCT_URBAN  | POPPCT_UC     | POPDEN_URBAN  | POPDEN_UC     | POPDEN_RURAL  | AREAPCT_URBAN | AREAPCT_UC    | PCT_LAND      | PCT_WATER_TOT | PCT_WATER_INLAND |

From what we can see, the data is pretty messy and is going to require some cleaning before we can use it. In terms of content, the dataframe has 1534 rows, not counting the blank header rows, and 57 columns, a few of which are unnecessary. 

For this project, I'm likely going to be using the OUTAGE.START.DATE and OUTAGE.START.TIME columns to get the date and time of the outage as well as POSTAL.CODE (the state abbreviation) to get the location. I'll also be looking at the and CAUSE.CATEGORY and CAUSE.CATEGORY.DETAIL column to get the cause of the outage and I'll be looking at the other columns to see if they correlate with the severity of the outage.

In this dataframe, severity is described by three columns: DEMAND.LOSS.MW, CUSTOMERS.AFFECTED, and OUTAGE.DURATION. I'm going to be using OUTAGE.DURATION as the primary severity measure here.

### Brainstorming

Here's a few of the things that I wanted to explore in this dataset:

- distribution of outages per state
- distribution of outages over time
- correlations between the columns
- patterns which I can predict with an ML model

Here's a few questions that I had when I began:

- Do power outages have any temporal patterns, i.e. could I predict when an outage would occur?
- Do power outages have any spacial patterns, i.e. could I predict where an outage would occur?
- Could I predict the severity of a power outage given it's location and other initial data?

For this project, I'm going to address the last question: **predicting the severity of a power outage**. Let's get started!

---
## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

Let's go ahead and clean the data by dropping unnecessary rows and columns and resetting the column names to be the fifth row. This gets us the following:

```py
df = (
    raw[6:]
    .reset_index(drop=True)
    .rename(columns=raw.iloc[4])
    .drop(["variables", "OBS"], axis=1)
)
print(df.head().to_markdown(index=False))
```

|   YEAR |   MONTH | U.S._STATE   | POSTAL.CODE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | OUTAGE.START.DATE   | OUTAGE.START.TIME   | OUTAGE.RESTORATION.DATE   | OUTAGE.RESTORATION.TIME   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   HURRICANE.NAMES |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   RES.PRICE |   COM.PRICE |   IND.PRICE |   TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES |   TOTAL.SALES |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   RES.CUSTOMERS |   COM.CUSTOMERS |   IND.CUSTOMERS |   TOTAL.CUSTOMERS |   RES.CUST.PCT |   COM.CUST.PCT |   IND.CUST.PCT |   PC.REALGSP.STATE |   PC.REALGSP.USA |   PC.REALGSP.REL |   PC.REALGSP.CHANGE |   UTIL.REALGSP |   TOTAL.REALGSP |   UTIL.CONTRI |   PI.UTIL.OFUSA |   POPULATION |   POPPCT_URBAN |   POPPCT_UC |   POPDEN_URBAN |   POPDEN_UC |   POPDEN_RURAL |   AREAPCT_URBAN |   AREAPCT_UC |   PCT_LAND |   PCT_WATER_TOT |   PCT_WATER_INLAND |
|-------:|--------:|:-------------|:--------------|:--------------|:-------------------|----------------:|:-------------------|:--------------------|:--------------------|:--------------------------|:--------------------------|:-------------------|:------------------------|------------------:|------------------:|-----------------:|---------------------:|------------:|------------:|------------:|--------------:|------------:|------------:|------------:|--------------:|-------------:|-------------:|-------------:|----------------:|----------------:|----------------:|------------------:|---------------:|---------------:|---------------:|-------------------:|-----------------:|-----------------:|--------------------:|---------------:|----------------:|--------------:|----------------:|-------------:|---------------:|------------:|---------------:|------------:|---------------:|----------------:|-------------:|-----------:|----------------:|-------------------:|
|   2011 |       7 | Minnesota    | MN            | MRO           | East North Central |            -0.3 | normal             | 2011-07-01 00:00:00 | 17:00:00            | 2011-07-03 00:00:00       | 20:00:00                  | severe weather     | nan                     |               nan |              3060 |              nan |                70000 |       11.6  |        9.18 |        6.81 |          9.28 |     2332915 |     2114774 |     2113291 |       6562520 |      35.5491 |      32.225  |      32.2024 |         2308736 |          276286 |           10673 |           2595696 |        88.9448 |        10.644  |       0.411181 |              51268 |            47586 |          1.07738 |                 1.6 |           4802 |          274182 |       1.75139 |             2.2 |      5348119 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2014 |       5 | Minnesota    | MN            | MRO           | East North Central |            -0.1 | normal             | 2014-05-11 00:00:00 | 18:38:00            | 2014-05-11 00:00:00       | 18:39:00                  | intentional attack | vandalism               |               nan |                 1 |              nan |                  nan |       12.12 |        9.71 |        6.49 |          9.28 |     1586986 |     1807756 |     1887927 |       5284231 |      30.0325 |      34.2104 |      35.7276 |         2345860 |          284978 |            9898 |           2640737 |        88.8335 |        10.7916 |       0.37482  |              53499 |            49091 |          1.08979 |                 1.9 |           5226 |          291955 |       1.79    |             2.2 |      5457125 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2010 |      10 | Minnesota    | MN            | MRO           | East North Central |            -1.5 | cold               | 2010-10-26 00:00:00 | 20:00:00            | 2010-10-28 00:00:00       | 22:00:00                  | severe weather     | heavy wind              |               nan |              3000 |              nan |                70000 |       10.87 |        8.19 |        6.07 |          8.15 |     1467293 |     1801683 |     1951295 |       5222116 |      28.0977 |      34.501  |      37.366  |         2300291 |          276463 |           10150 |           2586905 |        88.9206 |        10.687  |       0.392361 |              50447 |            47287 |          1.06683 |                 2.7 |           4571 |          267895 |       1.70627 |             2.1 |      5310903 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2012 |       6 | Minnesota    | MN            | MRO           | East North Central |            -0.1 | normal             | 2012-06-19 00:00:00 | 04:30:00            | 2012-06-20 00:00:00       | 23:00:00                  | severe weather     | thunderstorm            |               nan |              2550 |              nan |                68200 |       11.79 |        9.25 |        6.71 |          9.19 |     1851519 |     1941174 |     1993026 |       5787064 |      31.9941 |      33.5433 |      34.4393 |         2317336 |          278466 |           11010 |           2606813 |        88.8954 |        10.6822 |       0.422355 |              51598 |            48156 |          1.07148 |                 0.6 |           5364 |          277627 |       1.93209 |             2.2 |      5380443 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2015 |       7 | Minnesota    | MN            | MRO           | East North Central |             1.2 | warm               | 2015-07-18 00:00:00 | 02:00:00            | 2015-07-19 00:00:00       | 07:00:00                  | severe weather     | nan                     |               nan |              1740 |              250 |               250000 |       13.07 |       10.16 |        7.74 |         10.43 |     2028875 |     2161612 |     1777937 |       5970339 |      33.9826 |      36.2059 |      29.7795 |         2374674 |          289044 |            9812 |           2673531 |        88.8216 |        10.8113 |       0.367005 |              54431 |            49844 |          1.09203 |                 1.7 |           4873 |          292023 |       1.6687  |             2.2 |      5489594 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |

Let's also clean up the start and restoration times so that they're combined into one column and are of the the datetime type.

```py
print(cleaned[["OUTAGE.START", "OUTAGE.RESTORATION"]].head().to_markdown(index=False))
```

|   OUTAGE.START |   OUTAGE.RESTORATION |
|---------------:|---------------------:|
|    1.30954e+18 |          1.30972e+18 |
|    1.39983e+18 |          1.39983e+18 |
|    1.28812e+18 |          1.2883e+18  |
|    1.34008e+18 |          1.34023e+18 |
|    1.43718e+18 |          1.43729e+18 |

### Univariate Analysis

First of all, I would like to explore the distribution of power outages per state. We can do so by using the folium module to plot geographical data. In my opinion this is more interesting to look at than a bar chart. Let's also apply a log scale to the colors since state's with more people and infrastructure will tend to have more power outages. You can hover over each state to see the actual number of outages recorded in this dataset.

```py
plot_geo(
    cleaned.groupby("POSTAL.CODE").size(),
    legend_name="Number of Power Outages",
    scale_fn=np.log,
)
```

<iframe
  src="Assets/Number_of_Power_Outages.html"
  width="830"
  height="600"
  frameborder="0"
></iframe>

It seems that California has the most power outages in the dataset with Texas, Michigan, and Washington having about half as much. It also seems that the rural states in the middle of the country experience less power outages which makes sense because there's less people and infrastructure out there.

Next, I want to look at the distribution of outages over time. To do so, we can use a histogram to plot the number of outages every year as well as a line graph to plot the number of outages per month. This will give more resolution to the distribution of outages over time.

<iframe
  src="Assets/Power_Outages_over_Time.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

Doing this reveals that our data is only from 2000-2016. Interestingly, it seems that there were a lot of power outages recorded in 2011. This could be because there were simply a lot of power outages in that year or it could be other factors. For example, data could have been lost or not recorded for other years. Or perhaps more minor power outages were included in 2011 than other years. To see if this is a possibility, we can explore the number of major vs minor power outages over time in the bivariate analysis.

### Bivariate Analysis

Let's explore the number of major vs minor outages over time. To do so, I will define a major outage to be in the top 30th percentile of outages in terms of outage duration.

<iframe
  src="Assets/Severity_of_Power_Outages_over_Time.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

It seems that my hypothesis seems to be on the right track. Although the number of major outages did increase slightly from 2010 to 2011, it wasn't nearly as much as the increase in minor outages in the same time interval.

Another relationship that I would like to explore is the relationship between Demand Loss and Customers Affected during major power outages since it could tell us how strongly different measures of severities are correlated. Since I want to explore this relationship in the context of major outages, I'll only look at outages with more than 100 Customers Affected. Moreover, I'll use a log scale for both Demand Loss and Customers Affected since they scale up exponentially. Finally I'll draw the trendline on the graph as well.

<iframe
  src="Assets/Customers_Affected_vs_Demand_Loss.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

It looks like these two measures of severity are strongly correlated on log scales. As customers affected increases, demand loss tends to also increase. This makes sense since as customers affected increases, the power supply is more likely to be interrupted, which means that the demand loss is also more likely to be higher.

### Interesting Aggregates

One aggregate that I would like to explore is distibution of causes over time. To do so we can aggregate by the Cause Category and Year columms.

```py
num_outages_per_cause_over_time = pd.pivot_table(
    cleaned,
    index="CAUSE.CATEGORY",
    columns="YEAR",
    values="POSTAL.CODE",
    aggfunc="count",
    fill_value=0,
)

print(num_outages_per_cause_over_time.to_markdown(index=False))
```

| CAUSE.CATEGORY                |   2000 |   2001 |   2002 |   2003 |   2004 |   2005 |   2006 |   2007 |   2008 |   2009 |   2010 |   2011 |   2012 |   2013 |   2014 |   2015 |   2016 |
|:------------------------------|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|-------:|
| equipment failure             |      5 |      1 |      0 |      6 |      5 |      3 |      1 |      6 |      9 |     10 |      5 |      4 |      1 |      4 |      0 |      0 |      0 |
| fuel supply emergency         |      0 |      0 |      0 |      0 |      0 |      1 |      1 |      3 |      4 |      3 |      3 |      6 |      5 |      7 |     13 |      2 |      3 |
| intentional attack            |      2 |      0 |      1 |      2 |      0 |      0 |      0 |      0 |      0 |      0 |      0 |    121 |     89 |     80 |     47 |     43 |     33 |
| islanding                     |      0 |      0 |      0 |      0 |      0 |      0 |      0 |      1 |      6 |      4 |      9 |      3 |      4 |      7 |      1 |      9 |      2 |
| public appeal                 |      0 |      3 |      0 |      1 |      7 |      0 |      2 |      2 |      2 |     10 |     14 |     16 |      3 |      0 |      5 |      4 |      0 |
| severe weather                |     13 |      1 |     13 |     30 |     56 |     47 |     54 |     40 |     76 |     45 |     62 |    107 |     65 |     49 |     45 |     48 |     12 |
| system operability disruption |      6 |     10 |      3 |      7 |      3 |      4 |      9 |      4 |     14 |      6 |     13 |     12 |      7 |      6 |      1 |     13 |      9 |

It seems that the prevailing cause every year is severe weather except for 2011 where the majority of outages were caused by intentional attacks. Interestingly, it seems that the data only starts counting intentional attacks in 2011 and it's only been going down ever since.

Another interesting aggregate that we could explore is the mean number of customers affected over Climate Region and Month to see if perhaps some climates are affected more seasonally than others.

```py
mean_customers_affected_per_month_by_climate = pd.pivot_table(
    cleaned,
    index="CLIMATE.REGION",
    columns="MONTH",
    values="CUSTOMERS.AFFECTED",
    aggfunc=lambda x: np.mean(x) / 1000 if not x.empty else 0,
    fill_value=0,
).astype(int)

print(mean_customers_affected_per_month_by_climate.to_markdown())
```

| CLIMATE.REGION     |   1 |   2 |   3 |   4 |   5 |   6 |   7 |   8 |   9 |   10 |   11 |   12 |
|:-------------------|----:|----:|----:|----:|----:|----:|----:|----:|----:|-----:|-----:|-----:|
| Central            | 113 |  87 |  65 |  94 |  76 | 148 | 129 | 228 | 186 |  193 |   49 |   57 |
| East North Central |  72 | 182 |  93 | 116 |  97 | 147 | 125 | 243 | 104 |  129 |  148 |  130 |
| Northeast          |  53 | 112 | 131 |  58 |  29 | 132 |  87 | 328 | 100 |  147 |   42 |  108 |
| Northwest          | 170 |  43 |  17 |  56 |   0 |   5 |   0 | 164 |   0 |   53 |  104 |  129 |
| South              | 240 | 296 |  96 | 133 | 216 | 134 |  79 | 114 | 484 |  138 |   73 |  155 |
| Southeast          | 180 | 157 | 133 | 107 |  80 | 148 |  72 | 202 | 318 |  717 |   86 |   51 |
| Southwest          |   0 |  24 | 166 |   0 |  30 |  36 |  55 |   0 |   0 |   44 |    0 |  100 |
| West               | 538 | 131 | 118 |  64 | 418 |  19 | 123 | 148 | 308 |  148 |  194 |  298 |
| West North Central |   0 |   0 |   0 |   0 |   0 | 123 |   0 |   0 |   0 |   35 |    0 |   24 |

From this data, we can see that there are generally more customers affected in the summer months for regions such as Central and Northeast while other regions such as the West has more customers affected during winter months.

---
## Assessment of Missingness

First, let's take a look at the number of missing values in each column.

```py
missing = (
    cleaned.apply(lambda x: x.isna().sum())
    .sort_values(ascending=False)
    .rename("Number of Missing Values")
)

print(missing[missing > 0].to_markdown())
```

|                       |   Number of Missing Values |
|:----------------------|---------------------------:|
| HURRICANE.NAMES       |                       1462 |
| DEMAND.LOSS.MW        |                        705 |
| CAUSE.CATEGORY.DETAIL |                        471 |
| CUSTOMERS.AFFECTED    |                        443 |
| OUTAGE.DURATION       |                         58 |
| OUTAGE.RESTORATION    |                         58 |
| TOTAL.PRICE           |                         22 |
| COM.SALES             |                         22 |
| RES.SALES             |                         22 |
| RES.PRICE             |                         22 |
| IND.PRICE             |                         22 |
| COM.PRICE             |                         22 |
| IND.SALES             |                         22 |
| RES.PERCEN            |                         22 |
| COM.PERCEN            |                         22 |
| TOTAL.SALES           |                         22 |
| IND.PERCEN            |                         22 |
| POPDEN_UC             |                         10 |
| POPDEN_RURAL          |                         10 |
| ANOMALY.LEVEL         |                          9 |
| MONTH                 |                          9 |
| CLIMATE.CATEGORY      |                          9 |
| OUTAGE.START          |                          9 |
| CLIMATE.REGION        |                          6 |

### NMAR Analysis

It seems that Hurricane Names have the most missing values but this is Missing by Design since that column is only used when the cause is a Hurricane. I would also argue that none of the missing columns are NMAR (Not Missing at Random). The missingness of two of the top missing columns, Demand Loss and Customers Affected are likely related to Outage Duration (another measure of severity), which would most likely make it MAR. The missingness of Cause Category Detail likely depends on Cause Category, making it likely MAR. 

The rest of columns seem to have similar amounts of missing values. To see this more explicitly, we can plot the missingness of each column in the dataset to see if we can spot any trends in the missing values.

<iframe
  src="Assets/Missingness_in_the_Dataset.html"
  width="830"
  height="1000"
  frameborder="0"
></iframe>

Looking at the plot above, we can see that many missing values occur in groups of columns. This likely means that the missingness of these columns depends on when the data was collected, making them likely MAR as well on. Hence, it doesn't seem that any columns are NMAR in this dataset.

### Missingness Dependency

Two interesting columns to test missingness on are Demand Loss and Cause Details. For both of these, I'll test if they're MAR on the Cause Category. That is, does Cause Category affect whether Demand Loss or Cause Details are missing? I'll use TVD as a test statistic since I'm comparing distributions for a categorical variable and run a permutation test where I permute the Cause Categories.

I'll set up our first Hypothesis Test as such:

- \\(H_0\\): The missingness of Demand Loss is independent of Cause Category
- \\(H_a\\): The missingness of Demand Loss is dependent on Cause Category

```py
assess_missingness_on_cause("DEMAND.LOSS.MW")
```

<iframe
  src="Assets/DEMAND_LOSS_MW_Missingness.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

As you can see above, the resulting p-value was 0 and so we reject the Null at an p-value. As we can see from the bar chart above. The Demand Loss values are more likely to be missing if the Cause Category is severe weather or an intentional attack and less likely to be missing if the Cause was system operability disrution, equipment failure, or islanding.

I'll set up the next Hypothesis Test as such:

- \\(H_0\\): The missingness of Cause Category Detail is independent of Cause Category
- \\(H_a\\): The missingness of Cause Category Detail is dependent on Cause Category

```py
assess_missingness_on_cause("CAUSE.CATEGORY.DETAIL")
```

<iframe
  src="Assets/CAUSE_CATEGORY_DETAIL_Missingness.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

As you can see above, we again reject the Null for any p-value. Interestingly, the difference is much more dramatic here with it being much more likely for the detail to be missing if the cause was system operability disruption, public appeal, or islanding while it's much less likely to be missing if the cause was severe weather or intentional attack.

---
## Hypothesis Testing

For my regular Hypothesis Test, I'll be testing if the number of outages are dependent on the month to see if there are any seasonal trends. Said more formally, my null and alternative Hypotheses are:

- \\(H_0\\): The number of power outages that occur is independent of the month (i.e. there is a uniform distribution of outages over months)
- \\(H_a\\): The number of power outages that occur is dependent on the month (i.e. there isn't a uniform distribution of outages over months)

First, let's plot number of outages per calendar month.

<iframe
  src="Assets/Outages_per_Month.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

As you can see above, it seems that there are more outages during the summer and winter months, but we can't know for sure unless we run a Hypothesis Test. To run this test, I will be sampling from a uniform distribution and I'll using TVD again as my test statistic because we can treat months as categorical variables. I'll also be using a significance level of 0.01.

```py
tvd = lambda x, y: np.abs(x - y).sum() / 2
uniform = np.ones(12) / 12
month_props = cleaned.value_counts("MONTH", normalize=True).values
observed = tvd(month_props, uniform)

stats = np.array([])
for _ in range(1000):
    rand = np.random.choice(12, size=cleaned.shape[0], p=uniform)
    stat = pd.Series(rand).value_counts(normalize=True)
    stats = np.append(stats, tvd(stat, uniform))

pval = (stats >= observed).mean()
result = "Reject" if pval < 0.01 else "Fail to Reject"
print(f"p-value: {pval}\nHypothesis: {result}")
```
p-value: 0.0  
Hypothesis: Reject

As you can see above, we again reject the null for any p-value. It seems that there may be some seasonal trend in the number of power outages. From the bar chart above, it seems that there are more outages during the summer and winter months.

---
## Framing a Prediction Problem

The question that I'm going to choose to frame a prediction problem around is the severity of power outages. The first thing I would like to look at is patterns in temporal data to see if I could predict how severe an outage is given when it occurs. I looked at many different columns but none of them seem to have any solid temporal pattern from what I could tell. Here's one such example where I look at Demand Loss, Outage Duration, and Customers Affected (all of them on log scales) over time.

<iframe
  src="Assets/Monthy_Average_of_Outage_Metrics.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

As you can see, there is a correlation between these different measures of severity. However, there's not much of a temporal pattern here to build a prediction model from. Next, let's take a look at a correlation matrix to see if we could create a regression model to predict severity.

<iframe
  src="Assets/Correlation_Matrix.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

Although there do seem to be some correlation to these measures of severity, basically all of them are weak or don't help us much. Taking a look at the scale, the correlation coefficiants only range from -0.3 to 0.2, indicating weak correlations. Customers Affected does have a few correlations including Sales and Customers which makes sense, but those are also weak correlations with a correlation coefficiant of less than 0.2.

The columns with the strongest correlations are Year, Outage Start, and Outage Restoration to Log Outage Duration. However, these columns essentially have the same data, which we just explored with the time plot where we concluded that there wasn't much of a pattern to predict from, especially if I want to predict severities of future outages.

### Problem Identification

This means that we won't be able to do regression with much degree of accuracy since none of the quantative columns give us much to work with. Instead, I'll turn to classifying outages as High or Low Severity based on their duration. To do so, let's take a look at the distribution of Log Outage Duration and classify each data point whether they are above or below the mean. More specifically, an outage will be classified as High Severity if it's about 15 hours or longer.

<iframe
  src="Assets/Outage_Severity.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

From the above graph, we can see that the distribution is tri-modal with a spike at 0. This means that we have close to 100 outages that only lasted a minute. After doing some research, it seems that the majority of power outages are only seconds or minutes long. The other two modes of this distribution are centered around 5 hours and 36 hours. Perhaps this could be because if an outage can't be restored after a certain period of time and it's already late, it could be put off until the next morning since it won't affect many people in the middle of the night.

Next, I would like to look at quantative columns that I may be able to predict Outage Duration from. To do this, let's examine the columns with the highest correlations to Log Outage Duration. I'll discard Year, Outage Start, and Outage Restoration since they all essentially have the same data and also won't be very useful for predicting future outage severities.

```py
print(
    corr_matrix.loc["LOG_OUTAGE_DURATION"]
    .apply(abs)
    .sort_values(ascending=False)
    .drop(["YEAR", "OUTAGE.START", "OUTAGE.RESTORATION"])
    .rename("Correlation")
    .head()
    .to_markdown()
)
```

|                |   Correlation |
|:---------------|--------------:|
| PC.REALGSP.USA |      0.215041 |
| UTIL.CONTRI    |      0.200287 |
| PCT_LAND       |      0.159314 |
| PCT_WATER_TOT  |      0.159293 |
| RES.SALES      |      0.14143  |

```py
print(
    corr_matrix.loc["OUTAGE.DURATION"]
    .apply(abs)
    .sort_values(ascending=False)
    .drop(["YEAR", "OUTAGE.START", "OUTAGE.RESTORATION"])
    .rename("Correlation")
    .head()
    .to_markdown()
)
```

|                |   Correlation |
|:---------------|--------------:|
| PCT_LAND       |     0.121614  |
| PCT_WATER_TOT  |     0.121605  |
| UTIL.CONTRI    |     0.102768  |
| PC.REALGSP.USA |     0.070885  |
| START_HR       |     0.0708838 |

We could try using these columns, but they could end up hurting the model rather than helping since weak correlations could lead to overfit data. Even if there's not linear correlation to the other columns, we can still visually examine data for trends. To do so, I've created a function to plot a column and it's relationship to Outage Duration.

```py
plot_duration("START_HR", logify=False)
```

<iframe
  src="Assets/Median_Outage_Duration_by_START_HR.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

```py
plot_duration("START_DAYOFWEEK", logify=False)
```

<iframe
  src="Assets/Median_Outage_Duration_by_START_DAYOFWEEK.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

Doing so reveals some interesting patterns. Firstly, outages that start in the middle of the day tend to last much shorter than outages than begin in the evening and during the night. This is most likely because more effort and resources are put towards restoring an outage that starts in the middle of the day since that would hurt the economy the most because of lost productivity. 

Similarly, plotting the start day of week reveals that outages that start on the weekend tend to last longer. This is probably also due to the same reason. Outages need to restored faster on weekdays so as to not lose productive working hours.

As for my metric, I'll be using accuracy because it's a binary classification problem and it's the easiest to interpret for binary classification. However, I will be looking at precision and recall later to see how the model performs in terms of those metrics. Now, let's get started with our baseline model.

---
## Baseline Model

To create the baseline model, I'll be using the pattern that we noticed above. That is, Start Hour and Start Day of Week both influence how long the outage lasts. I'll also try the other numerical columns that were linearly correlated to outage duration as well. To create the pipeline, I'll be using a RandomForestClassifier on Start Hour and Day of Week while running GridSearchCV to fine-tune my hyperparemeters.

```py
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import GridSearchCV
from sklearn.metrics import make_scorer, accuracy_score

preprocessor = ColumnTransformer(
    [("numeric", "passthrough", ["START_HR", "START_DAYOFWEEK", "PC.REALGSP.USA"])],
    remainder="drop",
)

pipeline = Pipeline(
    [
        ("preprocessor", preprocessor),
        ("classifier", RandomForestClassifier(random_state=42)),
    ]
)

param_grid = {
    "classifier__max_depth": np.arange(1, 11),
    "classifier__n_estimators": np.arange(1, 51),
}

grid_search = GridSearchCV(
    pipeline,
    param_grid,
    scoring=make_scorer(accuracy_score),
    cv=5,
    n_jobs=-1,
    verbose=1,
)

dropped_na = severity.dropna(
    subset=["CLIMATE.REGION", "CAUSE.CATEGORY.DETAIL", "SEVERITY"]
)
X = dropped_na.drop("SEVERITY", axis=1)
y = dropped_na["SEVERITY"]

grid_search.fit(X, y)
plot_accuracy_surface(grid_search, "Baseline")
print(f"Best score: {grid_search.best_score_}\nBest params: {grid_search.best_params_}")
```

<iframe
  src="Assets/Grid_Search_Results_Baseline.html"
  width="830"
  height="700"
  frameborder="0"
></iframe>
Best score: 0.6735464315238358  
Best params: {'classifier__max_depth': 4, 'classifier__n_estimators': 9}

When trying out different models, I discovered that including the numerical column, PC.REALGSP.USA (Per capita real GSP in the U.S., measured in 2009 chained U.S. dollars) along with the ordinal columns, Start Hour and Day of Week increased test accuracy while the other numerical columns generally decreased test accuracy. 

Plotting the hyperparameter tuning also reveals some interesting trends. Increasing Max Depth too much leads to overfitting and accuracy drops while increasing Number of Estimators doesn't decrease accuracy much, but rather levels off.

The above model has a test accuracy of 67% which is good for this type of problem, but I would like to see if I can improve this model further by imputing some of the missing data and exploring categorical columns.

---
## Final Model

The first thing that I would like to do is impute the Cause Category Details to use them in the model. If I were to use it otherwise, I would have to drop about a third of my data due to the Cause Details being missing. As we saw before, the missingness of Cause Category Details is MAR on Cause Category so I'll probabilistically impute the details on the cause.

```py
final_df = impute_details(severity)
```

<iframe
  src="Assets/Conditional_Probabilities.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

The Visualization above shows what each Cause Category is composed of in terms of details. For example, 90% of intentional attacks are categorized as vandalism, while other causes such as severe weather has a more diverse set of details. Anyways, I have imputed the details based on the distribution of of the details in each category. Now, I'll include Cause Category and Details in my model by one hot encoding them.

I'm choosing to include these categorical variables since from a data generation perspective, certain causes are more likely to cause severe outages than others. Therefore, I think it makes sense to include them in the model.

```py
from sklearn.pipeline import Pipeline
from sklearn.compose import ColumnTransformer
from sklearn.preprocessing import OneHotEncoder
from sklearn.ensemble import RandomForestClassifier
from sklearn.model_selection import GridSearchCV
from sklearn.metrics import make_scorer, accuracy_score

cat_transformer = (
    "categorical",
    OneHotEncoder(handle_unknown="ignore"),
    ["CAUSE.CATEGORY.DETAIL", "CAUSE.CATEGORY"],
)

preprocessor = ColumnTransformer(
    [
        cat_transformer,
        ("numeric", "passthrough", ["START_HR", "START_DAYOFWEEK"]),
    ],
    remainder="drop",
)

pipeline = Pipeline(
    [
        ("preprocessor", preprocessor),
        ("classifier", RandomForestClassifier(min_samples_leaf=7, random_state=42)),
    ]
)

param_grid = {
    "classifier__max_depth": np.arange(1, 31, 3),
    "classifier__n_estimators": np.arange(1, 51),
}

grid_search = GridSearchCV(
    pipeline,
    param_grid,
    scoring=make_scorer(accuracy_score),
    cv=5,
    n_jobs=-1,
    verbose=3,
)

X = final_df.drop("SEVERITY", axis=1)
y = final_df["SEVERITY"]
grid_search.fit(X, y)
model = grid_search.best_estimator_

plot_accuracy_surface(grid_search, "Final")
print(f"Best score: {grid_search.best_score_}\nBest params: {grid_search.best_params_}")
```

<iframe
  src="Assets/Grid_Search_Results_Final.html"
  width="830"
  height="700"
  frameborder="0"
></iframe>
Best score: 0.7845826564554808  
Best params: {'classifier__max_depth': 16, 'classifier__n_estimators': 45}

For this model, I noticed that the model performs about the same with and without the PC.REALGSP.USA column, so I decided to remove it for the sake of efficiency. I also tried different combinations of other columns including POSTAL.CODE and UTIL.CONTRI but having more columns tended to decrease accuracy, most likely due to overfitting.

I stuck with the Random Forest Classifier because it performed best in this case and used GridSearchCV to find the best hyperparameters and best overall model. Looking at the hyperparameter tuning graph, we can see that increasing Max Depth and Number of Estimators didn't have much of an effect after a certain point as the graph leveled off at around 78%, an 11% improvement from the baseline model.

---
## Fairness Analysis

First, let's take a look at the model's confusion matrix as well as recall, precision, and accuracy.

<iframe
  src="Assets/Confusion_Matrix.html"
  width="500"
  height="500"
  frameborder="0"
></iframe>

```py
recall = float(cm[1, 1] / (cm[1, 1] + cm[1, 0]))
precision = float(cm[1, 1] / (cm[1, 1] + cm[0, 1]))
accuracy = float((cm[0, 0] + cm[1, 1]) / cm.sum())
print(f'Recall: {recall:.2f}\nPrecision: {precision:.2f}\nAccuracy: {accuracy:.2f}')
```
Recall: 0.84  
Precision: 0.76  
Accuracy: 0.79

It seems that our Recall score was pretty high, indicating that we did well against False Negatives. That is, we rarely predicted that an outage is Low Severity when it was actually High Severity. This is desirable for this type of model since thinking that an outage is Low Severity when in reality, it's High Severity means that you'll plan for the best case scenerio rather than the worst case. This could mean making plans with the expectation that the power will return sooner than when it actually returns.

### Fairness Analysis

Now, to perform the fairness analysis, I'll check how well the model performs in different States to see if it's more or less accurate in some states. Then, I'll plot it using folium again to visualize accuracy as geographical data.

<iframe
  src="Assets/Accuracy_by_State.html"
  width="830"
  height="600"
  frameborder="0"
></iframe>

It seems like we do pretty well in terms of accuracy for most states except for Nebraska for some reason. Now, Let's run a permuation test to see if this variance is due to random chance or not. I'll be using standard deviation of accuracy as my test statistic while shuffling the postal codes to perform this permutation test. I'll set up the hypotheses as such:

 - \\(H_0\\): The standard deviation in accuracies over states is due to random chance.
 - \\(H_a\\): The standard deviation in accuracies over states is not due to random chance.

```py
from tqdm import tqdm

dropped_na = severity.dropna(subset=["SEVERITY"])
observed_accuracies = pd.Series(accuracies_by_state(dropped_na))
observed = observed_accuracies.std()
stats = np.array([])

for _ in tqdm(range(1000)):
    shuffled = dropped_na.assign(
        **{"POSTAL.CODE": np.random.permutation(dropped_na["POSTAL.CODE"])}
    )
    stat = pd.Series(accuracies_by_state(shuffled)).std()
    stats = np.append(stats, stat)

pval = (stats >= observed).mean()
result = "Reject" if pval < 0.01 else "Fail to Reject"
print(f"p-value: {pval}\nHypothesis: {result}")
```

p-value: 0.025  
Hypothesis: Fail to Reject

It seems that with the p-value we get when running this permutation test allows us to to reject the null at a significance of 0.05 but not at 0.01. I would also like to see the p-value if we remove Nebraska from the mix. I'll then plot both observed test statsitics as on the histogram of permutated test statistics.

<iframe
  src="Assets/Permutation_Test.html"
  width="830"
  height="500"
  frameborder="0"
></iframe>

This graph reveals that removing Nebraska gives us much higher p-value, meaning we fail to reject the null. That is, it seems that the variation of accuracies of states is mostly likely due to random chance and are thus fair, except for Nebraska. I wonder what's going on in Nebraska.

---
## Conclusion

Overall, this was an interesting project and I hope you learned something from this analysis. I know I certainly did. I learned about new visualization techniques as well as creating Machine Learning models with scikit-learn, although I never figured out what's going on with Nebraska. This project was a lot of fun and I'm looking forward to my future in Data Science. Thanks for reading!

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/katex/dist/katex.min.css">
<script defer src="https://cdn.jsdelivr.net/npm/katex/dist/katex.min.js"></script>
<script defer src="https://cdn.jsdelivr.net/npm/katex/dist/contrib/auto-render.min.js"
    onload="renderMathInElement(document.body);"></script>