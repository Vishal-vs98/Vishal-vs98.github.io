Sales Conversion Optimisation for XYZ using R
================

**Social Media**

Can you imagine life without Facebook,
Instagram, WhatsApp, Snapchat, and Youtube, etc.? — I know I cannot.

We know the significance of social media in our personal lives but where
does social media stand in the business world?

**Interesting Facts**

As of October 2021, there were 4.55 billion active social media users or
a whopping 57.6% of the total global population. 
Facebook has been the
social media market leader for a very long time now and more than 79% of
marketing professionals disclosed using paid advertisement on Facebook
(Hubspot, 2021).

**Task**

Let’s help XYZ company by analyzing its Facebook advertisement campaign
data (taken from Kaggle) and offering actionable insights using R to
optimize marketing effectiveness for their future campaigns.

*This is the link to the source of this* ***dataset*** -
<https://www.kaggle.com/loveall/clicks-conversion-tracking>

**We will be answering the following business questions -**

1.  How can XYZ optimize the social ad campaigns to attain the *highest
    conversion rate* possible?

2.  what are the perfect *target demographics* with the appropriate
    *click through rate* (CTR) for XYZ’s marketing campaigns?

3.  What is the ideal *turnaround/decision making time* per age group to
    convert and re-target future social campaigns?

4.  Among all the marketing campaigns of XYZ, which one was the *most
    creative campaign*? (so that XYZ can re run the most creative
    campaign with adjusted audience)

*We will be performing exploratory data analysis and using K-Prototype
unsupervised learning algorithm to solve XYZ’s business problem.*

**Data Preparation and Libraries**

We will be using the following libraries to analyse XYZ’s marketing
campaigns -

``` r
library(dplyr)
library(tidyr)

library(clustMixType)       # For clustering analysis

library(ggplot2)
library(ggcorrplot)
library(purrr)
library(GGally)
library(patchwork)
```

Loading the data in R and taking a glimpse of the same. This data
consists of 11 variables and 1143 observations.

    ## Rows: 1,143
    ## Columns: 11
    ## $ ad_id               <int> 708746, 708749, 708771, 708815, 708818, 708820, 70~
    ## $ xyz_campaign_id     <int> 916, 916, 916, 916, 916, 916, 916, 916, 916, 916, ~
    ## $ fb_campaign_id      <int> 103916, 103917, 103920, 103928, 103928, 103929, 10~
    ## $ age                 <chr> "30-34", "30-34", "30-34", "30-34", "30-34", "30-3~
    ## $ gender              <chr> "M", "M", "M", "M", "M", "M", "M", "M", "M", "M", ~
    ## $ interest            <int> 15, 16, 20, 28, 28, 29, 15, 16, 27, 28, 31, 7, 16,~
    ## $ Impressions         <int> 7350, 17861, 693, 4259, 4133, 1915, 15615, 10951, ~
    ## $ Clicks              <int> 1, 2, 0, 1, 1, 0, 3, 1, 1, 3, 0, 0, 0, 0, 7, 0, 1,~
    ## $ Spent               <dbl> 1.43, 1.82, 0.00, 1.25, 1.29, 0.00, 4.77, 1.27, 1.~
    ## $ Total_Conversion    <int> 2, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,~
    ## $ Approved_Conversion <int> 1, 0, 0, 0, 1, 1, 0, 1, 0, 0, 0, 0, 0, 0, 1, 1, 0,~
    
**Feature Engineering**

**Step 1 - Converting variable type**

There are two character variables *‘age’* and *‘gender’* in the data.
Lets keep these columns unchanged for simpler graphical analysis and
*create a numeric version of age variable* for clustering analysis or
unsupervised learning.

The variables *‘interest’* and *‘xyz\_campaign\_id’* are of numerical
type in the data but they must be *converted into categorical type* as
they represent a group of characteristics. The other 2 ID variables
(ad\_id & fb\_campaign\_id) are also supposed to be categorical but
before converting it, we will be checking whether we should keep them in
the data for further analysis or not.

**Step 2 - Removing irrelevant variables**

Lets check whether the id columns have any characteristics which could
help in our analysis or not.

xyz\_campaign\_ID has *3 unique categories* while the other 2 ID
variables have too many unique values to extract any feature out of it.
So, we will be *removing* the other 2 ID’s from the data.

**Step 3 - Treating missing values**

Lets take a look at the *statistical summary* of the data set and
check whether the data has *missing values* to be treated or not.

    ##  xyz_campaign_id gender     interest    Impressions          Clicks      
    ##  916 : 54        F:551   16     :140   Min.   :     87   Min.   :  0.00  
    ##  936 :464        M:592   10     : 85   1st Qu.:   6504   1st Qu.:  1.00  
    ##  1178:625                29     : 77   Median :  51509   Median :  8.00  
    ##                          27     : 60   Mean   : 186732   Mean   : 33.39  
    ##                          15     : 51   3rd Qu.: 221769   3rd Qu.: 37.50  
    ##                          28     : 51   Max.   :3052003   Max.   :421.00  
    ##                          (Other):679                                     
    ##      Spent        Total_Conversion Approved_Conversion     Age           
    ##  Min.   :  0.00   Min.   : 0.000   Min.   : 0.000      Length:1143       
    ##  1st Qu.:  1.48   1st Qu.: 1.000   1st Qu.: 0.000      Class :character  
    ##  Median : 12.37   Median : 1.000   Median : 1.000      Mode  :character  
    ##  Mean   : 51.36   Mean   : 2.856   Mean   : 0.944                        
    ##  3rd Qu.: 60.02   3rd Qu.: 3.000   3rd Qu.: 1.000                        
    ##  Max.   :639.95   Max.   :60.000   Max.   :21.000                        
    ##                                                                          
    ##  Age_grp_proxy  
    ##  Min.   :32.00  
    ##  1st Qu.:32.00  
    ##  Median :37.00  
    ##  Mean   :38.32  
    ##  3rd Qu.:42.00  
    ##  Max.   :47.00  
    ## 

Created and used a function to find na values in each of the variables
if present.

Luckily there are *no missing values* to be imputed in the data. So,
Lets do a *preliminary analysis* to look for correlation between
variables in the data.

![HeatMap to show correlation between variables](/images/HeatMap.png)

***Correlation between variables***

From the above pairwise correlation heat map it can be observed that
there is *high positive correlation* between the following variables -

1.  Impressions and (Clicks,Spent,Total Conversion).
2.  Clicks and Spent.
3.  Approved Conversion and Total Conversion.

*Strong correlation between these variables is a positive sign!*

**Answering Business Questions-**

**Business Question 1**

How to optimize the social ad campaigns for the *highest conversion
rate* possible? (Attain best Reach to conversion ratios/click to
conversion ratios)

**Answer**

Lets create certain metrics to judge the performance of marketing
campaigns, The metrics which we will be using are -

1.  *Click through rate* (CTR) = Clicks/Impressions multiplied by 100                                                                                                             (Number of viewers who ended up clicking on the advertisement)
2.  *Total Conversion Rate* (TCR) = Total Conversions/Clicks multiplied
    by 100                                                                                                                                                                         (The percentage of viewers who clicked on the ad eventually made an enquiry about the product)
3.  *Approved Conversion Rate* (ACR) = Approved Conversions/Clicks
    multiplied by 100
    (The percentage of enquiries were converted into sale)
    
*Task*

*K-Prototype clustering algorithm* would be ideal to find out the
*customer segments* that the business should target to maximize its
*Click to Conversion ratio* as the cluster features
(“Age\_grp\_proxy”,“gender”,“Impressions” and “interest”) consists of
categorical as well as numeric variables.

Lets build the Segments using k-prototype unsupervised learning model
and we will be plotting the sum of within squares to determine the
optimum number of clusters.

We can determine the optimum number of clusters for this data set using
the *elbow method*. 

![scree plot](/images/qplot0.png)

In the above graph elbow is formed when *4* clusters
are created, so we will be dividing the data into *4 segments* using
k-prototype clustering.

The following table shows the cluster prototypes or the characteristics
for each clusters.

![](/images/cluster-prototype.png)

The above table shows characteristics of each clusters or the modes for
each variables.

To find out which cluster has the *best performance* lets compare the
metrics for each cluster.

Lets start with comparing the amount paid to facebook to show advertisement 
and click through rates for each customer segment.

![Metrics comparison between clusters](/images/spentvsCTR.png)

*Analysis-*

Amount *paid* to Facebook for showing advertisements to *segment 2* is
the *lowest*.

Now, lets compare the conversion rate between clusters.

![Metrics comparison between clusters](/images/conversion-rate.png)

*Analysis-*

Clicks to Total /Approved conversion ratio is the highest for *segment
2*.

***Bottom Line*** :- By targeting *segment 2*, which represents *“Male”*
viewers that have interest code “10” and belonging to the age group of
*“35-39”*, they can attain the *best conversion rate possible*, also, it
would be economical because the amount spent to show ads for this
segment is comparatively less than the other segments.

**Business Question 2**

Finding the perfect *target demographic*s with the appropriate click
through rates (CTR)

**Answer**

Age, Gender and interest are some of the demographic characteristics for
which the data is available to us.

Lets Compare the *click through rates* of the demographic variables to
understand the perfect target demographics with bar graphs.

Lets start by understanding the percentage of viewers who clicked on the 
advertisement for each age group.

![Comparison of CTR between age groups](/images/CTR-Age-Group.png)

*Comparison of Click Through Rates between Age Groups*

In the above bar graph it can be observed that with an increase in Age
the Click through rate also increases; Age group *30-34* and *45-49*
have the *lowest* and the *highest* CTR respectively.

Now, Lets compare the percentage of viewers who clicked on the 
advertisement between male and female audience.

![Comparison of CTR between Genders](/images/CTR-Gender.png)

*Comparison of Click Through Rates between Genders*

In the above bar graph it can be observed that *females* have
significantly higher Click through rate when compared to males.

Finally, lets understand the the percentage of viewers who clicked on the 
advertisement for each interest category.

![Comparison of CTR between interest codes](/images/CTR-Interest-code.png)

*Comparison of Click Through Rates between interest codes*

In the above graph, it can be seen that the high number of interest
categories makes it difficult to draw a comparison but interest code 26
and 113 has the highest and the lowest click through rate respectively.

Choosing a cutoff click through rate value to select optimal interest
codes would be a subjective call. However, if we choose the cutoff CTR
to be 0.020 or 2% then targeting audience with interest codes
*26,65,106,27,23,25* and *63* would be an optimal strategy.

***Bottom Line*** :- The perfect target demographics with the
appropriate Click through rates would be when the target audience are
**females** belonging to the **age group of 45-49** having either of the
following interest codes **26,65,106,27,23,25 and 63**.

**Business Question 3**

Understanding the ideal *turnaround/decision making time* per *age
group* to convert and re-target future social campaigns.

**Answer 3**

The decision making time of each age group can be analysed by
understanding the number of clicks each age group takes before making
enquiries and how many of those clicks are eventually converted.

*Task*

Now we will graphically analyse the age group wise click through rate
and Conversion rate data together for easy comparison of
turnaround/decision making time between various age groups.

![Metrics comparison age group wise](/images/Metrics-Age-Group.png)

*Comparison of Click through rate and conversion rate between age
groups*

*Analysis -*

In the above figure, an interesting relation between the metrics and the
age groups can be observed. As the *Age* of the viewers *increases*
their *CTR also increases* but the *conversion rates falls*.

Lets understand the decision making time of the following Age groups and
extract deeper ***insights*** -

**Age Group ‘30-34’**

This Age group has the lowest median click through rate but they make an
enquiry shortly after watching the advertisement (high relative Total
Conversion Count). Also, they are approximately twice as fast as all the
other age groups in making the purchase decision (high relative Approved
Conversion Count). 
They can be categorised as ***impulsive buyers*** or
the age group with comparatively low turnaround time.

**Age Group ‘45-49’**

In Contrast to the age group of 30-34, the Viewers in this age group
have the highest median click through rate but their total conversion
count is significantly lower than that of the age group 30-34, Which
implies that they take more time before making an enquiry even if they
found the advertisement attractive (they are comparatively more
cautious). 
Also, the low relative approved conversion rate indicates
that they take decisions with ***deliberation***, because of which they
tend to withhold their buying decision many times.

***Bottom Line :-*** Age group *‘30-34’* and *‘45-49’* have relatively
*lowest* and *highest* decision making time respectively. Also, With Age
the decision making time increases.

**Business Question 4**

Comparing the individual campaign performance so the *best
creative/campaign* can be run again with adjusted audiences.

**Answer**

To understand which campaign performed the best in terms of creativity,
we will be comparing the *click through rates* and *total conversion
rates* which are a good *indicator* of persuasiveness of a campaign .

*Task -*

Lets plot a bar graph to compare Click through rates and conversion
rates for each campaign together.

![Metrics comparison between campaigns](/images/CR-Campaign.png)

*Analysis -*

In the above graph, It can be seen that campaign 1178 and 936 have the
least and the most click through rate (CTR) respectively. Also, campaign
936 has a CTR similar to campaign 916’s CTR.

It can also be seen that campaign 1178 and 916 have the least and the
most Conversion rate (CR) respectively. On Comparison, It is clear that
even though campaign 936 has the highest CTR but it is insignificantly
higher than campaign 916’s CTR. On the other hand, Campaign 916 has a CR
which is significantly higher than the other campaigns.

***Bottom Line :-*** Campaign **916** is the most creative campaign
overall as it has the *highest combined Click through rate and
conversion rate value*.

**Conclusion**

After analyzing XYZ’s campaigns it can be concluded that campaign 916 is
the most creative campaign and with an increase in age the decision
making time also increases. Also, females of the age group ‘45-49’ have
the highest click rates.

To attain the best conversion rate possible XYZ should target (segment
2) male audience of the age group ‘35-39’.
