# An Investigation on the Power Outages Over Years

## Introduction

In both industrial production and daily life, electricity is frequently used every day. Consequently, power outages can have a significant impact on people. By analyzing power outage data, we can better guide how to enhance electricity usage quality, analyze potential causes of outages, and develop corresponding improvement measures.

This project mainly centered around a question "whether climate category and affect the duration of a power outage?" With knowing such conclusion, people can better indicates a pattern to identify or predict the major power outages, which can help humans to prepare on the sudden power outage or further research. 

This dataset includes 56 column variables and 1534 row variables in total. In researching the question stated above, not all 56 columns variables will be included in the analysis. Thus, relevant information will be included for the analysis such as 

| Column                    | Description                                                 |
| ------------------------- | ----------------------------------------------------------- |
| `OUTAGE.START.DATE`       | The date when an outage occurs                              |
| `OUTAGE.START.TIME`       | The time when an outage occurs                              |
| `OUTAGE.DURATION`         | The minutes an outage lasts                                 |
| `CLIMATE.CATEGORY`        | The climate type(warm, normal, cold) where an outage occurs |
| `U.S._STATE`              | The state where an outage occurs                            |
| `OUTAGE.RESTORATION.DATE` | The date when an outage ends                                |
| `CAUSE.CATEGORY.DETAIL`   | The detailed reason for an outage.                          |

We noted that there're many redundant data in the dataset, such as the `YEAR` and `MONTH` column is duplicated since `OUTAGE.START.DATE` is included. And with `OUTAGE.DURATION`, `OUTAGE.START.DATE` and `OUTAGE.START.TIME`, we can directly compute the `OUTAGE.RESTORATION.DATE` and the `OUTAGE.RESTORATION.TIME`. Therefore, we exclude some of such columns from our analysis.

Note that we take into account some columns that seems unrelated to our question, but it's useful to detect the below topic of missingness.

## Data Cleaning and EDA

### Data Cleaning

#### Combine Data Column and Time Column

It's not convenience if a specific datetime is described by two columns, so first we combine the `OUTAGE.START.DATE` column and the `OUTAGE.START.TIME` column into a single `OUTAGE.START` column with the `pd.Timestamp` type.

The cleaned dataframe's datatype for each useful column is listed below.

```
OUTAGE.START               datetime64[ns]
OUTAGE.DURATION                    object
U.S._STATE                         object
CLIMATE.CATEGORY                   object
OUTAGE.RESTORATION.DATE            object
CAUSE.CATEGORY.DETAIL              object
dtype: object
```

The cleaned dataframe is shown below(with only representative rows selected for display).

```
           OUTAGE.START OUTAGE.DURATION U.S._STATE CLIMATE.CATEGORY  \
5   2015-07-18 02:00:00            1740  Minnesota             warm   
4   2012-06-19 04:30:00            2550  Minnesota           normal   
3   2010-10-26 20:00:00            3000  Minnesota             cold   
54  2014-01-24 00:00:00          108653  Wisconsin             cold   
833 2013-08-12 11:55:00               4     Oregon           normal   

    OUTAGE.RESTORATION.DATE CAUSE.CATEGORY.DETAIL  
5       2015-07-19 00:00:00                   NaN  
4       2012-06-20 00:00:00          thunderstorm  
3       2010-10-28 00:00:00            heavy wind  
54      2014-04-09 00:00:00                  Coal  
833     2013-08-12 00:00:00   suspicious activity 
```

### Univariate Analysis

#### Distribution of Outage Duration

In the univariate analysis, we would analyze the distribution of the outage duration and U.S. states.

<iframe src="assets/univariate1.html" width=800 height=600 frameBorder=0></iframe>

This shows that exception from very few data, most of the duration is short. About half outages are less than 1,000 minutes, and most are less than 5,00 minutes, while only few outages lasting longer than that. Also, it means that the variance may be large since the minimal and maximal values differ a lot.

#### Distribution of Outage U.S. States

Then, we use the same way to analyze the distribution of U.S. states of the outages.

<iframe src="assets/univariate2.html" width=800 height=600 frameBorder=0></iframe>



