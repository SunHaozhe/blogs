---
layout: post
category: Python
title: Pandas memo
tagline: by SunHaozhe
tags: 
  - Python
  - Pandas
  - memo
  - Machine learning
mathjax: true
comments: true
published: true
---


Read dataset and reset index in place:

```python
df = pd.read_csv("train.csv")
df.set_index("id", inplace=True)
```

Save dataset set a csv file 

```python
df.to_csv("blabla.csv")
df.to_csv("blabla.csv", sep="\t", encoding="utf-8")
```

Visualize DataFrame:

```python
print(df.info())
df
```

Convert `object` (string) columns to `Categorical` columns:

```python
categorical_cols = ["...", "...", "..."]
df.loc[:, categorical_cols] = df.loc[:, categorical_cols].astype("category")
```

Convert `float` columns to `int` columns (as long as there are no missing values):

```python
int_cols = ["...", "...", "..."]
df.loc[:, int_cols] = df.loc[:, int_cols].astype("int")
```

Convert Categorical columns into dummy columns:

```python
df = pd.get_dummies(df, drop_first=True)
# drop_first: whether to get k-1 dummies out of k categorical 
# levels by removing the first level.

df = pd.get_dummies(df, columns=["...", "..."])
# If columns is None (default) then all the columns with 
# object or category dtype will be converted.
```

Count null values of each column:

```python
df.isnull().sum() 
```

Select rows with one or more null values: 

```python
df.loc[df.isnull().any(axis=1)] 
```

Fill missing values:

```python
df["name"].fillna("unknown", inplace=True)
df["age"].fillna(df["age"].mean(), inplace=True)
df["A"].fillna(method="ffill", inplace=True)
```

Remove missing values:

```python
df.dropna(axis=0, how="any", inplace=True) # drop rows
df.dropna(axis=1, how="any", inplace=True) # drop columns
```

Remove columns/features:

```python
df.drop(["...", "...", "..."], axis=1, inplace=True)
```

Remove duplicate rows of `DataFrame`:

```python
df.drop_duplicates(subset=None, keep='first', inplace=True, ignore_index=True) 
```

Add interaction term:

```python
df["C"] = df["A"] * df["B"]
```

Reset the index to the default integer index:

```python
df.reset_index(inplace=True) # the old index is added as a column
df.reset_index(drop=True, inplace=True) # drop the old index 
```

Convert pandas column to DateTime:

```python
df["date"] =  pd.to_datetime(df["date"])
```

Select rows between two dates:

```python
df.loc[(df["date"] >= "2020-03-17") & (df["date"] < "2020-03-20"), :]
```

Select rows if value in column is in a list of values:

```python
df.loc[df["country"].isin(["China", "France"]), :]

# to get the opposite, use ~
df.loc[~df["country"].isin(["China", "France"]), :]
```

Sort by the values along either axis:

```python
df.sort_values("A", ascending=True, inplace=False, axis=0)
```

Sort object by labels (along an axis):

```python
df.sort_index(ascending=True, inplace=True, axis=0)
```

Replace values: 

```python
# replaces 0 by 5
df.replace(0, 5, inplace=True)    
# replaces Inf by NaN 
df.replace([np.inf, - np.inf], np.nan, inplace=True)  
```

Normalize features:

```python
# normalize features to a given range
from sklearn.preprocessing import MinMaxScaler
# normalize features by removing the mean and scaling to unit variance
from sklearn.preprocessing import StandardScaler

scaler = MinMaxScaler() # StandardScaler()
# Let's assume df has not been augmented 
# by the constant column yet.
df = pd.DataFrame(scaler.fit_transform(df), 
columns=df.columns, index=df.index)
df = df.assign(const=1)
```

Standard database join operations between `DataFrame` or named `Series` objects:

```python
df3 = df1.merge(df2, on=["..."], how="inner") # SQL inner join
df3 = df1.merge(df2, on=["..."], how="left")  # SQL left outer join
df3 = df1.merge(df2, on=["..."], how="right") # SQL right outer join
df3 = df1.merge(df2, on=["..."], how="outer") # SQL full outer join
df3 = df1.merge(df2, left_index=True, right_index=True)

# If both objects are Series or named Series, consider:
df = pd.merge(s1, s2, left_index=True, right_index=True, how="outer")
```

Concatenate `DataFrame` objects:

```python
# vertically concatenate two DataFrames
df = pd.concat([df1, df2], axis=0, ignore_index=True) 
```

Split features `X` and labels `y`:

```python
y = df.loc[:, "y"]
X = df.drop("y", axis=1)
```

Split training set and test set:

```python
from sklearn.model_selection import train_test_split

X_train, X_test, y_train, y_test = train_test_split(X, y, 
test_size=0.3, random_state=42)

```

Add a column of constant `1`'s:
```python
X = X.assign(const=1)
# Used for statsmodels
# The added column's name is "const"
```

Swap/reorder columns of `DataFrame` objects:

```python
df = df.reindex(columns=["...", "..."])
```

Visualize the number of distinct values that each feature can take:

```python
df.nunique()
```

Visualize the number of distinct values that each feature can take and the corresponding data type:

```python
unique_counts = pd.DataFrame([(col, df[col].dtype, df[col].nunique()) \
                              for col in df.columns], columns=["col_name", "dtype", "nunique"])\
.set_index("col_name").sort_values(by=['nunique'])

unique_counts
```

Visualize counts of unique values in descending order of frequency:

```python
df["A"].value_counts()
```

For a certain column, parse string representations to lists of elements:

```python
import ast
col_names = ["genres", "spoken_languages"]
for col in col_names:
    df[col] = df[col].apply(lambda x: ast.literal_eval(x)) 
```

For a certain column, convert lists of strings to dummies: 

```python
for col in ["genres", "production_countries", "spoken_languages"]:
    df = df.join(df[col].str.join('|').str.get_dummies()\
    .add_prefix(col + "_")).drop(col, axis=1)
```

```
genres: [Comedy, Drama, Family, Romance], [Drama, Thriller], ...	
genres_Comedy, genres_Drama, genres_Family, genres_Romance, ...
```

Get a subset of the DataFrame’s columns based on the column dtypes:

```python
df.select_dtypes(include=["object", "bool", "float64", "int"])
```

Make plots of Series or DataFrame (matplotlib is used by default):

```python
df.plot(x="...", y="...", kind="line")
plt.show()
```

Make histograms:

```python
df["age"].hist(bins=10)
plt.show()
```

Generate descriptive statistics (count, mean, std, min, 25%, 50%, 75%, max for each feature) of a `GroupBy` object`:

```python
df.groupby(["country"]).describe()
df.groupby(["country"])["cases"].describe()
df.groupby(["country"])["cases", "fatalities"].describe()
```

MultiIndex indexing:

```
                                    cases  fatalities
country 			 date                                  
China          2020-01-22             548          17
               2020-01-23             643          18
...                                   ...         ...
Italy          2020-03-17           31506        2503
               2020-03-18           35713        2978
```

```python
df.loc["China", :]
df.loc[(slice(None), "2020-01-23"), :]
df.loc[(slice(None), slice("2020-01-23", "2020-01-25")), :]
```

Revert from MultiIndex to single index dataframe:

```python
# level: only remove the given levels from the index

# integer position
df.reset_index(level=[1, 3], inplace=True) 
# the name of the level
df.reset_index(level=["...", "...", "..."], inplace=True) 
```

Transform multiple columns to MultiIndex:

```python
df.set_index(["A", "B"], inplace=True)
```

Slice a MultiIndex DataFrame with a condition based on the index:

```python
l0 = df.index.get_level_values(0) # level 0 index
l1 = df.index.get_level_values(1) # level 1 index 
df.loc[(l0 == "foo") | ((l0=="bar") & (l1=="two")), :]
```

Exchange index level of a MultiIndex DataFrame:

```python
df = df.swaplevel(0, 1)
df = df.swaplevel("...", "...")
df = df.swaplevel("...", 1)
df = df.swaplevel(0, "...")

# However, calling this method does not 
# change the ordering of the values.

# To solve this, do the following: 
# "..." stands for the index level name (str)
df.sort_values("...", ascending=True, inplace=True, axis=0)
```

Rename a `Series`:

```python
s.rename("...", inplace=True)
```

Convert `Series` to `DataFrame`:

```python
df = s.to_frame()
```

Build a `pd.Timestamp` (datetime) object:

```python
time_stamp = pd.to_datetime("2020-03-21")
```

Add date offset to a `pd.Timestamp` object: 

```python
time_stamp = pd.to_datetime("2019-02-19")  # 2019-02-19 00:00:00
time_stamp += pd.DateOffset(days=54)       # 2019-04-14 00:00:00
time_stamp += pd.DateOffset(minutes=23849) # 2019-04-30 13:29:00
```

Slice a dataframe based on datetime index (if datetime column has been set as the index):

```python
df.loc["2009-05-01" : "2010-03-01", :]
df.loc["2012" : "2012-02-18", :]
```

Conversion of `pd.Timedelta` Series, `pd.TimedeltaIndex`, and `pd.Timedelta` scalars:

```python
# All the following conversions output float64

# to days 
td / np.timedelta64(1, 'D')
(pd.to_datetime("2020-03-21") - df["date"]) / np.timedelta64(1, 'D')
td.astype('timedelta64[D]')

# to seconds 
td / np.timedelta64(1, 's')
td.astype('timedelta64[s]')

# to months (these are constant months)
td / np.timedelta64(1, 'M')
```

```python
time_delta = pd.to_datetime("2021-02-19") - pd.to_datetime('2020-06-18')
print(time_delta) # 246 days 00:00:00
print(time_delta.days) # 246
print(time_delta.components) 
# Components(days=246, hours=0, minutes=0, seconds=0, 
# milliseconds=0, microseconds=0, nanoseconds=0)
```


Rolling window calculations:

```
# suppose df is the following DataFrame with shape (5, 1)

# rolling window calculations return DataFrames with 
# exactly the same shape

   column_1
0         0
1         1
2         2
3         3
4         4
```

```python
df.rolling(3).sum()
```

```
   column_1
0       NaN
1       NaN
2       3.0
3       6.0
4       9.0
```

```python
df.rolling(3, min_periods=1).sum()
```

```
   column_1
0       0.0
1       1.0
2       3.0
3       6.0
4       9.0
```

```python
df.rolling(3, center=True).sum()
```

```
   column_1
0       NaN
1       3.0
2       6.0
3       9.0
4       NaN
```

```python
df.rolling(3, min_periods=1, center=True).sum()
```

```
   column_1
0       1.0
1       3.0
2       6.0
3       9.0
4       7.0
```


Count the number of `True`/`False` in each row/column:

```python
# suppose df is a DataFrame that has either 
# True or False as value

# For pd.DataFrame.apply(), 
# axis=0/"index", apply function to each column
# axis=1/"columns", apply function to each row 

# find out how many True/False are there in each column 
df.apply(pd.Series.value_counts, axis=0)

# find out how many True/False are there in each row 
df.apply(pd.Series.value_counts, axis=1)
```

Print all rows and all columns of a DataFrame

```python
with pd.option_context('display.max_rows', None, 'display.max_columns', None): 
    print(df)
```


























