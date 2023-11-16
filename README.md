# An Investigation on the Power Outages Over Years

## Introduction

In both industrial production and daily life, electricity is frequently used every day. Consequently, power outages can have a significant impact on people. By analyzing power outage data, we can better guide how to enhance electricity usage quality, analyze potential causes of outages, and develop corresponding improvement measures.

The dataset is sourced from `https://engineering.purdue.edu/LASCI/research-data/outages` and contains power outages data since 2000, with 1534 rows.

The dataset has as many as 56 columns totally. But we only select a few columns related to our investigation, which are shown below:

| Column              | Description                    |
| ------------------- | ------------------------------ |
| `OUTAGE.START.DATE` | The date when an outage occurs |
| `OUTAGE.START.TIME` | The time when an outage occurs |
| `OUTAGE.DURATION`   | The minutes an outage lasts    |
|                     |                                |
|                     |                                |
|                     |                                |
|                     |                                |
|                     |                                |
|                     |                                |

## Data Cleaning and EDA

### Data Cleaning

#### Combine Data Column and Time Column

It's not convenience if a specific datetime is described by two columns, so first we combine the `OUTAGE.START.DATE` column and the `OUTAGE.START.TIME` column into a single `OUTAGE.START` column with the `pd.Timestamp` type.

