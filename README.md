![Coffee](https://img.freepik.com/premium-photo/white-coffee-cup-roasted-coffee-beans-around_35570-794.jpg)
# Coffee Quality Analysis
## Description of the project
该项目的目标是通过一个用 Python 编写的程序，对咖啡质量数据进行分析和可视化，以实现以下功能：
1. 数据清理：处理缺失值、重复值和异常值。Data cleansing: dealing with missing values, duplicates and outliers.
2. 统计分析： 按国家分组生成基本统计摘要和平均评级。Statistical analyses: Generate basic statistical summaries and average ratings by country grouping.
3. 数据可视化：包括评分分布直方图和其他高级可视化。Data visualisation: including histograms of rating distributions and other advanced visualisations.
4. 高级可视化：用于分析数据模式和变量之间的关系。Avanced Visualization：Used to analyse data patterns and relationships between variables.
   
## Source of data
- Data file: ’coffee_ratings.csv’
- Data source: ’/content/drive/MyDrive/coffee_quality_project/’
Data description: This dataset contains coffee cup measurement data from a number of countries, including information on ratings, country origin, and more.

## 功能实现和步骤说明 Functional implementation and step-by-step description
### Step 1: Data reading and initial check
Using pandas to read CSV data files
#### 示例代码
Checking data types, missing values and duplicate values
#### 示例代码

### Step 2: Data cleansing
Removing outliers
#### 示例代码
保证数据质量，准备进行分析
#### 示例代码

## Step 3: Statistical analyses
描述数据分布，包括均值、标准差等统计信息
按国家分组计算总评分的平均值
#### 示例代码

## Step 4 : Data visualisation
评分分布直方图-使用 seaborn 和 matplotlib 绘制直方图，查看评分的总体分布
#### 示例代码
按国家评分的柱状图：
展示不同国家的平均评分
#### 示例代码

## Step 5 :  Avanced Visualization

## Usage
1. Clone the project: ’git clone https://github.com/coffee_quality_project’
2. Install dependencies: ’pip install -r requirements.txt’
3. Run the code: 'data_analysis.py'
## Environment dependencies
- pandas
- matplotlib
- seaborn
