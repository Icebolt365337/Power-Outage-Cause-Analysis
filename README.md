# Power Outage Cause Analysis
---

## Table of Contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Introduction

Author - Uday Lingampalli

The prevalence of technology in our lives has also brought with it a new problem, or should I say, magnified a previously existing problem, power outages. When the power goes out, a lot of what we're doing can come to a halt, which can have big consequences. What's just as important as fixing a power outage is learning the cause of one, as this can give insights on how to effectively and efficiently fix one, as well as give future knowledge on potential prevention. In order to effectively use the knowledge of the cause of a power outage, though, we need to analyze the characteristics of these causes. In order to do this, I will be using the following dataset on power outages, additional details of which can be found [here.]("https://www.sciencedirect.com/science/article/pii/S2352340918307182") The dataset has 1534 rows, and 38 columns relevant to my analysis. These columns, and their descriptions (taken from the above link), are as follows:

| Column Name | Column Description |
| --- | --- |
| 'YEAR' | Indicates the year when the outage event occurred |
| 'MONTH' | Indicates the month when the outage event occurred |
| 'POSTAL.CODE' | Represents the postal code of the U.S. state where the outage event occurred |
| 'NERC.REGION' | The North American Electric Reliability Corporation (NERC) regions involved in the outage event |
| 'CLIMATE.REGION' | U.S. Climate regions as specified by National Centers for Environmental Information (nine climatically consistent regions in continental U.S.A.) |
| 'ANOMALY.LEVEL' | This represents the oceanic El Niño/La Niña (ONI) index referring to the cold and warm episodes by season. It is estimated as a 3-month running mean of ERSST.v4 SST anomalies in the Niño 3.4 region (5°N to 5°S, 120–170°W) |
| 'CLIMATE.CATEGORY' | This represents the climate episodes corresponding to the years. The categories—“Warm”, “Cold” or “Normal” episodes of the climate are based on a threshold of ± 0.5 °C for the Oceanic Niño Index (ONI) |
| 'OUTAGE.START.DATE' | 	This variable indicates the day of the year when the outage event started (as reported by the corresponding Utility in the region) |
| 'OUTAGE.START.TIME' | 	This variable indicates the time of the day when the outage event started (as reported by the corresponding Utility in the region) |
| 'OUTAGE.RESTORATION.DATE' | This variable indicates the day of the year when power was restored to all the customers (as reported by the corresponding Utility in the region) |
| 'OUTAGE.RESTORATION.TIME' | This variable indicates the time of the day when power was restored to all the customers (as reported by the corresponding Utility in the region) |
| 'CAUSE.CATEGORY' | 	Categories of all the events causing the major power outages |
| 'OUTAGE.DURATION' | Duration of outage events (in minutes) |
| 'DEMAND.LOSS.MW' | Amount of peak demand lost during an outage event (in Megawatt) [but in many cases, total demand is reported] |
| 'CUSTOMERS.AFFECTED' | 	Number of customers affected by the power outage event |
| 'RES.PRICE' | Monthly electricity price in the residential sector (cents/kilowatt-hour) |
| 'COM.PRICE' | 	Monthly electricity price in the commercial sector (cents/kilowatt-hour) |
| 'IND.PRICE' | Monthly electricity price in the industrial sector (cents/kilowatt-hour) |
| 'RES.SALES' | 	Electricity consumption in the residential sector (megawatt-hour) |
| 'COM.SALES' | 	Electricity consumption in the commercial sector (megawatt-hour) |
| 'IND.SALES' | Electricity consumption in the industrial sector (megawatt-hour) |
| 'RES.PERCEN' | 	Percentage of residential electricity consumption compared to the total electricity consumption in the state (in %) |
| 'COM.PERCEN' | 	Percentage of commercial electricity consumption compared to the total electricity consumption in the state (in %) |
| 'IND.PERCEN' | 	Percentage of industrial electricity consumption compared to the total electricity consumption in the state (in %) |
| 'RES.CUSTOMERS' | 	Annual number of customers served in the residential electricity sector of the U.S. state |
| 'COM.CUSTOMERS' | 	Annual number of customers served in the commercial electricity sector of the U.S. state |
| 'IND.CUSTOMERS' | Annual number of customers served in the industrial electricity sector of the U.S. state |
| 'RES.CUST.PCT' | 	Percent of residential customers served in the U.S. state (in %) |
| 'COM.CUST.PCT' | Percent of commercial customers served in the U.S. state (in %) |
| 'IND.CUST.PCT' | Percent of industrial customers served in the U.S. state (in %) |
| 'PC.REALGSP.STATE' | 	Per capita real gross state product (GSP) in the U.S. state (measured in 2009 chained U.S. dollars) |
| 'PC.REALGSP.USA' | 	Per capita real GSP in the U.S. (measured in 2009 chained U.S. dollars) |
| 'PC.REALGSP.REL' | 	Relative per capita real GSP as compared to the total per capita real GDP of the U.S. (expressed as fraction of per capita State real GDP & per capita US real GDP) |
| 'PC.REALGSP.CHANGE' | 	Percentage change of per capita real GSP from the previous year (in %) |
| 'UTIL.REALGSP' | 	Real GSP contributed by Utility industry (measured in 2009 chained U.S. dollars) |
| 'TOTAL.REALGSP' | Real GSP contributed by all industries (total) (measured in 2009 chained U.S. dollars) |
| 'UTIL.CONTRI' | Utility industry׳s contribution to the total GSP in the State (expressed as percent of the total real GDP that is contributed by the Utility industry) (in %) |
| 'PI.UTIL.OFUSA' | 	State utility sector׳s income (earnings) as a percentage of the total earnings of the U.S. utility sector׳s income (in %) |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning

In order to clean the data, I first dropped 17 columns which wouldn't be considered or used for my analysis, as well as dropping rows/columns that contained formatting information, redundant information, and metadata but no data. The columns I kept are the ones displayed in the table above.

Next, I combined the 'OUTAGE.START.DATE' and the 'OUTAGE.START.TIME' columns into one column, 'OUTAGE.START', which combined the information into a pd.Timestamp, and deleted the predecessor columns now that they had irrelevant information. I proceeded to do the same for the 'OUTAGE.RESTORATION.DATE' and the 'OUTAGE.RESTORATION.TIME' columns.

Afterwards, I checked the range of unique values for each columns, checking for invalid inputs. Most of the columns already had NaN for invalid inputs, but I noticed that the 'OUTAGE.DURATION', 'OUTAGE.LOSS.MW', and the 'CUSTOMERS.AFFECTED' columns had values of 0, which would be impossible for a reported outage. I replaced these invalid 0 values with NaN.

The first few rows/columns of the cleaned data is below: 

|   YEAR |   MONTH | POSTAL.CODE   | OUTAGE.START        | OUTAGE.RESTORATION   |
|-------:|--------:|:--------------|:--------------------|:---------------------|
|   2011 |       7 | MN            | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |
|   2014 |       5 | MN            | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |
|   2010 |      10 | MN            | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |
|   2012 |       6 | MN            | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |
|   2015 |       7 | MN            | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |

### Univariate Analysis

<iframe
  src="assets/univariate_1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot shows the number of outages per outage cause. From the graph, we can see that most outages are caused by 'severe weather', whereas the least outages are caused by 'islanding'.

<iframe
  src="assets/univariate_2.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This plot shows a survival chart of outage duration - in other words, the y-value represents the proportion of outages that last as long as or longer than that duration. From the graph, we can see that ~90% of outages last shorter than 7000 minutes.

<iframe
  src="assets/univariate_3.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This is a choropleth distribution that relates the number of outages per state. From the graph, we can see that California has a lot of outages, while many states in the central U.S. tend to have few outages.

### Bivariate Analysis

<iframe
  src="assets/bivariate_1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

This graph shows the distribution of outages by cause, per climate category. We can see that all climate categories seem to have a similar distribution of outages by cause, suggesting that climate category doesn't play a large role in outage cause, although it is important to note that the number of outages per climate category differ significantly.

### Interesting Aggregates

|   YEAR |   equipment failure |   fuel supply emergency |   intentional attack |   islanding |   public appeal |   severe weather |   system operability disruption |
|-------:|--------------------:|------------------------:|---------------------:|------------:|----------------:|-----------------:|--------------------------------:|
|   2000 |           0.192308  |                       0 |            0.0769231 |           0 |       0         |        0.5       |                       0.230769  |
|   2001 |           0.0666667 |                       0 |            0         |           0 |       0.2       |        0.0666667 |                       0.666667  |
|   2002 |           0         |                       0 |            0.0588235 |           0 |       0         |        0.764706  |                       0.176471  |
|   2003 |           0.130435  |                       0 |            0.0434783 |           0 |       0.0217391 |        0.652174  |                       0.152174  |
|   2004 |           0.0704225 |                       0 |            0         |           0 |       0.0985915 |        0.788732  |                       0.0422535 |

This aggregate shows the proportion of outages per cause, for each year of the dataset. An interesting trend we can see is that, with a few exceptions, severe weather uniformly causes the majority of outages for each year.

## Assessment of Missingness

### NMAR Analysis

I believe that the column 'OUTAGE.START' could be NMAR. The data in this column could be missing if the outage occurred at a time when few employees were working or present to handle that outage, causing the start time of the outage to not be reported and therefore not be present. If we could get additional data on the number of available employees at that time when the outage occurred, this column could potentially be MAR if that information was successfully tested and shown to likely explain the missingness.

### Missingness Dependency

I analyzed the missingness of the 'CUSTOMERS.AFFECTED' column on two other columns, 'CAUSE.CATEGORY' and 'CLIMATE.CATEGORY', using permutation testing with 1000 simulations. The first permutation test, analyzing the missingness of 'CUSTOMERS.AFFECTED' on 'CAUSE.CATEGORY', is as follows:

Null Hypothesis: 'CUSTOMERS.AFFECTED' is not MAR on the 'CAUSE.CATEGORY' column
Alternative Hypothesis: 'CUSTOMERS.AFFECTED' is MAR on the 'CAUSE.CATEGORY' column
Test statistic: TVD (Total Variance Distribution)
Significance Level: 0.05

The resulting p-value was 0.0. Since this was lower than the significance level, I rejected the null hypothesis. A graph of the simulations with the simulated and actual TVD is below:

<iframe
  src="assets/missingness_1.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

The second permutation test, analyzing the missingness of 'CUSTOMERS.AFFECTED' on 'CLIMATE.CATEGORY', is as follows:

Null Hypothesis: 'CUSTOMERS.AFFECTED' is not MAR on the 'CLIMATE.CATEGORY' column
Alternative Hypothesis: 'CUSTOMERS.AFFECTED' is MAR on the 'CLIMATE.CATEGORY' column
Test statistic: TVD (Total Variance Distribution)
Significance Level: 0.05

The resulting p-value was 0.26. Since this was higher than the significance level, I failed to reject the null hypothesis.

## Hypothesis Testing

I decided to test some characteristics of 'CAUSE.CATEGORY', in relation to the 'OUTAGE.DURATION' column. In particular, I wanted to test whether the outage cause of 'severe weather' had a greater outage duration than 'system operability disruption', using permutation testing with a 1000 simulations.

Null Hypothesis: The outage cause of 'system operability disruption' and 'severe weather' have the same outage durations
Alternative Hypothesis: The outage cause of 'severe weather' has a greater outage duration than 'system operability disruption'
Test Statistic: Difference in the average outage duration for 'severe weather' and 'system operability disruption'
Significance Level: 0.05

I chose this test statistic because the mean of the 'OUTAGE.DURATION' was the best way to test on this in relation to the 'CAUSE.CATEGORY' on only two causes. We couldn't use a TVD since we weren't comparing categorical distribution values, and a K-S test statistic would be meaningless here as there is nothing inherently important about the probability distribution function of the respective 'OUTAGE.DURATION'. I chose a significance level of 0.05 as I wanted to keep the chance that I incorrectly rejected the null hypothesis at 5%.

The resulting p-value was 0.0. Since the p-value was less than the significance level 0.05, we reject the null hypothesis.

## Framing a Prediction Problem

Next, I decided to make a prediction model to predict the cause of a major power outage. As I am attempting to predict the 'CAUSE.CATEGORY' column here, this is a multiclass classification problem, and I chose this variable as it is the best representation of the cause of a power outage. I decided to use accuracy as the metric for my model. I chose accuracy over other metrics such as precision and recall, as due to being a multiclass classification problem, there aren't glaring class imbalance issues which could harm the reliability of the accuracy metric, and as such accuracy seemed the best way to evaluate the model's performance in prediction for all classes.

## Baseline Model

For my baseline model, I decided to use a decision tree model. The full list of features that I allowed my model to use were as follows: 'POSTAL.CODE', 'NERC.REGION', 'CLIMATE.REGION', 'ANOMALY.LEVEL', 'CLIMATE.CATEGORY', 'CUSTOMERS.AFFECTED', 'RES.PRICE', 'COM.PRICE', 'IND.PRICE', 'RES.SALES', 'COM.SALES', 'IND.SALES', 'RES.PERCEN', 'COM.PERCEN', 'IND.PERCEN', 'RES.CUSTOMERS', 'COM.CUSTOMERS', 'IND.CUSTOMERS', 'RES.CUST.PCT', 'COM.CUST.PCT', 'IND.CUST.PCT', and 'PC.REALGSP.REL'. Most of these features were state/location dependent information that wasn't reliant on an outage restoration, therefore I could be confident that this information would be known prior to knowing the cause of an outage. The only feature that doesn't apply to is the 'CUSTOMERS.AFFECTED' column, but I believe that many reports of an outage at the same time could be considered the same outage and allow the knowledge of how many customers were affected without knowing the cause. The 'POSTAL.CODE', 'NERC.REGION', and 'CLIMATE.REGION' were nominal features, the 'CLIMATE.CATEGORY' column was an ordinal one, and the rest were quantitative features.

I used one hot encoding for the nominal and ordinal features, as I wasn't sure of what an appropriate order for the 'CLIMATE.CATEGORY' column values would be, even though I believed there was one. The baseline model reported an accuracy of 92.41% on the training set, and an accuracy of 79.63% on the testing set. While I believe the baseline model is good and fairly accurate, the ~12% difference makes me believe the model may have slightly overfit the training data.

## Final Model

The first new feature I engineered was using an np.log transformer on the 'CUSTOMERS.AFFECTED' column. This was because I believed outliers on the data could skew the model, whereas, after a certain point, a lot of customers affected is still a lot of customers even if there is a value significantly higher - as such, I used this log transformation to dampen the effect of outliers and large values. I also applied a Quantile Transformer to the 'RES.SALES', 'COM.SALES', 'IND.SALES', 'RES.CUSTOMERS', 'COM.CUSTOMERS', 'IND.CUSTOMERS' - as all these values are statewise, I felt it was much important to know where these values were relative to each other, which a quantile transformer would allow me to access.

I also decided to change my model to Random Forest model, as I believed it would be more robust to overfitting. I used a GridSearchCV to find the best hyperparameters, which in turn used k-fold cross-validation to find the best hyperparameters of maximum tree search depth and the number of trees in the forest. Out of the possible hyperparameter values I tested, the best ones ended up being a maximum depth of 22, and the number of trees as a 100. The final model had a 100% accuracy on the training set, a significant improvement, and a 81.94% accuracy on the testing set, a marginal improvement.

## Fairness Analysis

I decided to test the fairness of my model on relatively wealthier states, vs. less wealthy states, measured by relative per capita real GDP, or the column PC.REALGSP.REL, which relates a state's per capita real GDP to the U.S. per capita real GDP.
I set a threshold of 1, which are states whose per capita GSP is the same as the U.S. per capita GSP, with states less than this threshold considered to have less relative GDP, and states more than this threshold to have more relative GDP. Since this is a multiclassifier without any notable class imbalance, I decided to use accuracy as my measure of fairness.

Null Hypothesis: The model is fair. Its accuracy for states with less relative GDP and states with more relative GDP are the same, and any differences are due to random chance.
Alternative Hypothesis: Our model is unfair. Its accuracy for states with more relative GDP is higher than for states with more relative GDP.
Test Statistic: Accuracy
Significance Level: 0.05

The resulting p-value was 1.0. Since the p-value is higher than a significance value of 0.05, we fail to reject the null hypothesis.