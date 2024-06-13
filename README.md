# Fast Food Marketing Campaign A/B Test

## Situation

A fast-food chain is planning to introduce a new menu item. To determine the most effective marketing campaign for promoting this new product, three different promotion plans were tested across several randomly selected market locations. The weekly sales of the new item were recorded over the first four weeks using a different promotion at each location.

## Goal of the Test

The primary objective of this A/B test was to identify which of the three promotion plans has the greatest impact on sales. Understanding the most effective promotion strategy will guide future marketing decisions for the store.

A confidence level of 99% was used for the analysis.

## Target Metric

The dataset provides the following metrics:
- **MarketSize**: Size of the market area by sales
- **AgeOfStore**: Age of the store in years
- **Promotion**: One of the three promotions tested
- **SalesInThousands**: Sales amount for a specific LocationID, Promotion, and week

For this analysis, the focus is on the Promotion and SalesInThousands metrics to determine which promotion yields the highest sales.

## Calculations

The table below summarizes the key results of the Fast Food Marketing Campaign A/B Test. Although non-target metrics were included in the dataset, they were excluded from the analysis to focus on the effectiveness of the promotions.

You can find the query for the table in the appendix.

### Table 1. Summary of the results of the Fast Food Marketing Campaign A/B Test

| Promotion | Sales Count | Mean Sales | Std Dev Sales |
|-----------|-------------|------------|---------------|
| 1         | 43          | 232.396    | 64.113        |
| 2         | 47          | 189.318    | 57.988        |
| 3         | 47          | 221.458    | 65.535        |

The First promotion showed the highest mean sales (232.396), followed by the Third promotion (221.457), and the Second promotion (189.317). The sales count is slightly imbalanced, but the proportions are reasonable for comparison.

To determine if these differences are statistically significant, we conducted a two-sample t-test for each pair of promotions using the Evans Miller A/B test calculator. Here we will be comparing confidence intervals and estimated differences between two groups (in our case we will be comparing promotions - first to second, second to third, and first to third). The calculator asks to insert the mean (Average Sales), standard deviation, and sales count. We can see these kinds of results:


![Comparison 1](https://github.com/augustinas-banikonis/A-B-test/blob/main/1%20vs%202.PNG)
#### Picture 1. Comparing first and second promotions

**Key results:**
- **The difference in Means (D)**: The mean sales for the First promotion was 232.396, while for the Second promotion, it was 189.317, resulting in a difference in means (D) of 43.079.
- **Standard Error (SE)**: The standard error of the difference in means was calculated to be approximately 12.927.
- **p-Value (P)**: The p-value for this t-statistic was found to be less than 0.01, indicating a statistically significant difference between the two means.

At a 99% confidence level, the difference in mean sales between the First and Second promotions is statistically significant (P < 0.01). This means that the First promotion generates significantly higher mean sales than the Second promotion. This suggests that the First promotion is more effective in generating higher revenue per sale compared to the Second promotion. Let’s jump to the second comparison.

![Comparison 2](https://github.com/augustinas-banikonis/A-B-test/blob/main/1%20vs%203.PNG))
#### Picture 2. Comparing First and Third promotions

**Key results:**
- **The difference in Means (D)**: The mean sales for the First promotion was 232.396, while for the Third promotion, it was 221.457, resulting in a difference in means (D) of 11.
- **Standard Error (SE)**: The standard error of the difference in means was calculated to be approximately 13.607.
- **p-Value (P)**: The p-value for this t-statistic was found to be significantly higher than 0.01, indicating that the difference in means is not statistically significant.

At a 99% confidence level, the difference in mean sales between the First promotion and the Third promotion is not statistically significant (P > 0.01). This indicates that there is no significant difference in the mean sales values between the two promotion plans. This suggests that the First promotion and the Third promotion perform similarly in terms of generating revenue per sale. Let’s check the last comparison between the second and third promotion plans.

![Comparison 3](https://github.com/augustinas-banikonis/A-B-test/blob/main/2%20vs%203.PNG))
#### Picture 3. Comparing second and third promotions

**Key results:**
- **The difference in Means (D)**: The mean sales for the Third promotion was 221.457, while for the Second promotion, it was 189.317, resulting in a difference in means (D) of -31.683.
- **Standard Error (SE)**: The standard error of the difference in means was calculated to be approximately 12.705.
- The p-value was found to be approximately 0.0145, indicating that the difference in means is statistically significant at the 95% confidence level but not at the 99% confidence level.

At a 99% confidence level, the difference in mean sales between the Third promotion and the Second promotion is not statistically significant (P = 0.0145). There is no significant difference in the mean sales values between the two promotion plans at this confidence level. This suggests that, while the Third promotion shows a higher mean sales value compared to the Second promotion, the evidence is not strong enough to conclusively state that the Third promotion is more effective at a 99% confidence level.

## Final Recommendation

- **Preferred Promotion Plan**: The First Promotion is recommended as it has shown a statistically significant higher mean sales value compared to the Second Promotion.
- **Consideration for The Third Promotion**: The Third Promotion is also a viable option as it performs similarly to the First Promotion and better than the Second Promotion in mean sales, although not significantly at the 99% confidence level. Additional data or a further detailed analysis might help in making a more robust conclusion between the First Promotion and the Third Promotion.

In summary, the First Promotion stands out as the most effective promotion plan based on the current analysis, with the Third Promotion being a strong contender. The Second Promotion is the least effective of the three.


**References:**

The Evan Miller A/B Test Calculator - https://www.evanmiller.org/ab-testing/

Dataset - https://www.kaggle.com/datasets/chebotinaa/fast-food-marketing-campaign-ab-test 

## Appendix

### Query for Table 1

```sql
WITH aggregated_data AS (
  SELECT location_id,
         promotion,
         SUM(sales_in_thousands) AS TotalSales
  FROM `tc-da-1.turing_data_analytics.wa_marketing_campaign`
  GROUP BY location_id, promotion
)
SELECT promotion AS PromotionID,
       COUNT(TotalSales) AS SalesCount,
       AVG(TotalSales) AS MeanSales,
       STDDEV(TotalSales) AS StdDevSales
FROM aggregated_data
GROUP BY promotion
