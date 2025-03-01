# Paper Replication Assignment 2: Make Figure 1

In this assignment, you will use the filter, mutate, group_by, and
summarize functions to create Figure 1 in the paper “Defensive
Investments and the Demand for Air Quality: Evidence from the NOx Budget
Program.”

<img src="Fig1.JPG" data-fig-align="center" width="377" />

## Part 1: Prepare data and packages

Step 1: Use the **library** function to declare that you will use the
**tidyverse** package in this session. **(1 point)**

Step 2: The script below uploads data from year 2001 into the RStudio
environment. You just need to run it.

``` r
df2001<-read.csv("EPA AMPD/emission_2001.csv")
```

Step 3: Upload data from year 2002, 2005, 2006, and 2007 into the
RStudio environment. Combine data from year 2001, 2002, 2005, 2006, and
2007 into one dataframe. Call the new data frame **df**. **(2 points)**

Step 4: Run the code chunk below and answer Question 1.

``` r
df2<-df %>% 
  mutate(NOx_emit=ifelse(is.na(NOx..tons.),0,NOx..tons.))
```

#### Question 1: Fill in the blank (3 points)

This step creates a new column called NOx_emit that replaces \_\_**\_**
values in the column \_\_**\_** with \_\_**\_**.

## Part 2: Select observations from regulated states

Step 1: In this step, you will identify states that were regulated by
the NOx Budget Program. First, you will create a new dataframe called
**df3** that contains only observations where the column **Program.s.**
is equal to **“ARP, NBP”**

(**1 point)**

Step 2: The script below creates a vector of unique states in df3. You
just have to run it.

``` r
nbp_states<-unique(df3$State)
```

Step 3: The script below keeps only states regulated states in df4. You
just have to run it.

``` r
df_nbp<-df2 %>%
  filter(State %in% nbp_states)
```

## Part 3: Prepare data for the plot

#### Question 1: What is the x-axis in Figure 1? (1 point)

#### Answer:

#### Question 2: What is the y-axis in Figure 1? (1 points)

#### Answer:

Step 2: Before finding the average daily total NOx emissions, we need to
the total monthly NOx emissions. Create a new dataframe called
**df_nbp2** from **df_nbp**. Use the **group_by** and **summarize**
functions to find the total NOx emissions for each month and year. Name
the new column **NOx_emit**. **(2 points)**

Hint: You learned how to do this in [this
video](https://campus.datacamp.com/courses/introduction-to-the-tidyverse/grouping-and-summarizing?ex=5)
in DataCamp.

Step 3: We need to estimate the average daily NOx emissions from monthly
NOx emissions data. To do this, we will divide total NOx emissions in
months with 31 days by 31, divide NOx emissions in months with 30 days
by 30, and divide NOx emissions in February with 28.

Step 3.1: Create a new dataframe called **df_nbp3** from **df_nbp2**.
Create a new column called **NOx_daily** equal to **NOx_emit/31** if the
month is January, March, May, July, August, October, or December.
Otherwise, **NOx_daily** is equal to NA. **(4 points)**

Step 3.2: Create a new dataframe called **df_nbp4** from **df_nbp3**.
Create a new column called **NOx_daily** equal to **NOx_emit/30** if the
month is April, June, September, or November. Otherwise, **NOx_daily**
is equal to **NOx_daily**. **(4 points)**

Step 3.3: Create a new dataframe called **df_nbp5** from **df_nbp4**.
Create a new column called **NOx_daily** equal to **NOx_emit/28** if the
month February. Otherwise, **NOx_daily** is equal to **NOx_daily**. **(3
points)**

Step 4: We will create a new column representing the day of the year.

First, we will create a **date** column representing each month’s first
day.

We then use the **as.Date** function to convert the **date** column from
a character format to a date format.

We then use the **mutate** and **yday** functions to create a new column
called **doy** that represents the day of the year.

This is shown in the script below. You just have to run it.

``` r
df_nbp6<-df_nbp5 %>%
  mutate(date=paste("01",as.character(Month), as.character(Year))) %>%
  mutate(date=as.Date(date, format="%d %m %Y")) %>%
  mutate(doy=yday(date))
```

Step 5: Use the mutate and ifelse functions to create a new column
called **phase**. This variable will indicate the period before the
implementation of the NBP or during the implementation of NBP.

Create a new dataframe called **df_nbp7** from **df_nbp6**. If **Year**
is greater than or equal to 2005, then **phase** will equal
**“2005-2007”**. Otherwise, **phase** will equal **“2001-2002”**. **(3
points)**

Step 6: Create a new dataframe called **df_nbp8** from **df_nbp7**.Use
the **group_by** and **summarize** function to find the **average**
value of **NOx_daily** for each day of the year (represented by the
variable **doy**) and implementation phase (represented by the variable
**phase**). **(2 points)**

Step 7: Create a new dataframe called **df_nbp9** from **df_nbp8**.

Use the **mutate** function to create a new variable called **NOx_th**
that is equal to **NOx_daily** divided by 1000.

In addition, use the **mutate** and **as.factor** function to convert
the **phase** column in to a class factor. **(2 points)**

## Part 4: Make the plot

Step 1: Use the **ggplot** function to create a line graph. The graph
should have two lines, each showing the annual average NOx emissions
from each state before and after the implementation of the NOx Budget
Program. The graph should look like or similar the one below. **(4
points)**

<img src="Fig1_rep_draft.png" data-fig-align="center" width="500" />

Hint: You learned how to do this in [this
video](https://campus.datacamp.com/courses/introduction-to-the-tidyverse/types-of-visualizations?ex=1)
in DataCamp.

Step 2: The script below modifies the aesthetics of the plot that you
have created to match the published paper. You just have to run it.

``` r
df_nbp9$phase<-relevel(df_nbp9$phase,"2005-2007")

ggplot(data=df_nbp9, aes(x=doy, y=NOx_th, group=phase)) +
  geom_line(aes(linetype=phase), color="blue")+theme_bw()+
  xlab("Day of year")+
  ylab("Avg. Daily NOx Emissions in NBP States (Thousand Tons)")
```

#### Question 3: Why did NOx decrease during the middle of each year? (1 point)

Hint: You can find the answer in the Introduction section of [this
paper](https://pubs.aeaweb.org/doi/pdfplus/10.1257/aer.20131002).

#### Answer:

#### Question 4: Why is the line plot you created smoother than the original plot in the paper? (1 point)

Hint: Think about the frequency of the data.

#### Answer:

Step 3: Remove all lines of script with **\#\| eval: false** which
prevents the line from running when rendered. Afterward, click Render to
generate an html file of this document. Click Render to generate an html
file of this document. **(1 point)**

You have reached the end of the assignment. Save the Quarto document and
push the completed assignment back into the GitHub repository.
