# 2025NFLDraft

## Introduction

When I was a teaching assistant for the Case Western Reserve University bootcamp in 2019, I was so inspired by our prospective students that are going into careers in the world of data.  I made the decision to tackle a project and see how far I can get and what can I learn.  There is a project in my GitHub that took me through the process of how I was able to use Python to predict whether or not someone will get drafted.

https://stevethomastri.github.io/nfldraft/

The process is relatively the same, but I was really shocked when the model's accuracy, my accuracy in 2019, and my accuracy now were somewhere in the 69.6% range.  How is that possible?  Here is where the process begins.

## The Process

### Gathering the Data

File: Data Acquisition.ipynb

For this project, I used the Jupyter lab to construct the notebook.  I had to import the following libraries.
- csv - to save the csvs in a folder, just in case I need to go back and check what I have done.
- pandas - this library is for data wrangling
- np - Python's number package to handle NaNs
- selenium - to web scrape the combine data from the website https://www.pro-football-reference.com/draft/

1. Testing to make sure that I can get the data from the website.  My thinking is I just need to see if i can get the 2024 data and then come up with a way to get 20 years worth, starting from 2000.  
-  When you visit the website, the chart has the following column headers:
    - Player
    - Pos
    - School
    - College
    - Ht
    - Wt
    - 40yd
    - Vertical
    - Bench
    - Broad Jump
    - 3Cone
    - Shuttle
    - Drafted (tm/rnd/yr)

The initial question that I have is:

# Will the prospect be drafted in the NFL, if they participated in one combine event?

To get the practice table, I used the read.html function from pandas.  I used selenium to go to the website.  Then I took a snapshot of the current state.  I had to find the table using the find_by_css function and use the css to get the table.

Then, I used a for loop to get all of the years i needed.  Good news is that the urls are in a similar format.  Every time the chromedriver program goes to the next page and gets the table, I concatenated it using the pd.concat function.  Pandas stacks the previous table on top of the new table, so long as they have the same columns.  Once this is done, we got all of the prospects that were either drafted or not from 2000-2024, a total of 8473 rows.  Notice that I added the column "Year" when scraping.  I might need that because I will be replacing the Drafted column.

### Data Wrangling

There was a column that I dropped "College".  This links to the prospects College Stats, which at this point, I do not need for the first model.  Because I want to keep prospects and test those that have competed in at lest one combine event, I dropped all rows that showed NaN for all 6 events.  

Changing the Drafted (tm/rnd/yr) to isDrafted

I do nott care in what round they are drafted, although it is good idea for a future project.  I changed the column header to isDrafted.  If there is information in a cell, the value changes to 1, otherwise 0.  Basically True or False.  This is where the loc function comes into play.  It filters based on whether the cell is NaN.  Then eveything else becomes a 1.  

When the scraping happens, there were some rows that had the headings repeated, so thos rows needed to be dropped as well.  Also, if a row doesn't have a height or a weight, that will needs to be dropped.  We might need those numbers to make our mode.  So to recap, They must have a height and weight recorded, and also compete in at least one combine event.  

At this point, there were 7949 rows, so I just did a count on all of the rows that have a hypnen in their height and it verified to the exact number.  As a checkpoint, i made the following file:

File: drafted_2000_2024-prospectswithnoheightremoved.csv

This has been fun project so far.  There is a lot more exploring to do and a lot of directions of where this could go next.  For now, let's get our model of Drafted(Y/N). :blush:

The next step is to turn the height into inches.  Once, i did that, I made another checkpoint file

File: drafted_2000_2024-turnedheightintoinches.csv

About 14% of this dataset are wide receivers.  Every NFL team needs people to catch the ball as well.  In order to do proper modeling.  I had to turn the following columns into numerical using pd.to_numeric:
- Ht
- Wt
- 40yd
- Vertical
- Bench
- Broad Jump
- 3Cone
- Shuttle
- isDrafted

For some exploratory data analysis, I grouped positions and if they were drafted and looked at height and weight as examples.  If you want to be a tight end in the NFL, shoot to weight between 225 lbs and 278 lbs.  Got to also be able to block.  

Now just in case you want to make a decision on what schools to go to for your best chance to be drafted.  I have included a file

College drafted.xlsx

I would be looking at schools that have a high count.  For example, Alabama has a 78% rate of being accepted in the NFL.  This is out of 176 players.  It is also an SEC school.  A question could be are more SEC schools getting drafted than big ten schools?

Also part of data wrangling, there are some schools that have St. and some that have State.  We need to be consistent with what we will be using, so we decide to use State.  Also, Positions S and SAF are the same position, so we will need to standardize these as well.  I chose to use the S for safety.  Once these have been standardized, we made another file:

File: drafted_2000_to_2024_final_to_prepare_for_first_model.csv

Now we are ready to create the first model

### Our First Model

File: Data First Model.ipynb

In this section, we are now ready to make predictions and see what percentage of accuracy that we can get.  We are using the numpy and the pandas libraries.  Now we have added two new libraries.

- xgboost - called "eXtreme Gradient Boosting" using decision trees and classification.  This is where it creates a decision tree, learns from its mistakes, and tries to correct the errors to become more accurate.
- sklearn - provides tools for machine learning and statistical modeling.  we will be using test_train_split and accuracy score.

We will read the CSV of drafted_2000_to_2024_final_to_prepare_for_first_model.csv as a starting point for our model

At this point we will be dropping the following columns and only leaving the numeric columns.

- Unnamed: 0
- Player
- Pos
- School
- Year

We are separating them into Festures and our Target column "isDrafted".  To test the data, we will be using 30%.

The process is:

1. We define our algorithm
2. We fit our training data to the model
3. We make predictions using our testing data
4. We evaluate our model based on test predictions

Our Initial accuracy is 68.64%.  This is based on our max_depth having None
With our max_depth being 3, our accuracy rose to 69.52%.  That becomes our model that we will be using to predict out 2025 Prospects Data.

### 2025 Prospects

We will use the same method as we did to get the historical data.  When we do all of the coeaning, you should get 247 rows total.  Once this, we did a checkpoint file called

File: prospects2025.csv

We used this file to drop the necessary columns.  Then we got all of the predictions from that model and put those in the file:

File: 2025 predictions.csv

I watched the predictions with baited breath.  Would this time be the time that we get over 70%.  Well..no, but pretty close with a 69.6% accuracy or 172/247.  That is on par with what the accuracy of the model and the 2019 accuracy.  

### Next Steps and Questions

Now that we got our results, what can we learn from this process.  You realy have to get your hands dirty and clean the data.  it is very nice to go through the journey of using what you have learned and looking towards the future of improving upon the model.  Here are some questions to consider:

- Feature Engineering?  What would we add?  College Data, Awards, etc.?
- Does being in a conference determine whether you are more likely to draft in the NFL?
- We know that different positions involve different skills, would it be best to split the models based on positions?
- How about those that didn't participate in a combine event?  What percentage got drafted and what can we use to boost the accuracy numbers up?  What factors can we use for those that didn't participate in a combine event?

There might be more questions coming as this is an ongoing project in preparation for the 2026 NFL Draft.  