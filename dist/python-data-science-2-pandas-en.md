# Pandas

## Pandas

_Pandas_ is a data analysis library; it is based on _NumPy_

```py
import pandas as pd
```

## Series and DataFrame

- **Series**: Collection of values for some keys (table column)
- **DataFrame**: Collection of associated series (table)

Example:

|    | Area | Population | Capital          |
| -- | ---: | ---------: | ---------------- |
| CN |  9.6 |       1386 | Beijing          |
| RU |   17 |        144 | Moscow           |
| US |  9.8 |        327 | Washington, D.C. |

## Creating a Series

```py
area = pd.Series({'CN': 9.6, 'RU': 17, 'US': 9.8})
population = pd.Series({'CN': 1386, 'RU': 144, 'US': 327})
```

```py
area = pd.Series([9.6, 17, 9.8], ["CN", "RU", "US"])
population = pd.Series([1386, 144, 327], ["CN", "RU", "US"])
```

## Reading values

```py
area[0] # 9.6

area['CN'] # 9.6
```

## Series

Each series has a specific data type

```py
area.dtype # float64
```

Manually setting the datatype:

```py
area = pd.Series({"CN": 9.6, "RU": 17, "US": 9.8},
                 dtype="float32")
```

## DataFrame

```py
countries = pd.DataFrame({
    'area': area,
    'population': population})
```

# Importing and exporting data

## Importing and exporting data

data formats:

- CSV
- Excel
- JSON
- HDF5 (efficient binary format)
- SQL tables (via _SQLAlchemy_)

## Importing and exporting data

The following functions can import / export data from files. Imports may read files from online sources.

import: `pd.read_csv`, `pd.read_excel`, ...

export: `df.to_csv`, `df.to_excel`, ...

## Importing CSV

Example: Euribor (interest rates of European bonds)

```py
euribor = pd.read_csv(
    "https://datahub.io/core/euribor/r/euribor-12m-monthly.csv"
)
```

## Importing CSV

Possible keyword arguments for `read_csv`:

- `index_col`: identifies a column to be used as an index instead of a numerical index
- `header`: passing a value of `None` signifies that there is no header row
- `names`: column names to be used in the resulting `DataFrame`
- `sep`: for specifying other separators than a comma
- `usecols`: for importing only specific columns
- `parse_dates`: expects a list of column names
- ...

See also: <https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html>

## Importing CSV

Advanced Euribor example:

- parse the date
- use the date as the index
- only import the columns _date_ and _rate_

```py
euribor = pd.read_csv(
    "https://datahub.io/core/euribor/r/euribor-12m-monthly.csv",
    parse_dates=["date"],
    index_col="date",
    usecols=["date", "rate"]
)
```

## Importing CSV

Task: Import the following data sources, ensuring the data is formatted nicely:

- S&P 500 monthly prices (US stock index): <https://datahub.io/core/s-and-p-500/r/data.csv>
- Exchange rates: <https://datahub.io/core/us-euro-foreign-exchange-rate/r/monthly.csv>
- Iris dataset (statistics of leaf sizes for iris flowers): <http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data> (look up appropriate column names on the web)

## Importing CSV

possible solutions:

```py
iris = pd.read_csv(
    "http://archive.ics.uci.edu/ml/machine-learning-databases/iris/iris.data",
    header=None,
    names=["sepal_length", "sepal_width", "petal_length",
           "petal_width", "name"])
sp500 = pd.read_csv(
    "https://datahub.io/core/s-and-p-500/r/data.csv",
    index_col="Date",
    parse_dates=["Date"])
exchange_rates = pd.read_csv(
    "https://datahub.io/core/us-euro-foreign-exchange-rate/r/monthly.csv",
    parse_dates=["Date"])
```

## Importing and exporting data

Excel:

```py
euribor.to_excel('euribor.xlsx')
euribor = pd.read_excel('euribor.xlsx', index_col=0)
```

HDF5 (_pytables_ must be installed):

```py
euribor.to_hdf("data.hdf5", "euribor")
euribor = read_hdf("data.hdf5", "euribor")
```

# Pandas: Querying data

## Querying data (by row numbers / column numbers)

- `df.iloc[5]`: row 5 (returns a `Series` object)
- `df.iloc[:5]`: first 5 rows (returns a `DataFrame` object)
- `df.iloc[10:20]`: rows 10-19
- `df.iloc[5, 1]`: row 5, column 1
- `df.iloc[5, [0, 2]]`: row 5, column 0 and 2

## Querying data (by row names / column names)

- `df.index`: row names
- `df.columns`: column names

<!-- -->

- `df.loc["2009-01-02"]`: Row by index value
- `df.loc["2009-01-01":"2009-01-31"]`: Row in a specified range (both values included)
- `df.loc[:, "rate"]`: column `"rate"`
- `df["rate"]`: column `"rate"` (short version)
- `df.rate`: column `"rate"` (extra short version - only works with no special characters)
- `df.loc[:, ["rate", "maturity_level"]]`: two columns
- `df.loc["2009-01-02", "rate"]`: specific row and column

## Sorting rows

- `df.sort_values(by="rate")`
- `df.loc["2009-01-02" : "2009-12-31"].sort_values(by="rate")`
- `df.sort_index(ascending=False)`

## Sampling data

- `df.sample()` - random entry
- `df.sample(5)` - five entries
- `df.sample(frac=0.1)` - 10% of all entries

## Filtering entries

All values that don't fulfill the criterion are set to _NaN_ or _NA_.

```py
iris[iris > 0]
```

## Filtering rows

via _boolean indexing_ - which is applied by rows:

- `df[df.rate < 0]`
- `df[df.length < 0] = np.nan`
- `df[df.name == "Iris-setosa"]`
- `df[df.name.isin(["Iris-setosa", "Iris-virginica"])])]`

## Filtering rows

SQL template:

```sql
SELECT * FROM df
WHERE a < b AND b < c
```

Pandas:

```py
df[(df.a < df.b) & (df.b < df.c)]
```

or

```py
df.query("a < b < c")
```

## Exercises (Euribor)

- first entry
- last entry
- last 10 entries
- entry from 2009-01-02
- entries from the year 2009
- ...

## Solutions (Euribor)

- first entry: `euribor.iloc[0]`
- last entry: `euribor.iloc[-1]`
- last 10 entries: `euribor.iloc[-10:]`
- entry from 2009-01-02: `euribor.loc["2009-01-02"]`
- entries from the year 2009: `euribor.loc["2009-01-01": "2009-12-31"]`

## Exercises (Iris)

- maximum _petal length_ of _iris setosa_ (without using `.max`)

# Data statistics

## Data statistics

```py
df.describe()
```

## Statistics on a data frames / series

```py
countries.area.describe()
```

```txt
count     3.000000
mean     12.133333
std       4.215843
min       9.600000
25%       9.700000
50%       9.800000
75%      13.400000
max      17.000000
dtype: float64
```

(see next slide for explanations)

## Statistics on series

```py
countries.area.describe()
```

The above computes the following data:

- `area.count()`
- `area.mean()`
- `area.std()`
- `area.quantile(0)` / `area.min()`
- `area.quantile(0.25)`
- `area.quantile(0.5)` / `area.median()`
- `area.quantile(0.75)`
- `area.quantile(1)` / `ara.max()`

## Statistical quantities

- _std_: _Standard deviation_
- _median_: half of the values are less and half are greater than the median
- _min_: all values are greater than the minimum
- _25%-quantile_: 25% of all values are less

# Example: exchange rates

## Exchange rates

<https://datahub.io/core/exchange-rates/r/daily.csv>

Daily Exchange rates between USD and other countries

We will read exchange data - transform it so it only contains data for EUR-USD exchange rates and turn the dates into the index

## Exchange rates

Solution:

```py
er = pd.read_csv(
    "https://datahub.io/core/exchange-rates/r/daily.csv",
    parse_dates=[1])

# only select euro
er_eur_full = er.loc[er.Country=="Euro"]

# now we can set the date as the index
er_eur_dateindex = er_eur_full.set_index('Date')

# we can drop the country information
er_eur = er_eur_dateindex.loc[:, 'Value']
```

# Pandas: changing data

## Dropping data

dropping rows:

```py
df2 = df1.drop(["CN", "US"])
```

dropping columns:

```py
df2 = df1.drop(columns=["pop"])
```

## Computing derived data

Adding a new column:

```py
iris["sepal_ratio"] = iris["sepal_length"] / iris["sepal_width"]

iris["sepal_ratio"].mean()
iris["sepal_ratio"].std()

iris_setosa = iris.loc[
    iris["name"] == "Iris-setosa"
]

iris_setosa["sepal_ratio"].mean()
iris_setosa["sepal_ratio"].std()
```

## Computing derived data via NumPy

Task:

- Analyze the S&P 500 monthly data and determine the monthly gain / loss in percent for each month
- What was the biggest loss / gain in a month?

## Computing derived data via NumPy

converting to NumPy array:

```py
values_np = sp500["SP500"].to_numpy()
```

differences of sequential months:

```py
diffs = values_np[1:] - values_np[:-1]
# add a single NaN to the front
diffs = np.concatenate([
    np.array([float('nan')]), diffs])
```

adding to pandas data:

```py
sp500["Diff"] = diffs
sp500["Gain"] = sp500["Diff"] / sp500["SP500"]
```

## Setting data

```py
iris.iloc[0, 0] = 6

iris.loc[:, "sepal_ratio"] = float('nan')
```

# Missing data

## Missing data

In the exchange rate data there are some missing entries:

- some days are not present (weekends)
- for some days the values are `NaN`s

## Missing data

Values that represent missing data (since pandas 1.0):

- for floats: `NaN` (as usual in Python)
- for other data types: `NA` (from the pandas package)

## Missing data

removing any rows with missing data:

```py
er = er.dropna()
```

filling any rows with missing data with zeros:

```py
er = er.fillna(0)
```

filling any missing data with the value from the previous row:

```py
er = er.fillna(method="backfill")
```

## Missing data

interpolating data:

```py
er = er.intepolate()
er = er.interpolate(method="spline")
```

interpolating example:

```py
url = 'https://datahub.io/core/interest-rates-gb/r/data.csv'

ir_uk = pd.read_csv(url, index_col="date",
                    parse_dates=True)

ir_uk_weekly = ir_uk.resample('7d').interpolate()
```

## Exercise

Use the data from _sp500_ and euribor to compare the development of american and european interest rates

# Basic plots in Pandas

## Basic plots in Pandas

general procedure:

In Jupyter:

```py
data_frame.plot()
```

In the terminal:

```py
import matplotlib.pyplot as plt

data_frame.plot()

# show all figures that were created since the last
# call of .show()
plt.show()
```

## Basic plots in Pandas

- graph: `df.plot.line()` / `df.plot()`
- bar chart: `df.plot.bar()`
- scatter plot: `df.plot.scatter(x="name1", y="name2")`
- histogram: `df.plot.hist()`
- box plot: `df.plot.box()`
- pie chart: `df.pie()`

## Graph

via `df.plot.line()` or `df.plot()`

```py
euribor.plot.line()
```

```py
sp500["SP500"].plot.line(figsize=(9, 6))
```

## Bar chart

```py
euribor.iloc[-36:].plot.bar()
```

Exercise: Visualize the median of _sepal-width_ and _sepal-length_ for all three types of flowers

## Scatter Plot

```py
iris.plot.scatter(x="sepal_length", y="sepal_width")
```

Exercise: scatter plot for _Iris-setosa_

## Histogram

```py
iris.sepal_lenght.plot.hist()
```

```py
iris.sepal_length.plot.hist(bins=30)
```

## Histogram

Exercise:

Simulate 10 Million rolls with 10 dice each and visualize the distribution of the sum of eyes as a histogram

## Box plot

```py
iris.plot.box()
```

## Pie chart

```py
df.pie()
```

## Scatter matrix

creates several scatter plots - if there are 4 data series it will create 4x4 plots (scatter plots and histograms)

```py
from pandas.plotting import scatter_matrix

scatter_matrix(iris)
```

# Basic plots in pandas and pyplot

## Graph

Pyplot:

```py
plt.plot(x, y)
plt.plot(y)
```

Pandas:

```py
df.plot.line()
df.plot()
```

## Bar chart

Pyplot:

```py
plt.bar(x, y)
```

Pandas:

```py
df.plot.bar()
```

## Scatter Plot

Pyplot:

```py
plt.plot(x, y, ".")
plt.scatter(x, y, 2, "red")
```

Pandas:

```py
df.plot.scatter(x="name1", y="name2")
```

## Histogram

Pyplot:

```py
plt.hist(x)
```

Pandas:

```py
df.plot.hist()
```

## Histogram

Pyplot:

```py
plt.hist(
  iris[:, 2],
  bins=[1, 2, 4, 5, 6],
  density=True)
```

Pandas:

```py
iris.sepal_length.plot.hist(bins=5)
```

## Box Plot

Pyplot:

```py
plt.boxplot(data)
```

Pandas:

```py
df.plot.box()
```

## Pie chart

Pyplot:

```py
plt.pie(x, labels=[...])
```

Pandas:

```py
df.plot.pie()
```

# Cross tabulation

## Cross tabulation

A _cross tabulation_ shows the number of corresponding entries across multiple properties

## Cross tabulation

example:

```py
import seaborn as sns
import pandas as pd
titanic = sns.load_dataset("titanic")
pd.crosstab(titanic.survived, titanic.sex)
```

output:

```
sex       female  male
survived
0             81   468
1            233   109
```

# Grouping

## Grouping

splitting data into groups / categories and computing values based on these groups

## Grouping

example: average of the iris data

```py
iris.groupby(iris.name).mean()
```

```
                 sepal_length  sepal_width  petal_length  petal_width
name
Iris-setosa             5.006        3.418         1.464        0.244
Iris-versicolor         5.936        2.770         4.260        1.326
Iris-virginica          6.588        2.974         5.552        2.026
```

## Grouping

Exercise: average USD exchange rates for each currency in the 90s

# Multi index

## Multi index

**index column**: column that uniquely identifies a row in a DataFrame

**multi index**: combination of multiple columns for unique identification

## Multi index

example: exchange rates

| Date       | Country   | Exchange rate |
| ---------- | --------- | ------------- |
| 1971-01-01 | Australia | 0.894         |
| 1971-02-01 | Australia | 0.890         |
| 1971-03-01 | Australia | 0.890         |

A row can be uniquely identified by a combination of _date_ and _country_

## Multi index

Importing with a multi-index:

```py
exchange_rates = pd.read_csv(
    "https://datahub.io/core/us-euro-foreign-exchange-rate/r/monthly.csv",
    index_col=["Country", "Date"]
    parse_dates=["Date"])
```