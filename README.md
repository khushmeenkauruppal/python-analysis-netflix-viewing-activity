# python-analysis-netflix-viewing-activity
A python analysis of my netflix viewing activity.

### Objective

### Data Collection
Data Source: My own Netflix data obtained by simply requesting a copy of my personal information via Netflix Account.

Data is stored in a csv file which is then loaded into a pandas dataframe.

### Data Cleaning
Examine the data, check for NA, and duplicates for the table ViewingActivity saved into pandas dataframe 'df'.
```

#insert code

```
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

df.set_index('Start Time', inplace=True) # change 'Start Time' column into df's index
df.index = df.index.tz_convert('US/Eastern') # convert timezone
df.reset_index(inplace=True) # reset the index so 'Start Time' becomes a column again

```

### Data Analysis

### Data Visualization
