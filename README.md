# Impact_of_CoverCrops_on_WheatCropYields

Impact of Cover Crops on Wheat Crop Yields
As the global population continues to increase, scientists and policymakers are looking for ways to improve our agricultural systems so that we can feed more people.

The data used in this project is adapted from the 2017 Census of Agriculture performed by the U.S. Department of Agriculture (USDA). 
This survey is a complete count and characterization of U.S. farms and agricultural producers. 
The data include various information about agricultural land area and use, farming practices, and crop yields at the county level.

There are several farming strategies that promote the long-term health of agricultural land:

Agricultural land easements: contracts between the government and farmers where the government pays landowners to leave areas of the land untouched.
Conservation tillage: a method that helps protect the soil surface and reduce erosion from wind and water between crop harvest and planting.
Cover crops: plants used to slow erosion, return nutrients to the soil, and help smother weeds and control pests.
In this project, we’ll use inverse probability of treatment weighting (IPTW) to determine whether the use of cover crops causes an increase in crop yields. We encourage you to try all the project tasks in the file notebook.Rmd, but we have also provided solutions for reference in the file solution.Rmd.


# Data Exploration

1.
Let’s start by loading the dataset. We’ve provided a file named farms.csv in the workspace. Load this file into a dataframe named farm_df.



2.
Take a look at the head of the dataframe farm_df. Make sure to click through the arrows in the header to see all available variables.

Outcome:
total_yield: represents the average total yield of wheat in bushels per acre.
Treatment:
cover_10: indicates whether at least 10% of farms in a county employ cover crops.
Predictors:
region: geographic region where the county is located.
total_avg: average total size of farms (thousands of acres).
age_avg: average age of the primary farm operator (years).
experience_avg: average number of years of farming experience of the primary farm operator (years).
insurance_avg: average area of land with crop insurance (thousands of acres).
easement_p: indicates the average percent of land under an easement in the county.
conservation_till_avg: average area of land that uses conservation tillage methods (acres).
fertilizer_per_area: average cost of fertilizers used per acre (hundreds of thousands of dollars).


# Examine Initial Overlap and Balance

3.
First, let’s assess overlap and balance visually for a couple of variables to compare counties with AT LEAST 10% of farms using cover crops and counties with LESS THAN 10% of farms using cover crops.

Create a balance plot for the age_avg variable. Do the treatment and control distributions appear to be centered in the same location and have similar spreads?



4.
Now create a balance plot for the categorical variable region. Are the proportions of counties in the treatment versus control groups similar across the four regions?



5.
So far, it looks like we have quite a bit of imbalance between the treatment groups to deal with. Let’s assess balance numerically to quantify this imbalance more precisely. Create a balance table to show standardized mean differences (SMD) and variance ratios for all the predictor variables according to the treatment group. Check whether the balance measurements fall outside of the guidelines of ±0.1 for SMDs and between 0.5 and 2 for variance ratios.


# Perform IPTW Procedure and Re-check Balance

6.
Now it’s time to perform the inverse probability of treatment weighting (IPTW) procedure to see if we can minimize imbalance between the treatment groups before estimating the causal treatment effect.

To get the most out of this procedure, we have to carefully think about the form of the propensity score model. In other words, we should think about what variables in the dataset are predictive of cover crop use. Let’s start with a propensity score model with a limited set of variables:

region: The ability to grow cover crops depends in part on regional variation in climate.
total_avg: Cover crops might be more feasible to implement on farms that are smaller.
insurance_avg: Many farmers have crop insurance, but certain states offer subsidies to offset insurance costs if farmers decide to use cover crops.
fertilizer_per_area: Cover crops require the use of fertilizer. Higher fertilizer costs might prevent some farmers from using cover crops.
Perform an IPTW procedure using region, average farm size, average number of farms with crop insurance, and average cost of fertilizer per acre as predictors in the propensity score model. Specify in your code to calculate weights in order to estimate the average treatment effect (ATE). Save the output to a weighting object named farm_iptw.



7.
We need to perform a check of the IPTW procedure to see whether we have achieved a better balance between groups. Let’s look at the SMD values visually to see whether they now fall within our ±0.1 guidelines.

Create a Love plot showing the SMD between treatment groups for each variable in the propensity score model. Be sure to display the SMD for binary variables and the SMD threshold of ± 0.1.



# Refine Propensity Score Model

8.
The balance is pretty good, but let’s see if we can improve it further by tweaking the propensity score model.

Re-run the IPTW procedure but with a new propensity score model. This time, remove the fertilizer_per_area variable from the model. Then add to the model:

the average age of the primary producer
the average experience of the primary producer
the average percent of land under easement
the average number of farms using conservation tillage Save the new model as farm_iptw2.


9.
Let’s find out whether our expanded propensity score model leads to a better balance in the weighted data.

Create a Love plot showing the SMD between treatment groups for each variable using the new propensity score model. Be sure to display the SMD for binary variables and the SMD threshold of ±0.1.


10.
The Love plot shows SMD values closer to zero than the previous model! We can also inspect the distribution of propensity scores between treatment groups to see how well the IPTW procedure worked.

Generate a balance plot to display the distribution of propensity scores in each treatment group before AND after the weighting process. Do the distributions of the weighted propensity scores look closer to identical, and are they overlapping each other?

# Estimate Causal Treatment Effect

11.
You’re almost there — great job so far! The last step of the analysis is to fit the final outcome model to estimate the causal effect of cover crop usage on crop yields.

First, let’s fit the outcome regression model with total crop yield as the outcome, cover crop usage as the treatment variable, 
and the other variables from the second propensity score model as the additional predictors. 
Remember to include the weights from the IPTW procedure in the regression model. Save this regression model to an object name yield_mod.


12.
We’re not quite done yet! Remember that when we use IPTW to estimate the causal treatment effect, we need to use a robust standard error estimate.

Estimate the regression parameters for the weighted regression model using the coeftest() function. Incorporate a robust standard error estimator from the sandwich package.

13.
Take a look at the regression parameter for the treatment variable. 
How would you interpret this value? What do you think it says about the effect of cover crops on wheat yields? Write out your interpretation of the results.
