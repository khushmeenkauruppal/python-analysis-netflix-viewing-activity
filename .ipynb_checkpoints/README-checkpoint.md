# python-analysis-netflix-viewing-activity

A python analysis of my netflix viewing activity.

## Objective

Clean, Transform, and analyze personal Netflix data.

## Data Collection

Data Source: My own Netflix data obtained by simply requesting a copy of my personal information via Netflix Account.

(Note: The data is recorded between May 1, 2024 and June 8, 2024 (5 weeks))

Data is stored in a csv file which is then loaded into a pandas dataframe.

## Data Cleaning

Examine the data, check for NA, and duplicates for the table ViewingActivity saved into pandas dataframe 'df'.

Filter out other profiles since the analysis is based on my Netflix viewing activity.
```
df['Profile Name'] = df['Profile Name'].astype(str)
df = df[df['Profile Name' ].str.contains('khush', case=False, na=False)]
```

Drop unnecessary columns which are irrelevant to the objective.
```
df.drop(['Profile Name', 'Attributes', 'Supplemental Video Type', 'Device Type', 'Bookmark', 'Latest Bookmark', 'Country'], axis=1, inplace=True)
```

Check the datatypes of the columns.
```
df.dtypes
```

Convert 'Start Time' and 'Duration' datatype from String to Datetime and Timedelta.
```
df['Start Time'] = pd.to_datetime(df['Start Time'], utc=True)
df['Duration'] = pd.to_timedelta(df['Duration'])
```

Also, change the timezone for 'Start Time' from UTC to US/Eastern for more understandibility.
```
df.set_index('Start Time', inplace=True)
df.index = df.index.tz_convert('US/Eastern')
df.reset_index(inplace=True)
```

Drop the records with short durations.
```
df = df[df['Duration'] > '00:02:00']
```

After examining the 'Title' column, it turns out to be too much information in just one column. Therefore, parse the 'Title' column to break it down into four different columns i.e 'Name', 'Season', 'Episode Name', and 'Episode #' for easy understanding.
```
df[['Name', 'Season', 'Episode Name', 'Episode #']] = df['Title'].str.extract('^(.*?): Season (\d+): (.*) \(Episode (\d+)\)$', expand=True)
```

Since there are movies as well as shows records, there are going to be null values for season and episode columns for movies. To overcome this, divide the dataframe 'df' into two dataframes namely, 'movies' and 'shows'.
```
movies = df[df['Name'].isnull()]
shows = df[df['Name'].notnull()]
```

Since movies data doesnot require 'Name', 'Season', 'Episode Name', and 'Episode #' columns, drop them.
```
movies.drop(['Name', 'Season', 'Episode Name', 'Episode #'], axis=1, inplace=True)
```

Now, add two new columns to each dataset i.e 'Weekday' and 'Hour' to answer questions like-
* **On which days of the week have I watched Netflix the most?**
* **During which hours of the day do I most often start watching Netflix?**
```
shows['Weekday'] = shows['Start Time'].dt.strftime('%a')
shows['Hour'] = shows['Start Time'].dt.hour
```
(repeat the above steps for 'movies' as well)

We have our final, clean datasets namely **shows** and **movies**, ready for analysis.

## Data Visualization

1. Total Watch Time per Show over the span of **5 weeks**

![Image](plots/plot1.png)
   
3. Watch Time per Day over the span of **5 weeks**

| Total | Number |
|---|---|
| ![Image](plots/plot2.png) | ![Image](plots/plot6.png) |

| Average |
|---|
| ![Image](plots/plot3.png) |


5. Watch Time per Hour over the span of **5 weeks**

| Total |
|---|
| ![Image](plots/plot4.png) |

| Number |
|---|
| ![Image](plots/plot7.png) |

| Average |
|---|
| ![Image](plots/plot5.png) |

## Data Analysis

Key Takeaways:
* What show did I watch the most in the last 5 weeks?

  Brooklyn Nine-Nine is the most watched show with almost 46 Hours of watch time in the last 5 weeks.

* On which days of the week have I watched Netflix the most?

  When we look at the total number of hours of viewing activity, Tuesday to Thursday tops the list by a significant amount but when we look at the average the margin of difference decreases. This can be explained by looking at the number of times viewing started per day which is as high as 34 for days like Tuesdays and as low as 17 for days like Saturdays, thus, making the average almost the same throughout the week despite the huge difference in the total number of hours watched for each day. In conclusion, I tend to watch Netflix more during the weekdays, especially between Tuesday and Thursday as compared to the weekends.
  
* During which hours of the day do I most often start watching Netflix?

  The viewing activity peaks at 3PM, 6PM, and 7PM, with a total of 5+ hours over the last 5 weeks for each time of the day. 