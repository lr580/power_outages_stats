<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
<script> 
MathJax = {
  tex: {
    inlineMath: [['$', '$']],
    processEscapes: true
  }
};
</script>

# An Investigation on the Power Outages Over Years

## Introduction

In both industrial production and daily life, electricity is frequently used every day. Consequently, power outages can have a significant impact on people. By analyzing power outage data, we can better guide how to enhance electricity usage quality, analyze potential causes of outages, and develop corresponding improvement measures.

This project mainly centered around a question "whether climate category  affect the duration of a power outage?" With knowing such conclusion, people can better indicates a pattern to identify or predict the major power outages, which can help humans to prepare on the sudden power outage or further research. 

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

| OUTAGE.START        |   OUTAGE.DURATION | U.S._STATE   | CLIMATE.CATEGORY   | OUTAGE.RESTORATION.DATE   | CAUSE.CATEGORY.DETAIL   | OUTAGE.RESTORATION.MISSING   | OUTAGE.DURATION.MISSING   | OUTAGE.START.MISSING   | CAUSE.CATEGORY.DETAIL.MISSING   | RANDOM   | NO_RANDOM   |
|:--------------------|------------------:|:-------------|:-------------------|:--------------------------|:------------------------|:-----------------------------|:--------------------------|:-----------------------|:--------------------------------|:---------|:------------|
| 2015-07-18 02:00:00 |              1740 | Minnesota    | warm               | 2015-07-19 00:00:00       | nan                     | False                        | False                     | False                  | True                            | a        | False       |
| 2012-06-19 04:30:00 |              2550 | Minnesota    | normal             | 2012-06-20 00:00:00       | thunderstorm            | False                        | False                     | False                  | False                           | b        | False       |
| 2010-10-26 20:00:00 |              3000 | Minnesota    | cold               | 2010-10-28 00:00:00       | heavy wind              | False                        | False                     | False                  | False                           | b        | True        |
| 2014-01-24 00:00:00 |            108653 | Wisconsin    | cold               | 2014-04-09 00:00:00       | Coal                    | False                        | False                     | False                  | False                           | b        | True        |
| 2013-08-12 11:55:00 |                 4 | Oregon       | normal             | 2013-08-12 00:00:00       | suspicious activity     | False                        | False                     | False                  | False                           | b        | False       |


### Univariate Analysis

#### Distribution of Outage Duration

In the univariate analysis, we would analyze the distribution of the outage duration and U.S. states.

<iframe src="assets/univariate1.html" width=800 height=600 frameBorder=0></iframe>

This shows that exception from very few data, most of the duration is short. About half outages are less than 1,000 minutes, and most are less than 5,00 minutes, while only few outages lasting longer than that. Also, it means that the variance may be large since the minimal and maximal values differ a lot.

#### Distribution of Outage U.S. States

Then, we use the same way to analyze the distribution of U.S. states of the outages.

<iframe src="assets/univariate2.html" width=800 height=600 frameBorder=0></iframe>

This shows that some states frequently occur power outages(more than 50 times in total), and most others are less frequent, even several states only have several power outages. It's seems that the distribution is similar to gaussian distribution.

### Bivariate Analysis

Then, we do bivariate analysis between the outage duration and the state where the outage occurs.

<iframe src="assets/bivariate1.html" width=800 height=600 frameBorder=0></iframe>

The scatter plot shows that they may exist very strong correlation between the outage duration and the state. We could say that there may have positive relationship between the two variables.

To further explore it, we calculate the average outage duration minutes of different states and plot a line graph.

<iframe src="assets/bivariate2.html" width=800 height=600 frameBorder=0></iframe>

It seems that the average outage duration is different between different states. To check it further, we calculate the median, and the quartile below.

<iframe src="assets/bivariate3.html" width=800 height=600 frameBorder=0></iframe>

It seems that the duration time differs a lot between different states. So we can roughly conclude that it may have strong correlation.

### Interesting Aggregates

In the above mentioned bivariate analysis, we get a grouped table `stat_df`. Here, we show it again in table form. Below shows some rows of a grouped table aggregated by states and calculated the mean, median, and quartile of the outage duration.

| U.S._STATE    |     mean |   median |    Q1 |      Q3 |
|:--------------|---------:|---------:|------:|--------:|
| Mississippi   |   84     |     17.5 |   4   |   97.5  |
| Hawaii        |  845.4   |    543   | 237   | 1367    |
| Massachusetts |  944.167 |    211   |  19   | 1443.75 |
| Tennessee     | 1041.97  |    310   |  39   | 1230    |
| New York      | 6034.96  |   2880   | 268.5 | 8156.25 |

Furthermore, we want to explore the relationship between different climate category(i.e. `CLIMATE.CATEGORY` column) and the outage duration of different states. So we plot an pivot table below (Also, only show some rows).

| U.S._STATE    |    cold |  normal |    warm |
| :------------ | ------: | ------: | ------: |
| Wisconsin     | 12545.6 | 3962.56 |    1605 |
| Hawaii        |    1367 |   205.5 |  1224.5 |
| Massachusetts | 160.333 | 1159.38 |     721 |
| Tennessee     | 1476.58 | 1015.75 | 341.857 |
| New York      | 8914.58 | 3673.56 | 6092.83 |

To observe it more intuitively, we plot the table below.

<iframe src="assets/bar_climate_category.html" width=800 height=600 frameBorder=0></iframe>

It seems that the outage duration differ a lot in different climate. The warmer, the longer it lasts. But it's not necessary that it's true, so we'd check that in the hypothesis test below.

## Assessment of Missingness

### NMAR Analysis

One of the column in our dataset with missing values that is possibly NMAR is the `OUTAGE.DURATION` column. The reason for its missingness is most likely due to some reasons that why the `OUTAGE.START.DATE` or more frequently, the `OUTAGE.RESTORATION.DATE` is missing. Any of these columns miss will lead to the `OUTAGE.DURATION` miss. However, there's no clear reasoning why they miss recorded in the dataset. If we want additional data to record the reason why the start and restoration datetime of a outage is missing, then we could make it MAR.

### Missingness Dependency

We constructed permutation tests to determine the relationship. We've decided to test the dependency between the missingness of `OUTAGE.DURATION` with two columns: `OUTAGE.RESTORATION.DATE` and `CAUSE.CATEGORY.DETAIL`.

#### OUTAGE.DURATION and OUTAGE.RESTORATION.DATE (MAR)

It's no doubt that the missingness of the `OUTAGE.DURATION` depend on the missingness `OUTAGE.RESTORATION.DATE`. We can easily figure out this from both observing the data and reasoning in the reality. And now, we'd like check it by performing permutation tests.

Since it's a category column, we use TVD(Total Variation Distance) to perform permutation test. Recall that:

$$
TVD(X,Y) = \dfrac 12\sum_{i=1}^{n} \left| X_i - Y_i \right|
$$

The null hypothesis for the permutation test is that the specific column does not depend on another column. So if p-value is less than the significance level, we reject the null hypothesis, which means that we may think the column depend on another. Otherwise, we fail to reject the null hypothesis, which means that it's more possible to believe the column does not depend on another.

Below, we computed the TVD, run the permutation tests of 500 rounds, set the significance level to 0.05, calculate the p-value, draw the plot and get the conclusion.

The result is shown below.

<iframe src="assets/permutation_test_OUTAGE.DURATION.MISSING_OUTAGE.RESTORATION.MISSING.html" width=800 height=600 frameBorder=0></iframe>

The result shows that p-value is 0.00, since the significance level is 5%, we reject the null hypothesis. Therefore, we conclude that it is highly possible that the missingness of the 
`OUTAGE.DURATION` depends on the missingness of the `OUTAGE.RESTORATION`.

#### OUTAGE.DURATION and CAUSE.CATEGORY.DETAIL (MCAR)

We can easily find that, on the other hand, the missingness of the `CAUSE.CATEGORY.DETAIL`, have nothing to do with the missingness of `OUTAGE.DURATION`. Since it only depicts some additional information of the `CAUSE.CATEGORY`. So we should find that the missingness of the `CAUSE.CATEGORY.DETAIL` is independent of the missingness of the `CAUSE.CATEGORY.DETAIL`. Below, we will check our conjecture.

<iframe src="assets/permutation_test_OUTAGE.DURATION.MISSING_CAUSE.CATEGORY.DETAIL.html" width=800 height=600 frameBorder=0></iframe>

The plot result shows that p-value is 0.89, which is largely greater than the significance level of 5%, so we fail to reject the null hypothesis. Hence, we conclude that the missingness of `OUTAGE.DURATION` does not depend on the missingness of `CAUSE.CATEGORY.DETAIL`.

## Hypothesis Testing

### Permutation Test

Going back to our investigation topic, we are investigating does climate category affect the duration of a power outage. Recalling the plot in the interesting aggregates part, we intuitively propose the idea that there are some relationships between the climate and the duration of power outages. But observation alone cannot be a good indicator. So we determined a good way to test is making a hypothesis test.

We think that permutation test on the distribution of outage durations in the warm climate areas and the distribution in the cold climate areas to see whether there is an actual increase in duration time to warmer areas or not.

Null hypothesis: the outage duration time in the warm climate and the cold climate comes from the same distribution.

Alternative hypothesis: the outage duration time in the warm climate and the cold climate comes from different distributions.

Significance level: 5%. 

Number of permutation test rounds: 500.

Test statistics: the outage duration is numeric data rather than categorical. And recall that numeric data uses diff of means, which the categorical data uses TVD. Thereby, we use the diff of means as our test statistics.

The value is numeric rather than category, so we use the absolute difference between the mean of the two groups to check it. Recall the formula:

$$
diff\_of\_means(A,B) = |\dfrac 1n\sum_{i=1}^nA_i
- \dfrac 1m\sum_{i=1}^mB_i|
$$

Using the similar way, we perform the permutation test below.

<iframe src="assets/hyp_OUTAGE.DURATION_CLIMATE.CATEGORY_warm_cold.html" width=800 height=600 frameBorder=0></iframe>

### Conclusion

Out of our expectation, the result shows that p-value is 0.71, which is larger than 0.05, so we fail to reject the null hypothesis, which means that the outage duration time in the warm climate and the cold climate comes from the same distribution. That is, the outage duration have no relationship with the climate.