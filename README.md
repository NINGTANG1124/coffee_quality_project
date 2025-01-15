![Coffee](https://img.freepik.com/premium-photo/white-coffee-cup-roasted-coffee-beans-around_35570-794.jpg)
# Coffee Quality Analysis
## Description of the project
本项目使用 Tidy Tuesday 提供的咖啡评级数据集，探索不同国家咖啡的质量分布，分析评分指标对总分的影响，并进行可视化展示。
1. 数据清理：去除缺失值，去除重复值。将数据类型标准化。处理异常值，无关列。
3. 数据可视化：将数据清理后的数据文件绘制评分分布直方图，绘制箱线图展示评分与咖啡种类的关系，绘制散点图和回归分析图展示咖啡评分与地理海拔之间的关系，绘制交互式地图展示每个国家生产的咖啡在评分上的平均表现，以便观察。
   
## 数据来源Source of data
本项目使用的数据来自 Tidy Tuesday 2020-07-07 的咖啡评级数据集。链接
数据的原始来源为 Coffee Quality Database。
### 数据描述
- Data file: ’coffee_ratings.csv’
- Data source: ’/content/drive/MyDrive/coffee_quality_project/’
Data description: This dataset contains coffee cup measurement data from a number of countries, including information on ratings, country origin, and more.

## 功能实现和步骤说明 Functional implementation and step-by-step description
### Step 1: 数据读取和初始检查 Data reading and initial check
1. Using pandas to read CSV data files
#### 示例代码
’data = pd.read_csv('/content/drive/MyDrive/coffee_quality_project/coffee_ratings.csv')’ #提交作业时候记得更改路径
2. Checking data types, missing values and duplicate values
#### 示例代码
’print(data.info())’

### Step 2. Data cleaning
# 2.检查缺失值
print("缺失值统计：")
print(data.isnull().sum())

# 删除缺失值过多的列（>50%）
missing_percentage = data.isnull().mean() * 100
columns_to_drop = missing_percentage[missing_percentage > 50].index
data = data.drop(columns=columns_to_drop)
#这些列通常很难提供可靠的分析结果，缺失数据过多的列可能会对模型的性能造成负面影响，因为模型无法有效利用这些信息。但有列很重要，缺失数据可以通过其他变量推断（如 altitude_mean_meters 可通过平均高度填充），可以选择保留。

# 填充缺失值：数值列用中位数，类别列用众数
data['total_cup_points'].fillna(data['total_cup_points'].median(), inplace=True)
data['country_of_origin'].fillna(data['country_of_origin'].mode()[0], inplace=True)
data['species'].fillna(data['species'].mode()[0], inplace=True)

# 3. 处理重复值
duplicates = data.duplicated().sum()
print(f"重复值数量: {duplicates}")
data = data.drop_duplicates()

# 4. 数据类型标准化
# 将字符串标准化
data['country_of_origin'] = data['country_of_origin'].str.strip().str.title()
data['species'] = data['species'].str.strip().str.capitalize()

# 转换数值列为浮点类型
data[['altitude_mean_meters', 'total_cup_points']] = data[['altitude_mean_meters', 'total_cup_points']].apply(pd.to_numeric, errors='coerce')

# 5. 异常值处理
# 剔除评分低于50或高于100的异常值
data = data[(data['total_cup_points'] >= 50) & (data['total_cup_points'] <= 100)]

# 剔除海拔值为负数的异常样本
data = data[data['altitude_mean_meters'] > 0]

# 6. 删除无关列
columns_to_remove = ['lot_number', 'ico_number', 'certification_body', 'expiration']
data = data.drop(columns=columns_to_remove, errors='ignore')

# 7. 输出清理后的数据概况
print("清理后的数据概况：")
print(data.info())
print(data.describe())

# 8. 保存清理后的数据
data.to_csv("cleaned_coffee_data.csv", index=False)

## Step 3 : Data visualisation
1. 评分分布直方图-使用 seaborn 和 matplotlib 绘制直方图，查看评分的总体分布
#### 示例代码
import seaborn as sns
import matplotlib.pyplot as plt

# 使用清理后的数据
data = cleaned_data  # 使用清理后的数据集

# 绘制评分分布直方图
# Overall Rating Distribution (0-100)
plt.figure(figsize=(10, 6))
sns.histplot(data['total_cup_points'], bins=50, kde=True, color='blue')
plt.title("Overall Rating Distribution (0-100)")
plt.xlabel("Total Cup Points")
plt.ylabel("Sample Count")
plt.xlim(0, 100)
plt.grid(axis='y', linestyle='--', alpha=0.7)
plt.show()

2. 评分与咖啡种类的关系
#### 示例代码
# species（种类）这个字段通常表示咖啡豆的种属，而不是具体的品种（variety）。咖啡豆的种属主要有以下两种：Arabica，Robusta
plt.figure(figsize=(10, 6))
sns.boxplot(x='species', y='total_cup_points', data=cleaned_data, palette='Set2')
plt.title('Total Cup Points by Coffee Species', fontsize=16)
plt.xlabel('Coffee Species', fontsize=12)
plt.ylabel('Total Cup Points', fontsize=12)
plt.grid(axis='y')
plt.show()

3. 绘制评分与海拔的散点图和回归分析图
#### 示例代码
import seaborn as sns
import matplotlib.pyplot as plt

# 绘制评分与海拔的散点图和回归分析图
plt.figure(figsize=(10, 6))

# 散点图部分
sns.scatterplot(x='altitude_mean_meters', y='total_cup_points', data=cleaned_data, 
                alpha=0.6, color='blue', label='Data Points')

# 回归线部分
sns.regplot(x='altitude_mean_meters', y='total_cup_points', data=cleaned_data, 
            scatter=False, line_kws={'color': 'red', 'label': 'Regression Line'})

# 添加标题和轴标签
plt.title('Total Cup Points vs Altitude (with Regression)', fontsize=16)
plt.xlabel('Mean Altitude (meters)', fontsize=12)
plt.ylabel('Total Cup Points', fontsize=12)
plt.legend()
plt.grid()

plt.show()

4. 绘制不同国家的咖啡平均评分的交互式地图
### 示例代码
import plotly.express as px

# 1. 准备数据：按国家计算评分的平均值
country_ratings = data.groupby('country_of_origin', as_index=False)['total_cup_points'].mean()
country_ratings.columns = ['country', 'avg_rating']

# 检查数据
print(country_ratings.head())

# 2. 绘制交互式地图
fig = px.choropleth(
    country_ratings,
    locations="country",  # 与地图匹配的国家名称
    locationmode="country names",  # 使用国家名称进行匹配
    color="avg_rating",  # 以评分平均值为颜色变量
    title="Average Coffee Rating by Country",
    color_continuous_scale="YlGnBu",  # 颜色方案
    labels={'avg_rating': 'Average Rating'}  # 图例标签
)

fig.show()


## Usage
1. Clone the project: ’git clone https://github.com/coffee_quality_project’
2. Install dependencies: ’pip install -r requirements.txt’
3. Run the code: 'data_analysis.py'

## Environment dependencies
- pandas
- matplotlib
- seaborn
