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
