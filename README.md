# python-analysis-netflix-viewing-activity

A python analysis of my netflix viewing activity.

## Objective

Clean, Transform, and analyze personal Netflix data.

## Data Collection

Data Source: My own Netflix data obtained by simply requesting a copy of my personal information via Netflix Account.

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
* On which days of the week have I watched Netflix the most?
* During which hours of the day do I most often start watching Netflix?
```
shows['Weekday'] = shows['Start Time'].dt.weekday
shows['Hour'] = shows['Start Time'].dt.hour
```
(repeat the above steps for 'movies' as well)

We have our final, clean datasets namely **shows** and **movies**, ready for analysis.

## Data Analysis

Key Takeaways:
* Over 2000 minutes were spent watching Brooklyn Nine-Nine, making it the most watched show in the history.
* Viewing activity is higher during the weekdays as compared to the weekends, especially between Tuesdays and Thursdays.
* There's zero activity between 1 AM and 8 AM throughout the day.
* The viewing activity peaks at 11 AM, 3 PM, 6 PM, and 7 PM.