---
title: "STOR320 Final Paper"
author: "Timothy Lynch"
date: "April 24, 2020"
output:
  html_document:
    keep_md: yes
---



# INTRODUCTION

The Boston Marathon is arguably the world's most prestigious marathon, run annually on the 3rd Monday of April dating back to 1897. Since 1897, this difficult 26.2 mile race beginning in the suburbs outside of Boston has evolved tremendously into not only a regional holiday for the New England area, but also a global celebration. The race now has over 30,000 entrants annually and attracts runners from all over the globe of all ages and experience levels. 

While many runners particpate in the Boston Marathon with the primary goal of simply finishing the race, many runners are also very interested in their official finish time. Given the diverse makeup of the pool of entrants in the Boston Marathon, it is natural to wonder which demographics make a runner appear more suitable to finish with a faster time. This consideration inspired the creation of the first question: **Which combination of predictors and the interactions between them produce the best model for predicting official time?**. Potential predictors that we'll consider will include age, sex, the year the race was ran, and starting wave, in addition to the interactions between each of these predictors. 

While an individual's demographic characteristics is in many cases the best available set of demographic data to predict their Boston Marathon time, many runners participate in the Boston Marathon in multiple consecutive years. It may be possible to incorporate the official finish times of individuals in previous years to predict their finish times in subsequent years. This desire to more accurately predict the finish times of repeat participants led to the creation of our second question: **How can we predict the finish time for runners in the Boston Marathon who also participated the two years prior?**. Developing a model specific to runners who participated in consecutive years is especially useful because it not only will likely be more accurate in predicting the finish times of these runners, but also used by runners to decide whether they should run the marathon again based upon their predicted time. 

# DATA

Our data, consisting of Boston Marathon finisher data from 2015 to 2017, was obtained from Kaggle. The original source of the data was the marathon's official results for each year courtesy of the Boston Athletic Association. Since the data contains all finishers from the three years in question, we did not encounter any issues with regard to data collection, such as non-response bias. Each observation represents a different finisher of the marathon for that particular year with 26410, 26630, and 26597 finishers over the course of the three year period. 

The original dataset contained 14 variables directly determined by an individual's performance in the race in addition to 7 variables that helped provide identifying information about an individual. However, some of these variables were not entirely useful to predicting marathon times and were excluded from our analysis. For the relevant variables, *OfficialTime* is the time a runner took to travel from the starting line to the finish line. The data also includes *Half*, a runner's overall time 13.1 miles into the race, in addition to their time at every 5 kilometer interval, marked as *5K*, *10K*, etc. These interval times were useful in getting a sense of what parts of the course tend to be run faster by runners than other parts, but were not useful in predicting a runner's overall time since these interval times would obviously not be known prior to the start of the race. For the non-performance related variables, the *Age* variable was used to create the *AgeGroup* variable based on the official age divisions designated by the BAA, such as 18to39, 40to44, 45to49, etc. The *State* variable lists the two digit abbreviation for the state or province among those from USA or Canada, while the *Country* variable lists the three digit country code for all finishers.

Additionally, while the *Bib* variable is merely an identifier variable as each runner in the race has a unique Bib number, the *Bib* variable was useful as it helped create a new variable *Wave*. Since the Boston Marathon has many participants, runners are broken up into different starting groups, called Waves. An individual's Bib number indicates what Wave they are in as those in Wave 1 have Bib numbers between 101 and 7700, those in Wave 2 between 8000 and 15600, those in Wave 3 between 16000 and 23600, and those in Wave 4 between 24000 and 32500. Additionally, as Boston is one of the few marathons in the world to require qualifying times, runners are sorted into each wave based on their qualifying times. While the qualifying thresholds for each wave change slightly each year, Wave 1 runners generally have qualifying times under 3:10, Wave 2 between 3:10 and 3:29, Wave 3 between 3:29 and 3:50, and Wave 4 above 3:50. Charity runners and others without qualifying times are generally sorted into Wave 4. Because the waves are determined by these qualifying times, knowing an indivudual's wave will likely be useful to predict their official time. The below table displays the average times for runners from each wave from 2015 to 2017, confirming the notion that each subsequent wave is slower than the previous one.


<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<caption>Average Boston Marathon Times by Wave (2015-2017)</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Wave </th>
   <th style="text-align:left;"> 2015 </th>
   <th style="text-align:left;"> 2016 </th>
   <th style="text-align:left;"> 2017 </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Elite </td>
   <td style="text-align:left;"> 2:34:54 </td>
   <td style="text-align:left;"> 2:38:50 </td>
   <td style="text-align:left;"> 2:35:19 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> One </td>
   <td style="text-align:left;"> 3:07:20 </td>
   <td style="text-align:left;"> 3:16:11 </td>
   <td style="text-align:left;"> 3:18:34 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Two </td>
   <td style="text-align:left;"> 3:32:46 </td>
   <td style="text-align:left;"> 3:41:24 </td>
   <td style="text-align:left;"> 3:45:07 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Three </td>
   <td style="text-align:left;"> 3:52:11 </td>
   <td style="text-align:left;"> 3:58:40 </td>
   <td style="text-align:left;"> 4:04:51 </td>
  </tr>
  <tr>
   <td style="text-align:left;font-weight: bold;"> Four </td>
   <td style="text-align:left;"> 4:32:44 </td>
   <td style="text-align:left;"> 4:39:43 </td>
   <td style="text-align:left;"> 4:45:04 </td>
  </tr>
  <tr>
   <td style="text-align:left;color: white !important;background-color: cornflowerblue !important;font-weight: bold;"> All Runners </td>
   <td style="text-align:left;color: white !important;background-color: cornflowerblue !important;"> 3:46:26 </td>
   <td style="text-align:left;color: white !important;background-color: cornflowerblue !important;"> 3:55:03 </td>
   <td style="text-align:left;color: white !important;background-color: cornflowerblue !important;"> 3:58:05 </td>
  </tr>
</tbody>
</table>


In addition to showing how each subsequent wave is slower than the previous wave, the above table also reveals trends in the average performance of all runners. Out of the 3 years considered, 2015 was the fastest year on average for all runners with an average time almost 9 minutes faster than 2016 and almost 12 minutes faster than 2017. However, *Wave* and *Year* are not only the variables that may factor into predicting an individual's marathon time. To get a better sense of how variables such as *Age* and *Gender* may affect an individual's marathon time, the below plot shows boxplots for each intersection of age division and gender, using age divisions designated by the BAA. This plot indicates that males have faster median times than females within every single age division. Additionally, for both males and females, there is a positive relationship between age and marathon times as older age groups correspond to higher median marathon times. This relationship, however, does not appear to be linear as the difference in median times between subsequent age groups gets larger as age increases. 


![](Final-Paper-Template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

Since Part II will investigate creating a model for only runners who participated in the Boston Marathon all three years, it is important to understand how the demographics of these runners differ from the population of all Boston Marathon runners. The below table compiled from 2017 Boston Marathon data shows the proportion of runners of each age division and sex for runners running in their 3rd consecutive Boston Marathon and for all runners. Runners in their 3rd consecutive marathon were more likely to be male and more likely to be in an age group 45-49 or older than the overall marathon population. 


<table class="table table-condensed">
 <thead>
  <tr>
   <th style="text-align:right;"> Age Division </th>
   <th style="text-align:right;"> Runners in 3rd Consecutive Marathon </th>
   <th style="text-align:right;"> All 2017 Boston Marathon Runners </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">18to39</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #288cee">20.25%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #1c86ee">40.21%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">40to44</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #65adf3">14.47%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #acd3f8">14.55%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">45to49</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #1c86ee">21.40%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #a0ccf7">16.72%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">50to54</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #4a9ef1">17.02%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #bbdaf9">12.00%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">55to59</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #6fb2f4">13.59%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #cfe5fb">8.46%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">60to64</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #afd4f9">7.55%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #e1effc">5.25%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">65to69</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #d5e8fb">4.01%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #f3f8fe">2.04%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">70to74</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #f1f7fe">1.35%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #fbfdfe">0.61%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">75to79</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #fdfefe">0.26%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #fefefe">0.14%</span> </td>
  </tr>
  <tr>
   <td style="text-align:right;"> <span style="font-weight: bold">80+   </span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #ffffff">0.10%</span> </td>
   <td style="text-align:right;"> <span style="display: block; padding: 0 4px; border-radius: 4px; background-color: #ffffff">0.03%</span> </td>
  </tr>
</tbody>
</table>

# RESULTS

**Part I**

**Which combination of predictors and the interactions between them produce the best model for predicting official time?**

In our approach to creating a model to predict an individual's marathon time, only variables that would be known prior to the start of the race were considered since a model predicting marathon time has the most value if it is used prior to the start of the race. Based on the available data from the official Boston Marathon results from 2015 to 2017, these variables were limited to Age, Gender, Year, and Wave. Since these four variables only provide a somewhat limited picture of an individual's running ability, including all possible interactions between these variables was certainly a priority in the process of determining a model to predict official time. To accomplish this goal, model selection was performed using a variation of the elastic net method in which cross validation was performed to find the optimal tuning parameters that would minimize the prediction error of our model. 

For five separate alpha values between 0 and 1, this process determined maximum lambda values within one standard deviation of the lamba value that would minimize our cross validation error at each respective alpha level. After generating these 5 combinations of alpha and lamba, we created the model for the combination of tuning parameters with the minimum cross validation error and included only predictors with non-zero estimate values. The below table displays all of the predictors included in this model and their non-standardized coefficient values. Since this model was created with almost 80000 observations determined by all finishers of the Boston Marathon from 2015 to 2017, a large proportion of the possible predictors yielded non-zero estimates via this process and thus were included in the final model. Somewhat notably, however, *Age* by itself as a possible predictor was not included in the final model, however, Age as an interaction with several other variables was included. The below plot compares the the model's predicted marathon time to an indivudual's actual marathon time. This plot raises some concerns about the model's viability due to the range of predicted marathon times being much smaller than the range of actual marathon times. 


<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<caption>All Predictors &amp; Their Coefficients in Model to Predict Boston Marathon Time</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Parameter </th>
   <th style="text-align:right;"> Estimate </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Int </td>
   <td style="text-align:right;"> -11458.7279245 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM </td>
   <td style="text-align:right;"> -15.5270634 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveFour </td>
   <td style="text-align:right;"> 59.4084345 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveThree </td>
   <td style="text-align:right;"> 15.5371207 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveTwo </td>
   <td style="text-align:right;"> 0.1367550 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Year </td>
   <td style="text-align:right;"> 5.7811228 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age:GenderM </td>
   <td style="text-align:right;"> 0.4163266 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age:WaveOne </td>
   <td style="text-align:right;"> 0.0383854 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age:WaveThree </td>
   <td style="text-align:right;"> 0.5876703 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age:WaveTwo </td>
   <td style="text-align:right;"> 0.3269087 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age:Year </td>
   <td style="text-align:right;"> 0.0000004 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM:WaveFour </td>
   <td style="text-align:right;"> -0.8757157 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM:WaveOne </td>
   <td style="text-align:right;"> 6.8741937 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM:WaveThree </td>
   <td style="text-align:right;"> -0.2432811 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM:WaveTwo </td>
   <td style="text-align:right;"> 4.1312580 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM:Year </td>
   <td style="text-align:right;"> -0.0052967 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveFour:Year </td>
   <td style="text-align:right;"> 0.0136457 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveThree:Year </td>
   <td style="text-align:right;"> 0.0000512 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveTwo:Year </td>
   <td style="text-align:right;"> 0.0049322 </td>
  </tr>
</tbody>
</table>

![](Final-Paper-Template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->


To gain further insight into the viability of this model, it is necessary to investigate the nature of the residuals for this particular model.  A histogram of the residuals reveals the residuals to be somewhat normally distributed although a slight right skew in the distribution is present. Additionally, a plot of the residuals compared to the predicted values appears to be mostly uncorrelated with most of the residuals centered around zero, but some extreme positive residual values are present. However, the below plot of the residuals compared to the actual marathon times raises several concerns to the viability of this model. The below plot clearly shows a very strong positive relationship between actual marathon time and the residuals, meaning that faster runners' times are being overpredicted and slower runners' times are being underpredicted by our model. This positive relationship between residuals and marathon times was also present in the models constructed by the other combinations of alpha and lambda with similar cross validation errors. 


![](Final-Paper-Template_files/figure-html/unnamed-chunk-5-1.png)<!-- -->


Given that this strong relationship between the residuals and actual marathon times is indicative that the model is a poor fit for the data, our focus shifted to determining if any confounding variables were present that may be contributing to this issue. However, based on our available data, the only known data that was not considered within our model concerned the State and Country of each individual. The below figure shows the relationship between the number of runners who participated in the Boston Marathon from 2015 to 2017 and their average marathon time for each US State. This figure reveals that Massachusetts had both a significantly higher number of participants and a significantly higher average time, while the other 49 states seem to converge to the non-MA US mean. Since the Boston Marathon occurs in Massachusetts, it makes sense that MA would have more runners, but the location of the marathon does not completely explain the significant difference in mean time. 


![](Final-Paper-Template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->


However, when the same process of creating the model is conducted without runners from Massachusetts, the cross validation error is slightly smaller largely due to the removal of some outlier marathon times ran by individuals from MA, but the same positive relationship between residuals and actual marathon times is still present. Instead, the discrepancy in Massachusetts's average time compared to the other 49 states is likely due to the fact that 71.63% of runners from Massachusetts are in Wave 4, compared to just 14.05% of non-Massachusetts runners. Runners in Wave 4 typically run much slower times, do not have a qualifying time, and are usually running to represent some charity. Most of these charities tend to be more regionally-based in the Boston area, which explains why a greater proportion of Massachusetts runners tend to be in Wave 4. Therefore, we cannot discern any geographical reason that explains the positive relationship in the residuals of our model. 

While the positive relationship between the actual marathon times and the residuals does not violate any core assumptions of multiple regression, this relationship is indicative that this model does not fit the data well. With no clear, underlying factors contributing to why this model tends to overpredict faster times and underpredict slower times, it is reasonable to conclude that the issue lies more within the scope of our possible predictors. Given the extremely wide range of times and the diversity of the population of runners in the Boston Marathon, we cannot produce an consistently accurate model to predict an individual's Boston Marathon time from 2015 to 2017 knowing only *Gender*, *Age*, *Wave*, and *Year*. It is possible that additional years of data or additional useful variables would be required to significantly improve this model.


**Part II**

**Can we better predict the finish time for runners in the Boston Marathon who also participated the two years prior?**


While the process of developing a reliable model in Part I was unsuccessful based on the available data, we are not limited to the same four variables for runners who have participated in the Boston Marathon multiple times. Instead, our focus shifted to developing a model for runners who ran in all three marathons from 2015 to 2017, using both their demographic data and their 2015 and 2016 performances to predict their 2017 Boston Marathon time. Potential predictors considered to predict 2017 marathon times included *Age*, *Gender*, *Wave*, *OfficialTime2015*, *OfficialTime2016*, *stDev2015*, *stDev2016*. The *stDev2015* and *stDev2016* variables were computed by determining the standard deviation of a runner's eight 5K intervals throughout the marathon of that particular year. The intent of this variable was to determine if a runner's demonstrated ability to maintain consistent pacing throughout an entire marathon may be indicative of their ability to run faster in subsequent years. Our model creation process for predicting the time of an individual's 3rd consecutive Boston Marathon considered all 1902 individuals who finished the Boston Marathon in 2015, 2016, and 2017 and recorded splits at every 5K interval in 2015 and 2016. 

Prior to model selection, the set of potential predictors was amended slightly to avoid potential issues of multicollinearity. *OfficialTime15* and *OfficialTime16* are highly correlated to each other, so they should not both be included within a set of predictors. The same issue would apply to be including both *stDev2015* and *stDev2016*. To diminish the risk of multicollinearity, *OfficialTime15* and *Official16* were averaged together to form *OfficialTime1516*, while *stDev2015* and *stDev2016* were also averaged together to form *stDev1516*. However, since the distribution of 2017 Boston Marathon times for all participants was more similar to the distribution of 2016 than 2015, simply averaging these values together may not be the best solution to create a predictor that will be most helpful to predict 2017 marathon times. Due to this reality, model selection was performed twice, once using the averages in set of possible predictors and once using only 2016 values of Official Time and standard deviation of 5K intervals.  

While the circumstances were different, the general model selection process to find a model predicting 2017 Boston Marathon times for those individuals who ran the Boston Marathon in 2015 and 2016 was similar to the process laid out in Part I. For five different alpha values, optimal lamda values were found, and of the five combinations, the combination with the lowest cross validation error was chosen. This process was repeated using 2016 values for official time and standard deviation of 5K intervals and then with the average of 2015 and 2016 values for official time and standard deviation. After finding the best model in each scenario, it was determined that the set of possible predictors with the averages of 2015 and 2016 yielded a model that fit the data slightly better and had a lower cross validation error. The below table shows the predictors with non-zero estimates included in the model. The two plots below shows the comparison of the predicted 2017 marathon times to the actual 2017 marathon times and the relationship between the residuals and the predicted 2017 Marathon times. 


<table class="table table-striped" style="margin-left: auto; margin-right: auto;">
<caption>All Predictors &amp; Their Coefficients in Model to Predict Boston Marathon Time of Repeat Participants</caption>
 <thead>
  <tr>
   <th style="text-align:left;"> Parameter </th>
   <th style="text-align:right;"> Estimate </th>
  </tr>
 </thead>
<tbody>
  <tr>
   <td style="text-align:left;"> Int </td>
   <td style="text-align:right;"> 87.5287866 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OfficialTime1516 </td>
   <td style="text-align:right;"> 0.5591830 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveFour </td>
   <td style="text-align:right;"> 3.7967574 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveOne </td>
   <td style="text-align:right;"> -3.6740197 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> WaveThree </td>
   <td style="text-align:right;"> 0.1898840 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OfficialTime1516:stDev1516 </td>
   <td style="text-align:right;"> 0.0052462 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OfficialTime1516:Age </td>
   <td style="text-align:right;"> 0.0017106 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OfficialTime1516:WaveFour </td>
   <td style="text-align:right;"> 0.0360890 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OfficialTime1516:WaveOne </td>
   <td style="text-align:right;"> -0.0115551 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> OfficialTime1516:WaveThree </td>
   <td style="text-align:right;"> 0.0069161 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> stDev1516:WaveOne </td>
   <td style="text-align:right;"> -0.7177609 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> GenderM:WaveOne </td>
   <td style="text-align:right;"> -1.4096861 </td>
  </tr>
  <tr>
   <td style="text-align:left;"> Age:WaveOne </td>
   <td style="text-align:right;"> -0.0307966 </td>
  </tr>
</tbody>
</table>

![](Final-Paper-Template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->![](Final-Paper-Template_files/figure-html/unnamed-chunk-7-2.png)<!-- -->


Upon first glance, it is apparent that this model does not have as many issues as the model created in Part I. First, this model seems to be a more accurate fit with the range of predicted marathon times for those running in their 3rd consecutive marathon being pretty similar to the range of actual marathon times for the same individuals. The plot of the model's residuals against the predicted marathon time does not reveal any distinct pattern in the residuals and shows most of the residuals centered around 0, although some more extreme positive residuals are present. Additionally, in a plot of residuals compared to actual marathon times, this model does not exhibit the same flaws as the model in Part I with regards to overpredicting faster times and underpredicting slower times. Further transformations of the predictors or possibly a weighted average of the 2015 and 2016 values may potentially be able to improve the viability of this model. However, this model certainly is both simpler and more accurate than the model found in Part I in predicting Boston Marathon times of those who also ran the Boston Marathon the two years prior. 


# CONCLUSION

Overall, this process of trying to developing a model to accurately predict Boston Marathon times for any individual proved to be difficult using conventional multiple regression and cross validation techniques, especially considering the diverse pool on entrants in the Boston Marathon. The model development process outlined in Part I leading to a very strong relationship between actual marathon times and the residuals emphasized this difficulty of predicting marathon times based on only demographic variables that could be extracted from the official Boston Marathon results, such as Age, Gender, and Wave. The model generated in Part II, aided by the presence of additional possible predictors, showed better success with being able to predict marathon times only for those who also participated in the two years prior. 

The model constructed in Part II could be useful to allow potential returning runners to the Boston Marathon to weigh whether or not they should run again based off their predicted time. Alternatively, their predicted time could also be used to provide with a time goal to meet should they decide to run the Boston Marathon again. Additional work could be done to improve this model by introducing data from additional years of the Boston Marathon outside of just 2015 to 2017. 

The model constructed in Part I certainly needs improvement before it can comfortably be used to produce potential time goal for a first time Boston Marathon runner. For runners who register for the Boston Marathon via a qualifying time, using their qualfying time as a possible predictor certainly would improve the viability of a model to predict their Boston Marathon time if qualifying time data was more readily available. Additionally, other inputs such as training duration or training frequency certainly could improve the model if that information could be reliably collected via a survery of Boston Marathon runners. If these additional predictors could be included and create a more reliable model to predict Boston Marathon times across a wider span of years, it would be interesting to investigate how this model would compare to models predicting times for other major marathons, such as New York or London. 








