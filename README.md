![Coffee](https://img.freepik.com/premium-photo/white-coffee-cup-roasted-coffee-beans-around_35570-794.jpg)
# Coffee Quality Analysis
## Project Introduction
This project uses the coffee rating dataset provided by Tidy Tuesday，processed a dataset containing 43 columns and 1,339 rows to explore the impact of different species of coffee, different altitudes, different countries, flavour and acidity on ratings for visual presentation.
   
## Dataset Introduction
- data source: Tidy Tuesday's 2020-07-07 coffee rating dataset https://github.com/rfordatascience/tidytuesday/tree/main/data/2020/2020-07-07
- data file: `coffee_ratings.csv`
- data scale:
  - row: 1339
  - column: 43
- key column:
  - `total_cup_points`
  - `species`
  - `altitude_mean_meters`
  - `country_of_origin`
  - `flavor`
  - `acidity`

## Environmental dependence
- pandas
- numpy
- matplotlib
- seaborn
- scipy
- plotly
  
## Functional realisation and steps description
### Step 1.Data reading
1. Using pandas to read CSV data files
`data = pd.read_csv('data/coffee_ratings.csv')`
2. Checking data types
`print(data.info())`

### Step 2. Data cleaning
Raw data often contains missing values, duplicate values, inconsistent formatting or incorrect data, in order to safeguard the quality of the data and enhance the reliability of the results, it is necessary to first clean the overall data

```python
# Check for missing values
print("Missing value statistics：")
print(data.isnull().sum())

# Delete columns with excessive missing values（>50%）
missing_percentage = data.isnull().mean() * 100
columns_to_drop = missing_percentage[missing_percentage > 50].index
data = data.drop(columns=columns_to_drop)
print(f'Deleted the following (more than 50% missing values): {list(columns_to_drop)}')

# Fill missing values
data['total_cup_points'].fillna(data['total_cup_points'].median(), inplace=True)
data['country_of_origin'].fillna(data['country_of_origin'].mode()[0], inplace=True)
data['species'].fillna(data['species'].mode()[0], inplace=True)

# Deleting Exceptions
# Remove outliers with scores below 50 or above 100
data = data[(data['total_cup_points'] >= 50) & (data['total_cup_points'] <= 100)]
# Remove unusual samples with negative elevation values
data = data[data['altitude_mean_meters'] > 0]

# Delete irrelevant columns
columns_to_remove = ['lot_number', 'ico_number', 'certification_body', 'expiration']
data = data.drop(columns=columns_to_remove, errors='ignore')
```

### Step 3 : Data visualisation

#### 1. Plotting histograms of rating distribution
Plotting a histogram of the distribution of ratings can help to understand the spread of ratings
```python
sns.histplot(data['total_cup_points'], bins=50, kde=True, color='blue')
```

#### 2. Distribution of ratings by species
There are two main species of coffee beans : Arabica, Robusta
```python
sns.histplot(subset['total_cup_points'], kde=True, label=species, bins=30, alpha=0.5)
```

#### 3. Scatterplot of ratings and altitude
Scatter plots were drawn to help show the correlation between coffee ratings and altitude, and quantified by linear plots
```python
# Scatterplot
sns.scatterplot(x='altitude_mean_meters', y='total_cup_points', data=data, alpha=0.6, color='blue')
# Regression analysis
sns.regplot(x='altitude_mean_meters', y='total_cup_points', data=data, scatter_kws={'alpha': 0.5}, line_kws={'color': 'red'})
```

#### 4. Interactive map
-plotly.express
Need to standardise the country names first
```python
fig = px.choropleth(
    country_ratings,
    locations="country",
    locationmode="country names",
    color="avg_rating",
    title="Average Coffee Rating by Country",
    color_continuous_scale="YlGnBu",
    labels={'avg_rating': 'Average Rating'}
)
```
#### 5. Heat mapping of flavour and acidity

```python
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".2f")
```

## Usage
### 1. Run:
   ``bash
   python coffee_rating_project_analysis.py
### 2. Output:
#### 1. Histograms of rating distribution
   ![Rating Distribution](Output%20Image/rating%20distribution.png)
#### 2. Distribution of ratings by species
   ![Species Rating Distribution](Output%20Image/species%20rating%20distribution.png)
#### 3. Scatterplot of ratings and altitude
   ![Altitude Scatter Plot](Output%20Image/altitude%20scatter%20plot.png)
   ![Altitude Regression](Output%20Image/altitude%20regression.png)
#### 4. Interactive map
   ![Country Map Rating 1](Output%20Image/country%20map%20rating1.png)
   ![Country Map Rating 2](Output%20Image/country%20map%20rating2.png)
#### 5. Heat mapping of flavour and acidity
   ![Flavor & Acidity Heatmap](Output%20Image/flavor%26acidity%20heatmap.png)
