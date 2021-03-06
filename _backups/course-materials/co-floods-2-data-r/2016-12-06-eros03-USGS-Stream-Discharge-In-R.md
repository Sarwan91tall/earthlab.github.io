---
layout: single
title: "Visualize Stream Discharge Data in R - 2013 Colorado Floods"
excerpt: "This lesson walks through the steps need to download and visualize
USGS Stream Discharge data in R to better understand the drivers and impacts of
the 2013 Colorado floods."
authors: ['Leah Wasser', 'NEON Data Skills', 'Mariela Perignon']
lastModified: 2016-12-14
category: [course-materials]
class-lesson: ['co-floods-2-data-r']
permalink: /course-materials/co-floods-USGS-stream-discharge-r
nav-title: 'Stream Discharge Data R'
sidebar:
  nav:
author_profile: false
comments: false
order: 5
---

Several factors contributed to the extreme flooding that occurred in Boulder,
Colorado in 2013. In this data activity, we explore and visualize the data for
stream discharge data collected by the United States Geological Survey (USGS).


<div id="objectives" markdown="1">

### Learning Objectives
After completing this tutorial, you will be able to:

* Download stream gauge data from <a href="http://waterdata.usgs.gov/nwis" target="_blank"> USGS's National Water Information System</a>.
* Plot precipitation data in R.
* Publish & share an interactive plot of the data using Plotly.

### Things You'll Need To Complete This Lesson
Please be sure you have the most current version of R and, preferably,
RStudio to write your code.

 **R Skill Level:** Intermediate - To succeed in this tutorial, you will need to
have basic knowledge for use of the R software program.

### R Libraries to Install:

* **ggplot2:** `install.packages("ggplot2")`
* **plotly:** `install.packages("plotly")`

### Data to Download
We include directions on how to directly find and access the data from USGS's
National National Water Information System Database. However, depending on your
learning objectives you may prefer to use the
provided teaching data subset that can be downloaded from the <a href="https://ndownloader.figshare.com/files/6780978"> NEON Data Skills account
on FigShare</a>.

To more easily follow along with this lesson, use the same organization for your files and folders as we did. First, create a `data` directory (folder) within your `Documents` directory. If you downloaded the compressed data file above, unzip this file and place the `distub-events-co13` folder within the `data` directory you created. If you are planning to access the data directly as described in the lesson, create a new directory called `distub-events-co13` wihin your `data` folder and then within it create another directory called `discharge`. If you choose to save your files
elsewhere in your file structure, you will need to modify the directions in the lesson to set your working
directory accordingly.

</div>

## Research Question
What were the patterns of stream discharge prior to and during the 2013 flooding
events in Colorado?

## About the Data - USGS Stream Discharge Data

The USGS has a distributed network of aquatic sensors located in streams across
the United States. This network monitors a suit of variables that are important
to stream morphology and health. One of the metrics that this sensor network
monitors is **Stream Discharge**, a metric which quantifies the volume of water
moving down a stream. Discharge is an ideal metric to quantify flow, which
increases significantly during a flood event.

> As defined by USGS: Discharge is the volume of water moving down a stream or
> river per unit of time, commonly expressed in cubic feet per second or gallons
> per day. In general, river discharge is computed by multiplying the area of
> water in a channel cross section by the average velocity of the water in that
> cross section.
>
> <a href="http://water.usgs.gov/edu/streamflow2.html" target="_blank">
For more on stream discharge by USGS.</a>

<figure>
<a href="{{ site.baseurl }}/images/disturb-events-co13/USGS-Peak-discharge.gif">
<img src="{{ site.baseurl }}/images/disturb-events-co13/USGS-Peak-discharge.gif"></a>
<figcaption>
The USGS tracks stream discharge through time at locations across the United
States. Note the pattern observed in the plot above. The peak recorded discharge
value in 2013 was significantly larger than what was observed in other years.
Source: <a href="http://nwis.waterdata.usgs.gov/usa/nwis/peak/?site_no=06730200" target="_blank"> USGS, National Water Information System. </a>
</figcaption>
</figure>



# Work with Stream Gauge Data

## R Packages

We will use `ggplot2` to efficiently plot our data and `plotly` to create interactive plots.


```r
# set your working directory
#setwd("working-dir-path-here")

# load packages
library(ggplot2) # create efficient, professional plots
library(plotly) # create cool interactive plots

# set strings as factors to false
options(stringsAsFactors = FALSE)
```

##  Import USGS Stream Discharge Data Into R

Now that we better understand the data that we are working with, let's import it
into R. First, open up the `discharge/06730200-discharge_daily_1986-2013.txt`
file in a text editor.

What do you notice about the structure of the file?

The first 24 lines are descriptive text and not actual data. Also notice that
this file is separated by tabs, not commas. We will need to specify the
**tab delimiter** when we import our data.We will use the `read.csv()` function
to import it into an R object.

When we use `read.csv()`, we need to define several attributes of the file
including:

1. The data are tab delimited. We will this tell R to use the `"/t"`
**sep**arator, which defines a tab delimited separation.
2. The first group of 24 lines in the file are not data; we will tell R to skip
those lines when it imports the data using `skip=25`.
3. Our data have a header, which is similar to column names in a spreadsheet. We
will tell `R` to set `header=TRUE` to ensure the headers are imported as column
names rather than data values.
4. Finally we will set `stringsAsFactors = FALSE` to ensure our data come in a
individual values.

Let's import our data.

(Note: you can use the `discharge/06730200-discharge_daily_1986-2013.csv` file
and read it in directly using `read.csv()` function and then skip to the **View
Data Structure** section).


```r

#import data
# discharge <- read.csv("disturb-events-co13/discharge/06730200-discharge_daily_1986-2013.txt",
#                        sep="\t",
#                        skip=24,
#                        header=TRUE)

discharge <- read.csv("disturb-events-co13/discharge/06730200-discharge_daily_1986-2016.txt",
                      sep="\t",
                      skip=26,
                      header=TRUE)
## Warning in file(file, "rt"): cannot open file 'disturb-events-co13/
## discharge/06730200-discharge_daily_1986-2016.txt': No such file or
## directory
## Error in file(file, "rt"): cannot open the connection

# view first 6 lines of data
head(discharge)
## Error in head(discharge): object 'discharge' not found
```

When we import these data, we can see that the first row of data is a second
header row rather than actual data. We can remove the second row of header
values by selecting all data beginning at row 2 and ending at the
total number or rows in the file and re-assigning it to the variable `discharge`. The `nrow` function will count the total
number of rows in the object.


```r
# nrow: how many rows are in the R object
nrow(discharge)
## Error in nrow(discharge): object 'discharge' not found

# remove the first line from the data frame (which is a second list of headers)
# the code below selects all rows beginning at row 2 and ending at the total
# number of rows.
discharge <- discharge[2:nrow(discharge),]
## Error in eval(expr, envir, enclos): object 'discharge' not found
```

## Metadata
We now have an R object that includes only rows containing data values. Each
column also has a unique column name. However the column names may not be
descriptive enough to be useful - what is `X17663_00060_00003`?.

Reopen the `discharge/06730200-discharge_daily_1986-2013.txt` file in a text editor or browser. The text at
the top provides useful metadata about our data. On rows 10-12, we see that the
values in the 5th column of data are "Discharge, cubic feed per second (Mean)".  Rows 14-16 tell us more about the 6th column of data,
the quality flags.

Now that we know what the columns are, let's rename column 5, which contains the
discharge value, as **disValue** and column 6 as **qualFlag** so it is more "human
readable" as we work with it
in R.


```r
# view column headers
names(discharge)
## Error in eval(expr, envir, enclos): object 'discharge' not found

# rename the fifth column to disValue representing discharge value
names(discharge)[4] <- "disValue"
## Error in names(discharge)[4] <- "disValue": object 'discharge' not found
names(discharge)[5] <- "qualCode"
## Error in names(discharge)[5] <- "qualCode": object 'discharge' not found

# view revised column headers
names(discharge)
## Error in eval(expr, envir, enclos): object 'discharge' not found
```

## View Data Structure

Let's have a look at the structure of our data.


```r
# view structure of data
str(discharge)
## Error in str(discharge): object 'discharge' not found
```

It appears as if the discharge value is a `character` (`chr`) class. This is
likely because we had an additional row in our data. Let's convert the discharge
column to a `numeric` class. In this case, we can reassign that column to be of
class: `integer` given there are no decimal places.


```r
# view class of the disValue column
class(discharge$disValue)
## Error in eval(expr, envir, enclos): object 'discharge' not found

# convert column to integer
discharge$disValue <- as.integer(discharge$disValue)
## Error in eval(expr, envir, enclos): object 'discharge' not found

str(discharge)
## Error in str(discharge): object 'discharge' not found
```


### Converting Time Stamps

We have converted our discharge data to an `integer` class. However, the time
stamp field, `datetime` is still a `character` class.

To work with and efficiently plot time series data, it is best to convert date
and/or time data to a date/time class. As we have both date and time date, we
will use the class POSIXct.

To learn more about different date/time classes, see the
<a href="{{ site.baseurl }}/R/time-series-convert-date-time-class-POSIX/" target="_blank" >
*Dealing With Dates & Times in R - as.Date, POSIXct, POSIXlt*</a> tutorial.


```r
# view class
class(discharge$datetime)
## Error in eval(expr, envir, enclos): object 'discharge' not found

# convert to date/time class - POSIX
discharge$datetime <- as.POSIXct(discharge$datetime)
## Error in as.POSIXct(discharge$datetime): object 'discharge' not found

# recheck data structure
str(discharge)
## Error in str(discharge): object 'discharge' not found
```

### No Data Values
Next, let's query our data to check whether there are no data values in
it.  The metadata associated with the data doesn't specify what the values would
be, `NA` or `-9999` are common values


```r
# check total number of NA values
sum(is.na(discharge$datetime))
## Error in eval(expr, envir, enclos): object 'discharge' not found

# view distribution of values
hist(discharge$disValue)
## Error in hist(discharge$disValue): object 'discharge' not found
summary(discharge$disValue)
## Error in summary(discharge$disValue): object 'discharge' not found
```

Excellent! The data contains no NoData values.

## Plot The Data

Finally, we are ready to plot our data. We will use `ggplot` from the `ggplot2`
package to create our plot.


```r
# check out our date range
min(discharge$datetime)
## Error in eval(expr, envir, enclos): object 'discharge' not found
max(discharge$datetime)
## Error in eval(expr, envir, enclos): object 'discharge' not found

stream.discharge.30yrs  <- ggplot(discharge, aes(datetime, disValue)) +
              geom_point() +
              ggtitle("Stream Discharge (CFS) - Boulder Creek, 1986-2016") +
              xlab("Year") + ylab("Discharge (CFS)")
## Error in ggplot(discharge, aes(datetime, disValue)): object 'discharge' not found

stream.discharge.30yrs
## Error in eval(expr, envir, enclos): object 'stream.discharge.30yrs' not found
```

#### Questions:

1. What patterns do you see in the data?
1. Why might there be an increase in discharge during that time of year?


## Plot Data Time Subsets With ggplot

We can plot a subset of our data within `ggplot()` by specifying the start and
end times (in a `limits` object) for the x-axis with `scale_x_datetime`. Let's
plot data for the months directly around the Boulder flood: August 15 2013 -
October 15 2013.


```r

# Define Start and end times for the subset as R objects that are the time class
startTime <- as.POSIXct("2013-08-15 00:00:00")
endTime <- as.POSIXct("2013-10-15 00:00:00")

# create a start and end time R object
start.end <- c(startTime,endTime)
start.end
## [1] "2013-08-15 MDT" "2013-10-15 MDT"

# plot the data - Aug 15-October 15
stream.discharge.3mo <- ggplot(discharge,
          aes(datetime,disValue)) +
          geom_point() +
          scale_x_datetime(limits=start.end) +
          xlab("Month / Day") + ylab("Discharge (Cubic Feet per Second)") +
          ggtitle("Daily Stream Discharge (CFS) for Boulder Creek 8/15 - 10/15 2013")
## Error in ggplot(discharge, aes(datetime, disValue)): object 'discharge' not found

stream.discharge.3mo
## Error in eval(expr, envir, enclos): object 'stream.discharge.3mo' not found
```

We get a warning message because we are "ignoring" lots of the data in the
data set.

## Plotly - Interactive (and Online) Plots

We have now successfully created a plot. We can turn that plot into an interactive
plot using **Plotly**. <a href="https://plot.ly/" target="_blank" >Plotly </a>
allows you to create interactive plots that can also be shared online. If
you are new to Plotly, view the companion mini-lesson
<a href="{{ site.baseurl }}/R/Plotly/" target="_blank"> *Interactive Data Vizualization with R and Plotly*</a>
to learn how to set up an account and access your username and API key.

### Time subsets in plotly

To plot a subset of the total data we have to manually subset the data as the Plotly
package doesn't (yet?) recognize the `limits` method of subsetting.

Here we create a new R object with entries corresponding to just the dates we want and then plot that data.


```r

# subset out some of the data - Aug 15 - October 15
discharge.aug.oct2013 <- subset(discharge,
                        datetime >= as.POSIXct('2013-08-15 00:00',
                                              tz = "America/Denver") &
                        datetime <= as.POSIXct('2013-10-15 23:59',
                                              tz = "America/Denver"))
## Error in subset(discharge, datetime >= as.POSIXct("2013-08-15 00:00", : object 'discharge' not found

# plot the data
disPlot.plotly <- ggplot(data=discharge.aug.oct2013,
        aes(datetime,disValue)) +
        geom_point(size=3)     # makes the points larger than default
## Error in ggplot(data = discharge.aug.oct2013, aes(datetime, disValue)): object 'discharge.aug.oct2013' not found

disPlot.plotly
## Error in eval(expr, envir, enclos): object 'disPlot.plotly' not found

# add title and labels
disPlot.plotly <- disPlot.plotly +
	theme(axis.title.x = element_blank()) +
	xlab("Time") + ylab("Stream Discharge (CFS)") +
	ggtitle("Stream Discharge - Boulder Creek 2013")
## Error in eval(expr, envir, enclos): object 'disPlot.plotly' not found

disPlot.plotly
## Error in eval(expr, envir, enclos): object 'disPlot.plotly' not found

# view plotly plot in R
ggplotly(disPlot.plotly)
## Error in ggplotly(disPlot.plotly): object 'disPlot.plotly' not found
```

If you are satisfied with your plot you can now publish it to your Plotly account.


```r
# set username
Sys.setenv("plotly_username"="yourUserNameHere")
# set user key
Sys.setenv("plotly_api_key"="yourUserKeyHere")

# publish plotly plot to your plotly online account if you want.
plotly_POST(disPlot.plotly)

```

## Additional Resources

Additional information on USGS streamflow measurements and data:

* <a href="http://nwis.waterdata.usgs.gov/usa/nwis/peak/" target="_blank">Find peak streamflow for other locations</a>
* <a href="http://water.usgs.gov/edu/measureflow.html" target="_blank">USGS: How streamflow is measured</a>
* <a href="http://water.usgs.gov/edu/streamflow2.html" target="_blank">USGS: How streamflow is measured, Part II</a>
* <a href="http://pubs.usgs.gov/fs/2005/3131/FS2005-3131.pdf" target="_blank"> USGS National Streamflow Information Program Fact Sheet </a>

## API Data Access
USGS data can be downloaded via an API using a command line interface. This is
particularly useful if you want to request data from multiple sites or build the
data request into a script.
<a href="http://help.waterdata.usgs.gov/faq/automated-retrievals#RT">
Read more here about API downloads of USGS data</a>.


***
Return to the
<a href="{{ site.basurl }}/teaching-modules/disturb-events-co13/detailed-lesson"> *Ecological Disturbance Teaching Module* by clicking here</a>.
