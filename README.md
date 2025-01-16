![Coffee](https://img.freepik.com/premium-photo/white-coffee-cup-roasted-coffee-beans-around_35570-794.jpg)
# Coffee Quality Analysis
## Description of the project
This project uses the coffee rating dataset provided by Tidy Tuesday to explore the impact of different species of coffee, different altitudes, different countries, flavour and acidity on ratings for visual presentation.
   
## Data sources
The data used in this project comes from Tidy Tuesday's 2020-07-07 coffee rating dataset https://github.com/rfordatascience/tidytuesday/tree/main/data/2020/2020-07-07
The original source of the data is the Coffee Quality Database.

### Data description-----存疑
- Data file: ’coffee_ratings.csv’
- Data source: ’/content/drive/MyDrive/coffee_quality_project/’

## Functional realisation and steps description
### Step 1.Data reading
1. Using pandas to read CSV data files
’data = pd.read_csv('/content/drive/MyDrive/coffee_quality_project/coffee_ratings.csv')’ #提交作业时候记得更改路径
2. Checking data types
’print(data.info())’

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

# Check cleaned data
print("cleaned data：")
print(data.info())
print(data.describe())
```

### Step 3 : Data visualisation

#### 1. Plotting histograms of rating distribution
Plotting a histogram of the distribution of ratings can help to understand the spread of ratings
```python
import seaborn as sns
import matplotlib.pyplot as plt

data = cleaned_data

# Histogram of rating distribution
plt.figure(figsize=(10, 6))
sns.histplot(data['total_cup_points'], bins=50, kde=True, color='blue')
plt.title("Rating Distribution")
plt.xlabel("Total Cup Points")
plt.ylabel("Sample Count")
plt.xlim(0, 100)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()
```

#### 2. Distribution of ratings by species
There are two main species of coffee beans : Arabica, Robusta
```python
plt.figure(figsize=(12, 8))
for species in species_list:
    subset = cleaned_data[cleaned_data['species'] == species]
    sns.histplot(subset['total_cup_points'], kde=True, label=species, bins=30, alpha=0.5)

plt.title('Total Cup Points Distribution by Species', fontsize=16)
plt.xlabel('Total Cup Points', fontsize=12)
plt.ylabel('Sample Count', fontsize=12)
plt.legend(title='Species')
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()
```

#### 3. Scatterplot of ratings and altitude
Scatter plots were drawn to help show the correlation between coffee ratings and altitude, and quantified by linear plots
```python
# 1. Scatterplot of ratings and altitude
plt.figure(figsize=(10, 6))
sns.scatterplot(x='altitude_mean_meters', y='total_cup_points', data=data, alpha=0.6, color='blue')
plt.title('Scatter Plot: Total Cup Points vs Altitude', fontsize=16)
plt.xlabel('Mean Altitude (meters)', fontsize=12)
plt.ylabel('Total Cup Points', fontsize=12)
plt.grid()
plt.show()

# 2. Regression analysis
plt.figure(figsize=(10, 6))
sns.regplot(x='altitude_mean_meters', y='total_cup_points', data=data, scatter_kws={'alpha': 0.5}, line_kws={'color': 'red'})
plt.title('Regression: Total Cup Points vs Altitude', fontsize=16)
plt.xlabel('Mean Altitude (meters)', fontsize=12)
plt.ylabel('Total Cup Points', fontsize=12)
plt.grid()
plt.show()
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

fig.show()
```
#### 5. Heat mapping of flavour and acidity
```python
plt.figure(figsize=(8, 6))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', fmt=".2f")
plt.title('Correlation Heatmap: Ratings, Flavor, and Acidity', fontsize=16)
plt.show()
```

## Usage
1. Clone the project: ’git clone https://github.com/coffee_quality_project’
2. Install dependencies: ’pip install -r requirements.txt’
3. Run the code: 'data_analysis.py'

## Environment dependencies
- pandas
- matplotlib
- seaborn
- plotly.express
