---
layout: post
category: Python
title: Remove unused categories in Pandas series 
tagline: by SunHaozhe
tags: 
  - Python
  - Pandas
  - API
mathjax: true
comments: true
published: true
---

```python
df = pd.read_csv("train.csv")
df.set_index("id", inplace=True)

categorical_cols = ["province", "country"]
df.loc[:, categorical_cols] = df.loc[:, categorical_cols].astype("category")

print(df.info())
```

```
Int64Index: 17040 entries, 1 to 26379
Data columns (total 3 columns):
province    7800 non-null category
country     17040 non-null category
cases    		17040 non-null float64
dtypes: category(2), float64(1)
memory usage: ...
None
```

```python
df["country"]
```

```
Id
1        Afghanistan
2        Afghanistan
3        Afghanistan
4        Afghanistan
5        Afghanistan
            ...     
26375         Zambia
26376         Zambia
26377         Zambia
26378         Zambia
26379         Zambia
Name: country, Length: 17040, dtype: category
Categories (163, object): [Afghanistan, Albania, Algeria, Andorra, ..., Uzbekistan, Venezuela, Vietnam, Zambia]
```

For the `country` column, there are 17040 entries. Each entry is one of the 163 categories.

Let's keep only 3 categories which interest us:

```python
countries = ["China", "France", "Italy"]
df2 = df.loc[df["country"].isin(countries), :]
print(df2.info())
```

```
Int64Index: 42 entries, 4431 to 12429
Data columns (total 3 columns):
province    41 non-null category
country    	42 non-null category
cases    		42 non-null float64
dtypes: category(2), float64(2)
memory usage: 13.4 KB
None
```

```python
df2["country"]
```

```
Id
4431      China
4524      China
4617      China
4710      China
4803      China
4896      China
4989      China
5082      China
5175      China
5268      China
5361      China
5454      China
5547      China
5640      China
5733      China
5826      China
5919      China
6012      China
6105      China
6198      China
6291      China
6384      China
6477      China
6570      China
6663      China
6756      China
6849      China
6942      China
7035      China
7128      China
7221      China
7314      China
7407      China
9453     France
9546     France
9639     France
9732     France
9825     France
9918     France
10011    France
10104    France
12429     Italy
Name: country, dtype: category
Categories (163, object): [Afghanistan, Albania, Algeria, Andorra, ..., Uzbekistan, Venezuela, Vietnam, Zambia]
```

```python
df2["country"].unique()
```

```
[China, France, Italy]
Categories (3, object): [China, France, Italy]
```

```python
df2["country"].value_counts()
```

```
China          33
France          8
Italy           1
Zambia          0
Greece          0
               ..
Netherlands     0
Nepal           0
Namibia         0
Morocco         0
Afghanistan     0
Name: country, Length: 163, dtype: int64
```

```python
df2.groupby(["country"])["cases"].sum()
```

```
country
Afghanistan            0.0
Albania                0.0
Algeria                0.0
Andorra                0.0
Antigua and Barbuda    0.0
                      ... 
Uruguay                0.0
Uzbekistan             0.0
Venezuela              0.0
Vietnam                0.0
Zambia                 0.0
Name: cases, Length: 163, dtype: float64
```

Now we observe that even if there remains only 42 entries, there are still 163 categories as before.

To solve this, we can do the following:

```python
df2.groupby(df["country"].cat.remove_unused_categories())["cases"].sum()
```

```
country
China     81305.0
France    14427.0
Italy     53578.0
Name: cases, dtype: float64
```

Let's see what's going on. 

`df2["country"].cat` is a `pandas.core.arrays.categorical.CategoricalAccessor` object. The method `Series.cat.remove_unused_categories()` removes categories which are not used and returns a `Series` (DataFrame column).  

The above can also be done by:

```
df2.assign(country=df2["country"].cat.remove_unused_categories()).groupby("country")["cases"].sum()
```

or 

```
df2["country"] = df2["country"].cat.remove_unused_categories()
df2.groupby("country")["cases"].sum()
```
















