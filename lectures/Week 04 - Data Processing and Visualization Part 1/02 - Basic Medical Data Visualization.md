---
layout: default
title: 02 - Basic Medical Data Visualization
parent: Week 04 - Data Processing and Visualization Part 1
grand_parent: Lectures
nav_order: 4
---

# Basic Medical Data Exploration Visualization  Heart Diseases

[Source](https://towardsdatascience.com/basic-medical-data-exploration-visualization-heart-diseases-6ab12bc0a8b7)

In this lecture we're going to learn how to use [matplotlib](https://matplotlib.org/) and [seaborn](https://seaborn.pydata.org/) by following along with the following example. As always, the source author's link is listed for reference. This page will evolve over time.

## Dataset

The dataset we'll use here is the [Heart Disease Data Set](https://archive.ics.uci.edu/ml/datasets/Heart+Disease) containing 302 patient data each with 75 attributes. However, this example only uses 14 of them which can be seen below.

The columns used include:
1. age: age in years
2. sex: sex
    * 1 = male
    * 0 = female
3. cp: chest pain type 
    * Value 1: typical angina 
    * Value 2: atypical angina 
    * Value 3: non-anginal pain 
    * Value 4: asymptomatic 
4. trestbps: resting blood pressure (in mm Hg on admission to the hospital)
5. chol: serum cholestoral in mg/dl 
6. fbs: fasting blood sugar > 120 mg/dl
    * 1 = true
    * 0 = false
7. restecg:  restecg: resting electrocardiographic results 
    * Value 0: normal 
    * Value 1: having ST-T wave abnormality (T wave inversions and/or ST elevation or depression of > 0.05 mV) 
    * Value 2: showing probable or definite left ventricular hypertrophy by Estes' criteria 
8. thalach: maximum heart rate achieved 
9. exang: exercise induced angina
    * 1 = yes
    * 0 = no
10. oldpeak: ST depression induced by exercise relative to rest 
11. slope: the slope of the peak exercise ST segment 
    * Value 1: upsloping 
    * Value 2: flat 
    * Value 3: downsloping 
12. ca: number of major vessels (0-3) colored by flourosopy 
13. thal: 
    * 3 = normal
    * 6 = fixed defect
    * 7 = reversable defect 
14. num: diagnosis of heart disease (angiographic disease status) 
    * Value 0: < 50% diameter narrowing 
    * Value 1: > 50% diameter narrowing 


```python
columns = ["age", 
           "sex", 
           "cp", 
           "trestbps",
           "chol", 
           "fbs", 
           "restecg",
           "thalach",
           "exang", 
           "oldpeak",
           "slope", 
           "ca", 
           "thal", 
           "num"]
```


```python
# disable warnings for lecture
import warnings
warnings.filterwarnings('ignore')
```

## Overview of the Data Set , Cleaning, and Viewing


```python
import pandas as pd

# import the data and see the basic description
df = pd.read_csv("https://archive.ics.uci.edu/ml/machine-learning-databases/heart-disease/processed.cleveland.data")
df.columns = columns
```


```python
print("---- Describe ----")
print(df.describe())
```

    ---- Describe ----
                  age         sex          cp    trestbps        chol         fbs  \
    count  302.000000  302.000000  302.000000  302.000000  302.000000  302.000000   
    mean    54.410596    0.678808    3.165563  131.645695  246.738411    0.145695   
    std      9.040163    0.467709    0.953612   17.612202   51.856829    0.353386   
    min     29.000000    0.000000    1.000000   94.000000  126.000000    0.000000   
    25%     48.000000    0.000000    3.000000  120.000000  211.000000    0.000000   
    50%     55.500000    1.000000    3.000000  130.000000  241.500000    0.000000   
    75%     61.000000    1.000000    4.000000  140.000000  275.000000    0.000000   
    max     77.000000    1.000000    4.000000  200.000000  564.000000    1.000000   
    
              restecg     thalach       exang     oldpeak       slope         num  
    count  302.000000  302.000000  302.000000  302.000000  302.000000  302.000000  
    mean     0.986755  149.605960    0.327815    1.035430    1.596026    0.940397  
    std      0.994916   22.912959    0.470196    1.160723    0.611939    1.229384  
    min      0.000000   71.000000    0.000000    0.000000    1.000000    0.000000  
    25%      0.000000  133.250000    0.000000    0.000000    1.000000    0.000000  
    50%      0.500000  153.000000    0.000000    0.800000    2.000000    0.000000  
    75%      2.000000  166.000000    1.000000    1.600000    2.000000    2.000000  
    max      2.000000  202.000000    1.000000    6.200000    3.000000    4.000000  



```python
print('---- Info -----')
print(df.info())
```

    ---- Info -----
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 302 entries, 0 to 301
    Data columns (total 14 columns):
    age         302 non-null float64
    sex         302 non-null float64
    cp          302 non-null float64
    trestbps    302 non-null float64
    chol        302 non-null float64
    fbs         302 non-null float64
    restecg     302 non-null float64
    thalach     302 non-null float64
    exang       302 non-null float64
    oldpeak     302 non-null float64
    slope       302 non-null float64
    ca          302 non-null object
    thal        302 non-null object
    num         302 non-null int64
    dtypes: float64(11), int64(1), object(2)
    memory usage: 33.1+ KB
    None


We notice above that the ```ca``` and ```thal``` data elements are objects which we'll likely want to remap. Let's take a look at the data.


```python
df['thal'].unique()
```




    array(['3.0', '7.0', '6.0', '?'], dtype=object)




```python
df['ca'].unique()
```




    array(['3.0', '2.0', '0.0', '1.0', '?'], dtype=object)



From the codbook above we see these are coded values that we can remap.


```python
# Replace Every Number greater than 0 to 1 to mark heart disease
df.loc[df['num'] > 0 , 'num'] = 1
df.ca = pd.to_numeric(df.ca, errors='coerce').fillna(0)
df.thal = pd.to_numeric(df.thal, errors='coerce').fillna(0)
```


```python
df['thal'].unique()
```




    array([3., 7., 6., 0.])




```python
df['ca'].unique()
```




    array([3., 2., 0., 1.])



Now we can view the datatypes of the remapped data to ```float64``` and ```int64```.


```python
print('---- Dtype ----')
print(df.dtypes)
```

    ---- Dtype ----
    age         float64
    sex         float64
    cp          float64
    trestbps    float64
    chol        float64
    fbs         float64
    restecg     float64
    thalach     float64
    exang       float64
    oldpeak     float64
    slope       float64
    ca          float64
    thal        float64
    num           int64
    dtype: object


Next we'll want to 


```python
print('---- Null Data ----')
# count how many null values exist
print(df.isnull().sum())
```

    ---- Null Data ----
    age         0
    sex         0
    cp          0
    trestbps    0
    chol        0
    fbs         0
    restecg     0
    thalach     0
    exang       0
    oldpeak     0
    slope       0
    ca          0
    thal        0
    num         0
    dtype: int64



```python
# quickly check to see if there are any null values
print(df.isnull().values.any())
```

    False


After doing simple clean up, changing non-numerical value to NaN and replacing NaN with 0 we can safely say our data is somewhat clean.

## First / Last 10 Rows



```python
# print the first 10 and last 10
print('------ First 10 -------')
df.head(10)
```

    ------ First 10 -------





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>sex</th>
      <th>cp</th>
      <th>trestbps</th>
      <th>chol</th>
      <th>fbs</th>
      <th>restecg</th>
      <th>thalach</th>
      <th>exang</th>
      <th>oldpeak</th>
      <th>slope</th>
      <th>ca</th>
      <th>thal</th>
      <th>num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>67.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>160.0</td>
      <td>286.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>108.0</td>
      <td>1.0</td>
      <td>1.5</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>67.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>120.0</td>
      <td>229.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>129.0</td>
      <td>1.0</td>
      <td>2.6</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>2</th>
      <td>37.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>130.0</td>
      <td>250.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>187.0</td>
      <td>0.0</td>
      <td>3.5</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>41.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>130.0</td>
      <td>204.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>172.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>56.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>120.0</td>
      <td>236.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>178.0</td>
      <td>0.0</td>
      <td>0.8</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>62.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>140.0</td>
      <td>268.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>160.0</td>
      <td>0.0</td>
      <td>3.6</td>
      <td>3.0</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>6</th>
      <td>57.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>120.0</td>
      <td>354.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>163.0</td>
      <td>1.0</td>
      <td>0.6</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>63.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>130.0</td>
      <td>254.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>147.0</td>
      <td>0.0</td>
      <td>1.4</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>8</th>
      <td>53.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>140.0</td>
      <td>203.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>155.0</td>
      <td>1.0</td>
      <td>3.1</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>9</th>
      <td>57.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>140.0</td>
      <td>192.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>148.0</td>
      <td>0.0</td>
      <td>0.4</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>6.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>




```python
#  Last 10 
print('------ Last 10 -------')
df.tail(10)
```

    ------ Last 10 -------





<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>sex</th>
      <th>cp</th>
      <th>trestbps</th>
      <th>chol</th>
      <th>fbs</th>
      <th>restecg</th>
      <th>thalach</th>
      <th>exang</th>
      <th>oldpeak</th>
      <th>slope</th>
      <th>ca</th>
      <th>thal</th>
      <th>num</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>292</th>
      <td>63.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>140.0</td>
      <td>187.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>144.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>293</th>
      <td>63.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>124.0</td>
      <td>197.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>136.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>294</th>
      <td>41.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>120.0</td>
      <td>157.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>182.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>295</th>
      <td>59.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>164.0</td>
      <td>176.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>90.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>6.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>296</th>
      <td>57.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>140.0</td>
      <td>241.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>123.0</td>
      <td>1.0</td>
      <td>0.2</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>297</th>
      <td>45.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>110.0</td>
      <td>264.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>132.0</td>
      <td>0.0</td>
      <td>1.2</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>298</th>
      <td>68.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>144.0</td>
      <td>193.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>141.0</td>
      <td>0.0</td>
      <td>3.4</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>299</th>
      <td>57.0</td>
      <td>1.0</td>
      <td>4.0</td>
      <td>130.0</td>
      <td>131.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>115.0</td>
      <td>1.0</td>
      <td>1.2</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>7.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>300</th>
      <td>57.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>130.0</td>
      <td>236.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>174.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>1</td>
    </tr>
    <tr>
      <th>301</th>
      <td>38.0</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>138.0</td>
      <td>175.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>173.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



## Plotting Histograms

After reviewing the data in tabular form we want to visualize all of the data across the variables. We can do this easily with a histogram.


```python
# import matplotlib
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
# using pandas to generate the plots
df.hist()

# using matplotlib to render (or show) the plot
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_26_0.png)



```python
# get the histogram of every data points
fig = plt.figure(figsize = (18, 18))
ax = fig.gca()

df.hist(ax=ax, bins=30)
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_27_0.png)


With simple histogram of our data, we can easily observe the distribution of different attributes. One thing to note here is the fact that it is extremely easy for us to see which attributes are categorical values and which are not.

We can inspect a little bit more closely and take a look at the distribution of ages and fbs (fasting blood sugar). We can see that the age distribution is closely resembling of Gaussian distribution while fbs is a categorical value.


```python
# import seaborn
import seaborn as sns

# a closer look at age
plt.figure(figsize=(8, 8))
sns.distplot(df.age)
plt.show()
plt.close('all')
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_29_0.png)



```python
# a closer look at fbs
plt.figure(figsize=(8, 8))
sns.distplot(df.fbs)
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_30_0.png)


## Variance-Covariance Matrix

We can calculate variance-covariance matrices in a number of ways. First we'll use Numpy and then we'll use the built-in Dataframe functrion. Once calculated, we can observe that most attributes do not have a strong covariance relationship.


```python
import numpy as np
from numpy import dot

# calculate the Variance-Covariance Matrix 
sample = df.values
sample = sample - dot(np.ones((sample.shape[0],sample.shape[0])),sample)/(len(sample)-1)
covv = dot(sample.T,sample)/(len(sample)-1)
plt.figure(figsize=(8,8))
sns.heatmap(covv)
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_32_0.png)



```python
# compare with built in 
plt.figure(figsize=(8,8))
sns.heatmap(df.cov())
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_33_0.png)


## Correlation matrix

Similarly, the first image is created by manual numpy calculation and the second using the bulit-in method. Ee can observe that among the attributes there are actually strong correlation with one another. (especially heart disease and thal).


```python
# calculate correaltion matrix 
sample = df.values
certering_mat = np.diag(np.ones((302))) - np.ones((302,302))/302
std_matrix = np.diag(np.std(sample,0))
temp = dot(certering_mat,dot(sample, np.linalg.inv(std_matrix)  ))
temp = dot(temp.T,temp)/len(sample)

# plot
plt.figure(figsize=(13, 13))
sns.heatmap(np.around(temp,2),annot=True,fmt=".2f",cmap="Blues",annot_kws={"size": 15})
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_35_0.png)



```python
# correaltion matrix 
sns.set(font_scale=2)
plt.figure(figsize=(13,13))

sns.heatmap(df.corr().round(2),annot=True,fmt=".2f",cmap="Blues",annot_kws={"size": 15})
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_36_0.png)


## Interactive Histogram


```python
# plot the people who have heart vs not 
plt.figure(figsize=(13, 13))
sns.distplot(df.age[df.num==0], label='No Disease', color='blue')
sns.distplot(df.age[df.num==1], label='Disease', color='Red')
sns.distplot(df.trestbps[df.num==0],label= 'No Disease', color='Green')
sns.distplot(df.trestbps[df.num==1], label='Disease', color='violet')
plt.legend()
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_38_0.png)



```python
%matplotlib inline
import pygal
from IPython.display import SVG, HTML
html_pygal = """
<!DOCTYPE html>
<html>
  <head>
  <script type="text/javascript" src="http://kozea.github.com/pygal.js/javascripts/svg.jquery.js"></script>
  <script type="text/javascript" src="http://kozea.github.com/pygal.js/javascripts/pygal-tooltips.js"></script>
    <!-- ... -->
  </head>
  <body>
    <figure>
      {pygal_render}
    </figure>
  </body>
</html>
"""

hist = pygal.Histogram()

count, division = np.histogram(df.age[df.num==0].values,bins=100)
temp = []
for c,div in zip(count,division):
    temp.append((c,div,div+1))
    
count, division = np.histogram(df.age[df.num==1].values,bins=100)
temp1 = []
for c,div in zip(count,division):
    temp1.append((c,div,div+1))
    
count, division = np.histogram(df.trestbps[df.num==0].values,bins=100)
temp2 = []
for c,div in zip(count,division):
    temp2.append((c,div,div+1))
    
count, division = np.histogram(df.trestbps[df.num==1].values,bins=100)
temp3 = []
for c,div in zip(count,division):
    temp3.append((c,div,div+1))
    
hist.add('No Disease age', temp)
hist.add('Disease age', temp1)
hist.add('No Disease ', temp2)
hist.add('Disease', temp3)
hist.render()
HTML(html_pygal.format(pygal_render=hist.render()))
```





<!DOCTYPE html>
<html>
  <head>
  <script type="text/javascript" src="http://kozea.github.com/pygal.js/javascripts/svg.jquery.js"></script>
  <script type="text/javascript" src="http://kozea.github.com/pygal.js/javascripts/pygal-tooltips.js"></script>
    <!-- ... -->
  </head>
  <body>
    <figure>
      b'<?xml version=\'1.0\' encoding=\'utf-8\'?>\n<svg xmlns:xlink="http://www.w3.org/1999/xlink" xmlns="http://www.w3.org/2000/svg" id="chart-ac0d371a-7a65-4178-83a6-96b9824c4198" class="pygal-chart" viewBox="0 0 800 600"><!--Generated with pygal 2.4.0 (lxml) \xc2\xa9Kozea 2012-2016 on 2019-06-13--><!--http://pygal.org--><!--http://github.com/Kozea/pygal--><defs><style type="text/css">#chart-ac0d371a-7a65-4178-83a6-96b9824c4198{-webkit-user-select:none;-webkit-font-smoothing:antialiased;font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .title{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:16px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .legends .legend text{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:14px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis text{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:10px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis text.major{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:10px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .text-overlay text.value{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:16px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .text-overlay text.label{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:10px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:14px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 text.no_data{font-family:Consolas,"Liberation Mono",Menlo,Courier,monospace;font-size:64px}\n#chart-ac0d371a-7a65-4178-83a6-96b9824c4198{background-color:rgba(249,249,249,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 path,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 rect,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 circle{-webkit-transition:150ms;-moz-transition:150ms;transition:150ms}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .graph &gt; .background{fill:rgba(249,249,249,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .plot &gt; .background{fill:rgba(255,255,255,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .graph{fill:rgba(0,0,0,.87)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 text.no_data{fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .title{fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .legends .legend text{fill:rgba(0,0,0,.87)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .legends .legend:hover text{fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .line{stroke:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .guide.line{stroke:rgba(0,0,0,.54)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .major.line{stroke:rgba(0,0,0,.87)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis text.major{fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y .guides:hover .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .line-graph .axis.x .guides:hover .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .stackedline-graph .axis.x .guides:hover .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .xy-graph .axis.x .guides:hover .guide.line{stroke:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .guides:hover text{fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .reactive{fill-opacity:.7;stroke-opacity:.8}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .ci{stroke:rgba(0,0,0,.87)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .reactive.active,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .active .reactive{fill-opacity:.8;stroke-opacity:.9;stroke-width:4}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .ci .reactive.active{stroke-width:1.5}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .series text{fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip rect{fill:rgba(255,255,255,1);stroke:rgba(0,0,0,1);-webkit-transition:opacity 150ms;-moz-transition:opacity 150ms;transition:opacity 150ms}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip .label{fill:rgba(0,0,0,.87)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip .label{fill:rgba(0,0,0,.87)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip .legend{font-size:.8em;fill:rgba(0,0,0,.54)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip .x_label{font-size:.6em;fill:rgba(0,0,0,1)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip .xlink{font-size:.5em;text-decoration:underline}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip .value{font-size:1.5em}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .bound{font-size:.5em}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .max-value{font-size:.75em;fill:rgba(0,0,0,.54)}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .map-element{fill:rgba(255,255,255,1);stroke:rgba(0,0,0,.54) !important}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .map-element .reactive{fill-opacity:inherit;stroke-opacity:inherit}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-0,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-0 a:visited{stroke:#F44336;fill:#F44336}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-1,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-1 a:visited{stroke:#3F51B5;fill:#3F51B5}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-2,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-2 a:visited{stroke:#009688;fill:#009688}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-3,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .color-3 a:visited{stroke:#FFC107;fill:#FFC107}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .text-overlay .color-0 text{fill:black}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .text-overlay .color-1 text{fill:black}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .text-overlay .color-2 text{fill:black}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .text-overlay .color-3 text{fill:black}\n#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 text.no_data{text-anchor:middle}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .guide.line{fill:none}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .centered{text-anchor:middle}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .title{text-anchor:middle}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .legends .legend text{fill-opacity:1}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.x text{text-anchor:middle}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.x:not(.web) text[transform]{text-anchor:start}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.x:not(.web) text[transform].backwards{text-anchor:end}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y text{text-anchor:end}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y text[transform].backwards{text-anchor:start}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y2 text{text-anchor:start}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y2 text[transform].backwards{text-anchor:end}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .guide.line{stroke-dasharray:4,4}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .major.guide.line{stroke-dasharray:6,6}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .horizontal .axis.y .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .horizontal .axis.y2 .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .vertical .axis.x .guide.line{opacity:0}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .horizontal .axis.always_show .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .vertical .axis.always_show .guide.line{opacity:1 !important}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y .guides:hover .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.y2 .guides:hover .guide.line,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis.x .guides:hover .guide.line{opacity:1}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .axis .guides:hover text{opacity:1}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .nofill{fill:none}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .subtle-fill{fill-opacity:.2}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .dot{stroke-width:1px;fill-opacity:1}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .dot.active{stroke-width:5px}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .dot.negative{fill:transparent}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 text,#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 tspan{stroke:none !important}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .series text.active{opacity:1}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip rect{fill-opacity:.95;stroke-width:.5}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .tooltip text{fill-opacity:1}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .showable{visibility:hidden}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .showable.shown{visibility:visible}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .gauge-background{fill:rgba(229,229,229,1);stroke:none}#chart-ac0d371a-7a65-4178-83a6-96b9824c4198 .bg-lines{stroke:rgba(249,249,249,1);stroke-width:2px}</style><script type="text/javascript">window.pygal = window.pygal || {};window.pygal.config = window.pygal.config || {};window.pygal.config[\'ac0d371a-7a65-4178-83a6-96b9824c4198\'] = {"allow_interruptions": false, "box_mode": "extremes", "classes": ["pygal-chart"], "css": ["file://style.css", "file://graph.css"], "defs": [], "disable_xml_declaration": false, "dots_size": 2.5, "dynamic_print_values": false, "explicit_size": false, "fill": false, "force_uri_protocol": "https", "formatter": null, "half_pie": false, "height": 600, "include_x_axis": false, "inner_radius": 0, "interpolate": null, "interpolation_parameters": {}, "interpolation_precision": 250, "inverse_y_axis": false, "js": ["//kozea.github.io/pygal.js/2.0.x/pygal-tooltips.min.js"], "legend_at_bottom": false, "legend_at_bottom_columns": null, "legend_box_size": 12, "logarithmic": false, "margin": 20, "margin_bottom": null, "margin_left": null, "margin_right": null, "margin_top": null, "max_scale": 16, "min_scale": 4, "missing_value_fill_truncation": "x", "no_data_text": "No data", "no_prefix": false, "order_min": null, "pretty_print": false, "print_labels": false, "print_values": false, "print_values_position": "center", "print_zeroes": true, "range": null, "rounded_bars": null, "secondary_range": null, "show_dots": true, "show_legend": true, "show_minor_x_labels": true, "show_minor_y_labels": true, "show_only_major_dots": false, "show_x_guides": false, "show_x_labels": true, "show_y_guides": true, "show_y_labels": true, "spacing": 10, "stack_from_top": false, "strict": false, "stroke": true, "stroke_style": null, "style": {"background": "rgba(249, 249, 249, 1)", "ci_colors": [], "colors": ["#F44336", "#3F51B5", "#009688", "#FFC107", "#FF5722", "#9C27B0", "#03A9F4", "#8BC34A", "#FF9800", "#E91E63", "#2196F3", "#4CAF50", "#FFEB3B", "#673AB7", "#00BCD4", "#CDDC39", "#9E9E9E", "#607D8B"], "font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "foreground": "rgba(0, 0, 0, .87)", "foreground_strong": "rgba(0, 0, 0, 1)", "foreground_subtle": "rgba(0, 0, 0, .54)", "guide_stroke_dasharray": "4,4", "label_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "label_font_size": 10, "legend_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "legend_font_size": 14, "major_guide_stroke_dasharray": "6,6", "major_label_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "major_label_font_size": 10, "no_data_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "no_data_font_size": 64, "opacity": ".7", "opacity_hover": ".8", "plot_background": "rgba(255, 255, 255, 1)", "stroke_opacity": ".8", "stroke_opacity_hover": ".9", "title_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "title_font_size": 16, "tooltip_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "tooltip_font_size": 14, "transition": "150ms", "value_background": "rgba(229, 229, 229, 1)", "value_colors": [], "value_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "value_font_size": 16, "value_label_font_family": "Consolas, \\"Liberation Mono\\", Menlo, Courier, monospace", "value_label_font_size": 10}, "title": null, "tooltip_border_radius": 0, "tooltip_fancy_mode": true, "truncate_label": null, "truncate_legend": null, "width": 800, "x_label_rotation": 0, "x_labels": null, "x_labels_major": null, "x_labels_major_count": null, "x_labels_major_every": null, "x_title": null, "xrange": null, "y_label_rotation": 0, "y_labels": null, "y_labels_major": null, "y_labels_major_count": null, "y_labels_major_every": null, "y_title": null, "zero": 0, "legends": ["No Disease age", "Disease age", "No Disease ", "Disease"]}</script><script type="text/javascript" xlink:href="https://kozea.github.io/pygal.js/2.0.x/pygal-tooltips.min.js"/></defs><title>Pygal</title><g class="graph histogram-graph vertical"><rect class="background" height="600" width="800" x="0" y="0"/><g class="plot" transform="translate(181, 20)"><rect class="background" height="540" width="598.4" x="0" y="0"/><g class="axis y always_show"><g class="guides"><path class="axis major line" d="M0.000000 529.615385 h598.400000"/><text class="major" x="-5" y="533.1153846153846">0</text><title>0</title></g><g class="guides"><path class="guide line" d="M0.000000 484.464883 h598.400000"/><text class="" x="-5" y="487.96488294314383">2</text><title>2</title></g><g class="guides"><path class="guide line" d="M0.000000 439.314381 h598.400000"/><text class="" x="-5" y="442.814381270903">4</text><title>4</title></g><g class="guides"><path class="guide line" d="M0.000000 394.163880 h598.400000"/><text class="" x="-5" y="397.6638795986622">6</text><title>6</title></g><g class="guides"><path class="guide line" d="M0.000000 349.013378 h598.400000"/><text class="" x="-5" y="352.5133779264214">8</text><title>8</title></g><g class="guides"><path class="major guide line" d="M0.000000 303.862876 h598.400000"/><text class="major" x="-5" y="307.3628762541806">10</text><title>10</title></g><g class="guides"><path class="guide line" d="M0.000000 258.712375 h598.400000"/><text class="" x="-5" y="262.2123745819398">12</text><title>12</title></g><g class="guides"><path class="guide line" d="M0.000000 213.561873 h598.400000"/><text class="" x="-5" y="217.061872909699">14</text><title>14</title></g><g class="guides"><path class="guide line" d="M0.000000 168.411371 h598.400000"/><text class="" x="-5" y="171.91137123745824">16</text><title>16</title></g><g class="guides"><path class="guide line" d="M0.000000 123.260870 h598.400000"/><text class="" x="-5" y="126.76086956521743">18</text><title>18</title></g><g class="guides"><path class="major guide line" d="M0.000000 78.110368 h598.400000"/><text class="major" x="-5" y="81.61036789297663">20</text><title>20</title></g><g class="guides"><path class="guide line" d="M0.000000 32.959866 h598.400000"/><text class="" x="-5" y="36.45986622073582">22</text><title>22</title></g></g><g class="axis x"><path class="line" d="M0.000000 0.000000 v540.000000"/><g class="guides"><path class="guide line" d="M48.520738 0.000000 v540.000000"/><text class="" x="48.52073774179038" y="555.0">40</text><title>40</title></g><g class="guides"><path class="guide line" d="M115.817184 0.000000 v540.000000"/><text class="" x="115.81718398560506" y="555.0">60</text><title>60</title></g><g class="guides"><path class="guide line" d="M183.113630 0.000000 v540.000000"/><text class="" x="183.11363022941973" y="555.0">80</text><title>80</title></g><g class="guides"><path class="guide line" d="M250.410076 0.000000 v540.000000"/><text class="" x="250.4100764732344" y="555.0">100</text><title>100</title></g><g class="guides"><path class="guide line" d="M317.706523 0.000000 v540.000000"/><text class="" x="317.7065227170491" y="555.0">120</text><title>120</title></g><g class="guides"><path class="guide line" d="M385.002969 0.000000 v540.000000"/><text class="" x="385.00296896086377" y="555.0">140</text><title>140</title></g><g class="guides"><path class="guide line" d="M452.299415 0.000000 v540.000000"/><text class="" x="452.29941520467844" y="555.0">160</text><title>160</title></g><g class="guides"><path class="guide line" d="M519.595861 0.000000 v540.000000"/><text class="" x="519.5958614484931" y="555.0">180</text><title>180</title></g><g class="guides"><path class="guide line" d="M586.892308 0.000000 v540.000000"/><text class="" x="586.8923076923078" y="555.0">200</text><title>200</title></g></g><g class="series serie-0 color-0"><g class="histbars"><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190732" x="11.507692307692315" y="507.04013377926424"/><desc class="value">1: 29</desc><desc class="x centered">13.19010346378768</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907357" x="13.089158794421955" y="529.6153846153846"/><desc class="value">0: 29.47</desc><desc class="x centered">14.771569950517323</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907357" x="14.670625281151608" y="529.6153846153846"/><desc class="value">0: 29.94</desc><desc class="x centered">16.353036437246978</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="16.25209176788125" y="529.6153846153846"/><desc class="value">0: 30.41</desc><desc class="x centered">17.934502923976616</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="17.833558254610892" y="529.6153846153846"/><desc class="value">0: 30.88</desc><desc class="x centered">19.51596941070626</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="19.415024741340545" y="529.6153846153846"/><desc class="value">0: 31.35</desc><desc class="x centered">21.09743589743591</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907357" x="20.996491228070184" y="529.6153846153846"/><desc class="value">0: 31.82</desc><desc class="x centered">22.678902384165553</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907357" x="22.577957714799826" y="529.6153846153846"/><desc class="value">0: 32.29</desc><desc class="x centered">24.260368870895192</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907286" x="24.15942420152947" y="529.6153846153846"/><desc class="value">0: 32.76</desc><desc class="x centered">25.84183535762483</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907286" x="25.74089068825911" y="529.6153846153846"/><desc class="value">0: 33.23</desc><desc class="x centered">27.423301844354476</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190732" x="27.32235717498877" y="484.46488294314383"/><desc class="value">2: 33.7</desc><desc class="x centered">29.004768331084136</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907286" x="28.903823661718416" y="529.6153846153846"/><desc class="value">0: 34.17</desc><desc class="x centered">30.586234817813782</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907392" x="30.48529014844805" y="484.46488294314383"/><desc class="value">2: 34.64</desc><desc class="x centered">32.16770130454342</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="32.0667566351777" y="529.6153846153846"/><desc class="value">0: 35.11</desc><desc class="x centered">33.74916779127307</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="33.648223121907336" y="529.6153846153846"/><desc class="value">0: 35.58</desc><desc class="x centered">35.330634278002705</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="35.229689608636974" y="529.6153846153846"/><desc class="value">0: 36.05</desc><desc class="x centered">36.912100764732344</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="36.81115609536662" y="529.6153846153846"/><desc class="value">0: 36.52</desc><desc class="x centered">38.49356725146198</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907392" x="38.39262258209628" y="484.46488294314383"/><desc class="value">2: 36.99</desc><desc class="x centered">40.07503373819165</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="39.974089068825926" y="529.6153846153846"/><desc class="value">0: 37.46</desc><desc class="x centered">41.65650022492129</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190732" x="41.555555555555564" y="507.04013377926424"/><desc class="value">1: 37.93</desc><desc class="x centered">43.23796671165093</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="43.13702204228521" y="529.6153846153846"/><desc class="value">0: 38.4</desc><desc class="x centered">44.81943319838057</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190725" x="44.71848852901485" y="461.88963210702343"/><desc class="value">3: 38.87</desc><desc class="x centered">46.40089968511021</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="46.29995501574451" y="529.6153846153846"/><desc class="value">0: 39.34</desc><desc class="x centered">47.98236617183987</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190725" x="47.881421502474154" y="507.04013377926424"/><desc class="value">1: 39.81</desc><desc class="x centered">49.56383265856952</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="49.46288798920379" y="529.6153846153846"/><desc class="value">0: 40.28</desc><desc class="x centered">51.14529914529916</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190732" x="51.04435447593344" y="326.438127090301"/><desc class="value">9: 40.75</desc><desc class="x centered">52.7267656320288</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="52.62582096266308" y="529.6153846153846"/><desc class="value">0: 41.22</desc><desc class="x centered">54.30823211875844</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="158.02675585284283" rx="0" ry="0" width="3.364822312190732" x="54.20728744939272" y="371.5886287625418"/><desc class="value">7: 41.69</desc><desc class="x centered">55.88969860548809</desc><desc class="y centered">450.60200668896323</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="55.788753936122355" y="529.6153846153846"/><desc class="value">0: 42.16</desc><desc class="x centered">57.471165092217724</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.364822312190732" x="57.37022042285199" y="416.7391304347826"/><desc class="value">5: 42.63</desc><desc class="x centered">59.052631578947356</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="58.95168690958166" y="529.6153846153846"/><desc class="value">0: 43.1</desc><desc class="x centered">60.63409806567703</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="180.60200668896323" rx="0" ry="0" width="3.364822312190732" x="60.5331533963113" y="349.0133779264214"/><desc class="value">8: 43.57</desc><desc class="x centered">62.21556455240666</desc><desc class="y centered">439.314381270903</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="62.114619883040945" y="529.6153846153846"/><desc class="value">0: 44.04</desc><desc class="x centered">63.79703103913631</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="63.69608636977058" y="529.6153846153846"/><desc class="value">0: 44.51</desc><desc class="x centered">65.37849752586595</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.364822312190725" x="65.27755285650024" y="394.1638795986622"/><desc class="value">6: 44.98</desc><desc class="x centered">66.9599640125956</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="66.85901934322989" y="529.6153846153846"/><desc class="value">0: 45.45</desc><desc class="x centered">68.54143049932526</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.364822312190725" x="68.44048582995954" y="439.314381270903"/><desc class="value">4: 45.92</desc><desc class="x centered">70.12289698605491</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="70.02195231668918" y="529.6153846153846"/><desc class="value">0: 46.39</desc><desc class="x centered">71.70436347278454</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907535" x="71.6034188034188" y="461.88963210702343"/><desc class="value">3: 46.86</desc><desc class="x centered">73.28582995951419</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="73.18488529014846" y="529.6153846153846"/><desc class="value">0: 47.33</desc><desc class="x centered">74.86729644624383</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.364822312190725" x="74.7663517768781" y="439.314381270903"/><desc class="value">4: 47.8</desc><desc class="x centered">76.44876293297347</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="76.34781826360773" y="529.6153846153846"/><desc class="value">0: 48.27</desc><desc class="x centered">78.0302294197031</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="77.92928475033737" y="461.88963210702343"/><desc class="value">3: 48.74</desc><desc class="x centered">79.61169590643274</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="79.51075123706703" y="529.6153846153846"/><desc class="value">0: 49.21</desc><desc class="x centered">81.1931623931624</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907392" x="81.09221772379668" y="439.314381270903"/><desc class="value">4: 49.68</desc><desc class="x centered">82.77462887989205</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="82.67368421052633" y="529.6153846153846"/><desc class="value">0: 50.15</desc><desc class="x centered">84.3560953666217</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190725" x="84.25515069725597" y="326.438127090301"/><desc class="value">9: 50.62</desc><desc class="x centered">85.93756185335133</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="85.83661718398564" y="529.6153846153846"/><desc class="value">0: 51.09</desc><desc class="x centered">87.51902834008101</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190725" x="87.41808367071528" y="326.438127090301"/><desc class="value">9: 51.56</desc><desc class="x centered">89.10049482681063</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="88.99955015744492" y="529.6153846153846"/><desc class="value">0: 52.03</desc><desc class="x centered">90.68196131354028</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="90.58101664417455" y="529.6153846153846"/><desc class="value">0: 52.5</desc><desc class="x centered">92.26342780026992</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907392" x="92.16248313090419" y="394.1638795986622"/><desc class="value">6: 52.97</desc><desc class="x centered">93.84489428699956</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907535" x="93.74394961763383" y="529.6153846153846"/><desc class="value">0: 53.44</desc><desc class="x centered">95.42636077372921</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="225.75250836120404" rx="0" ry="0" width="3.3648223121907392" x="95.32541610436348" y="303.8628762541806"/><desc class="value">10: 53.91</desc><desc class="x centered">97.00782726045885</desc><desc class="y centered">416.7391304347826</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="96.90688259109312" y="529.6153846153846"/><desc class="value">0: 54.38</desc><desc class="x centered">98.58929374718849</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190725" x="98.48834907782276" y="461.88963210702343"/><desc class="value">3: 54.85</desc><desc class="x centered">100.17076023391812</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="100.06981556455243" y="529.6153846153846"/><desc class="value">0: 55.32</desc><desc class="x centered">101.7522267206478</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.364822312190725" x="101.65128205128207" y="416.7391304347826"/><desc class="value">5: 55.79</desc><desc class="x centered">103.33369320737742</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="103.2327485380117" y="529.6153846153846"/><desc class="value">0: 56.26</desc><desc class="x centered">104.91515969410707</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="158.02675585284283" rx="0" ry="0" width="3.364822312190711" x="104.81421502474136" y="371.5886287625418"/><desc class="value">7: 56.73</desc><desc class="x centered">106.49662618083671</desc><desc class="y centered">450.60200668896323</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="106.39568151147101" y="529.6153846153846"/><desc class="value">0: 57.2</desc><desc class="x centered">108.07809266756638</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="158.02675585284283" rx="0" ry="0" width="3.364822312190711" x="107.97714799820066" y="371.5886287625418"/><desc class="value">7: 57.67</desc><desc class="x centered">109.65955915429602</desc><desc class="y centered">450.60200668896323</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="109.5586144849303" y="529.6153846153846"/><desc class="value">0: 58.14</desc><desc class="x centered">111.24102564102566</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.3648223121907392" x="111.14008097165993" y="416.7391304347826"/><desc class="value">5: 58.61</desc><desc class="x centered">112.8224921277553</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="112.72154745838957" y="529.6153846153846"/><desc class="value">0: 59.08</desc><desc class="x centered">114.40395861448494</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907535" x="114.3030139451192" y="461.88963210702343"/><desc class="value">3: 59.55</desc><desc class="x centered">115.98542510121459</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="115.88448043184886" y="529.6153846153846"/><desc class="value">0: 60.02</desc><desc class="x centered">117.56689158794423</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="117.4659469185785" y="529.6153846153846"/><desc class="value">0: 60.49</desc><desc class="x centered">119.14835807467387</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="119.04741340530813" y="507.04013377926424"/><desc class="value">1: 60.96</desc><desc class="x centered">120.7298245614035</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="120.6288798920378" y="529.6153846153846"/><desc class="value">0: 61.43</desc><desc class="x centered">122.31129104813317</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907392" x="122.21034637876744" y="439.314381270903"/><desc class="value">4: 61.9</desc><desc class="x centered">123.89275753486281</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907535" x="123.79181286549708" y="529.6153846153846"/><desc class="value">0: 62.37</desc><desc class="x centered">125.47422402159245</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190725" x="125.37327935222673" y="484.46488294314383"/><desc class="value">2: 62.84</desc><desc class="x centered">127.0556905083221</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="126.95474583895637" y="529.6153846153846"/><desc class="value">0: 63.31</desc><desc class="x centered">128.63715699505175</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907392" x="128.53621232568602" y="394.1638795986622"/><desc class="value">6: 63.78</desc><desc class="x centered">130.21862348178138</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="130.11767881241568" y="529.6153846153846"/><desc class="value">0: 64.25</desc><desc class="x centered">131.80008996851103</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.364822312190711" x="131.69914529914533" y="439.314381270903"/><desc class="value">4: 64.72</desc><desc class="x centered">133.38155645524068</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="133.28061178587495" y="529.6153846153846"/><desc class="value">0: 65.19</desc><desc class="x centered">134.96302294197034</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.364822312190711" x="134.8620782726046" y="439.314381270903"/><desc class="value">4: 65.66</desc><desc class="x centered">136.54448942869996</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="136.44354475933426" y="529.6153846153846"/><desc class="value">0: 66.13</desc><desc class="x centered">138.1259559154296</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907677" x="138.02501124606385" y="461.88963210702343"/><desc class="value">3: 66.6</desc><desc class="x centered">139.70742240215924</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="139.6064777327935" y="529.6153846153846"/><desc class="value">0: 67.07</desc><desc class="x centered">141.2888888888889</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907392" x="141.18794421952316" y="484.46488294314383"/><desc class="value">2: 67.54</desc><desc class="x centered">142.87035537561854</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="142.76941070625278" y="529.6153846153846"/><desc class="value">0: 68.01</desc><desc class="x centered">144.45182186234814</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="144.35087719298244" y="529.6153846153846"/><desc class="value">0: 68.48</desc><desc class="x centered">146.0332883490778</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907392" x="145.9323436797121" y="484.46488294314383"/><desc class="value">2: 68.95</desc><desc class="x centered">147.61475483580745</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="147.51381016644171" y="529.6153846153846"/><desc class="value">0: 69.42</desc><desc class="x centered">149.1962213225371</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="149.0952766531714" y="507.04013377926424"/><desc class="value">1: 69.89</desc><desc class="x centered">150.77768780926675</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="150.67674313990105" y="529.6153846153846"/><desc class="value">0: 70.36</desc><desc class="x centered">152.3591542959964</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="152.2582096266307" y="461.88963210702343"/><desc class="value">3: 70.83</desc><desc class="x centered">153.94062078272606</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="153.83967611336033" y="529.6153846153846"/><desc class="value">0: 71.3</desc><desc class="x centered">155.5220872694557</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="155.42114260008998" y="529.6153846153846"/><desc class="value">0: 71.77</desc><desc class="x centered">157.10355375618536</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="157.00260908681963" y="529.6153846153846"/><desc class="value">0: 72.24</desc><desc class="x centered">158.685020242915</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="158.5840755735493" y="529.6153846153846"/><desc class="value">0: 72.71</desc><desc class="x centered">160.26648672964467</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="160.16554206027894" y="529.6153846153846"/><desc class="value">0: 73.18</desc><desc class="x centered">161.84795321637432</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="161.7470085470086" y="507.04013377926424"/><desc class="value">1: 73.65</desc><desc class="x centered">163.42941970310397</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="163.32847503373824" y="529.6153846153846"/><desc class="value">0: 74.12</desc><desc class="x centered">165.01088618983363</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="164.90994152046787" y="529.6153846153846"/><desc class="value">0: 74.59</desc><desc class="x centered">166.59235267656322</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="166.49140800719752" y="529.6153846153846"/><desc class="value">0: 75.06</desc><desc class="x centered">168.17381916329288</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="168.07287449392715" y="507.04013377926424"/><desc class="value">1: 75.53</desc><desc class="x centered">169.75528565002253</desc><desc class="y centered">518.3277591973244</desc></g></g></g><g class="series serie-1 color-1"><g class="histbars"><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190732" x="31.696626180836716" y="484.46488294314383"/><desc class="value">2: 35</desc><desc class="x centered">33.379037336932086</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="33.10985155195683" y="529.6153846153846"/><desc class="value">0: 35.42</desc><desc class="x centered">34.79226270805219</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="34.52307692307694" y="529.6153846153846"/><desc class="value">0: 35.84</desc><desc class="x centered">36.20548807917231</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="35.936302294197034" y="529.6153846153846"/><desc class="value">0: 36.26</desc><desc class="x centered">37.618713450292404</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="37.34952766531715" y="529.6153846153846"/><desc class="value">0: 36.68</desc><desc class="x centered">39.03193882141251</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="38.76275303643726" y="529.6153846153846"/><desc class="value">0: 37.1</desc><desc class="x centered">40.44516419253263</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="40.17597840755737" y="529.6153846153846"/><desc class="value">0: 37.52</desc><desc class="x centered">41.85838956365274</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="41.589203778677465" y="507.04013377926424"/><desc class="value">1: 37.94</desc><desc class="x centered">43.271614934772835</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="43.00242914979758" y="529.6153846153846"/><desc class="value">0: 38.36</desc><desc class="x centered">44.68484030589295</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190732" x="44.41565452091769" y="507.04013377926424"/><desc class="value">1: 38.78</desc><desc class="x centered">46.098065677013054</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="45.828879892037804" y="529.6153846153846"/><desc class="value">0: 39.2</desc><desc class="x centered">47.511291048133174</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190732" x="47.2421052631579" y="484.46488294314383"/><desc class="value">2: 39.62</desc><desc class="x centered">48.924516419253266</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="48.65533063427801" y="529.6153846153846"/><desc class="value">0: 40.04</desc><desc class="x centered">50.33774179037338</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="50.06855600539812" y="529.6153846153846"/><desc class="value">0: 40.46</desc><desc class="x centered">51.75096716149349</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190732" x="51.481781376518235" y="507.04013377926424"/><desc class="value">1: 40.88</desc><desc class="x centered">53.1641925326136</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="52.89500674763833" y="529.6153846153846"/><desc class="value">0: 41.3</desc><desc class="x centered">54.577417903733696</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190732" x="54.30823211875844" y="507.04013377926424"/><desc class="value">1: 41.72</desc><desc class="x centered">55.99064327485381</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="55.72145748987855" y="529.6153846153846"/><desc class="value">0: 42.14</desc><desc class="x centered">57.403868645973915</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="57.13468286099867" y="529.6153846153846"/><desc class="value">0: 42.56</desc><desc class="x centered">58.817094017094036</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="58.54790823211876" y="461.88963210702343"/><desc class="value">3: 42.98</desc><desc class="x centered">60.23031938821413</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190732" x="59.96113360323887" y="529.6153846153846"/><desc class="value">0: 43.4</desc><desc class="x centered">61.64354475933423</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="61.374358974358984" y="461.88963210702343"/><desc class="value">3: 43.82</desc><desc class="x centered">63.05677013045435</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="62.787584345479104" y="529.6153846153846"/><desc class="value">0: 44.24</desc><desc class="x centered">64.46999550157446</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907535" x="64.20080971659918" y="484.46488294314383"/><desc class="value">2: 44.66</desc><desc class="x centered">65.88322087269455</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="65.6140350877193" y="529.6153846153846"/><desc class="value">0: 45.08</desc><desc class="x centered">67.29644624381467</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="67.02726045883942" y="529.6153846153846"/><desc class="value">0: 45.5</desc><desc class="x centered">68.70967161493479</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190725" x="68.44048582995954" y="461.88963210702343"/><desc class="value">3: 45.92</desc><desc class="x centered">70.12289698605491</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="69.85371120107965" y="529.6153846153846"/><desc class="value">0: 46.34</desc><desc class="x centered">71.53612235717502</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190725" x="71.26693657219974" y="484.46488294314383"/><desc class="value">2: 46.76</desc><desc class="x centered">72.9493477282951</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="72.68016194331985" y="529.6153846153846"/><desc class="value">0: 47.18</desc><desc class="x centered">74.36257309941521</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="74.09338731443997" y="461.88963210702343"/><desc class="value">3: 47.6</desc><desc class="x centered">75.77579847053534</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="75.50661268556006" y="529.6153846153846"/><desc class="value">0: 48.02</desc><desc class="x centered">77.18902384165543</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="76.91983805668016" y="529.6153846153846"/><desc class="value">0: 48.44</desc><desc class="x centered">78.60224921277553</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190725" x="78.33306342780028" y="484.46488294314383"/><desc class="value">2: 48.86</desc><desc class="x centered">80.01547458389564</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="79.7462887989204" y="529.6153846153846"/><desc class="value">0: 49.28</desc><desc class="x centered">81.42869995501576</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="81.15951417004051" y="461.88963210702343"/><desc class="value">3: 49.7</desc><desc class="x centered">82.84192532613588</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="82.5727395411606" y="529.6153846153846"/><desc class="value">0: 50.12</desc><desc class="x centered">84.25515069725597</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="83.98596491228072" y="529.6153846153846"/><desc class="value">0: 50.54</desc><desc class="x centered">85.66837606837609</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907392" x="85.39919028340083" y="461.88963210702343"/><desc class="value">3: 50.96</desc><desc class="x centered">87.0816014394962</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="86.81241565452092" y="529.6153846153846"/><desc class="value">0: 51.38</desc><desc class="x centered">88.49482681061627</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907392" x="88.22564102564102" y="439.314381270903"/><desc class="value">4: 51.8</desc><desc class="x centered">89.9080521817364</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="89.63886639676114" y="529.6153846153846"/><desc class="value">0: 52.22</desc><desc class="x centered">91.32127755285651</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190725" x="91.05209176788127" y="484.46488294314383"/><desc class="value">2: 52.64</desc><desc class="x centered">92.73450292397663</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="92.46531713900139" y="529.6153846153846"/><desc class="value">0: 53.06</desc><desc class="x centered">94.14772829509674</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="93.87854251012149" y="529.6153846153846"/><desc class="value">0: 53.48</desc><desc class="x centered">95.56095366621685</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907392" x="95.29176788124157" y="394.1638795986622"/><desc class="value">6: 53.9</desc><desc class="x centered">96.97417903733694</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="96.70499325236169" y="529.6153846153846"/><desc class="value">0: 54.32</desc><desc class="x centered">98.38740440845706</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.3648223121907392" x="98.11821862348178" y="416.7391304347826"/><desc class="value">5: 54.74</desc><desc class="x centered">99.80062977957715</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="99.5314439946019" y="529.6153846153846"/><desc class="value">0: 55.16</desc><desc class="x centered">101.21385515069726</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="100.944669365722" y="529.6153846153846"/><desc class="value">0: 55.58</desc><desc class="x centered">102.62708052181736</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.364822312190725" x="102.35789473684213" y="394.1638795986622"/><desc class="value">6: 56</desc><desc class="x centered">104.04030589293748</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="103.77112010796225" y="529.6153846153846"/><desc class="value">0: 56.42</desc><desc class="x centered">105.4535312640576</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="225.75250836120404" rx="0" ry="0" width="3.364822312190725" x="105.18434547908235" y="303.8628762541806"/><desc class="value">10: 56.84</desc><desc class="x centered">106.86675663517772</desc><desc class="y centered">416.7391304347826</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="106.59757085020243" y="529.6153846153846"/><desc class="value">0: 57.26</desc><desc class="x centered">108.2799820062978</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="270.90301003344484" rx="0" ry="0" width="3.3648223121907392" x="108.01079622132255" y="258.7123745819398"/><desc class="value">12: 57.68</desc><desc class="x centered">109.69320737741792</desc><desc class="y centered">394.1638795986622</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="109.42402159244264" y="529.6153846153846"/><desc class="value">0: 58.1</desc><desc class="x centered">111.10643274853801</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="110.83724696356276" y="529.6153846153846"/><desc class="value">0: 58.52</desc><desc class="x centered">112.51965811965812</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190711" x="112.25047233468288" y="326.438127090301"/><desc class="value">9: 58.94</desc><desc class="x centered">113.93288349077824</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="113.66369770580299" y="529.6153846153846"/><desc class="value">0: 59.36</desc><desc class="x centered">115.34610886189836</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190725" x="115.07692307692311" y="326.438127090301"/><desc class="value">9: 59.78</desc><desc class="x centered">116.75933423301848</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="116.4901484480432" y="529.6153846153846"/><desc class="value">0: 60.2</desc><desc class="x centered">118.17255960413857</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="158.02675585284283" rx="0" ry="0" width="3.3648223121907535" x="117.90337381916329" y="371.5886287625418"/><desc class="value">7: 60.62</desc><desc class="x centered">119.58578497525866</desc><desc class="y centered">450.60200668896323</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="119.31659919028341" y="529.6153846153846"/><desc class="value">0: 61.04</desc><desc class="x centered">120.99901034637878</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190725" x="120.7298245614035" y="529.6153846153846"/><desc class="value">0: 61.46</desc><desc class="x centered">122.41223571749887</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="158.02675585284283" rx="0" ry="0" width="3.364822312190725" x="122.14304993252362" y="371.5886287625418"/><desc class="value">7: 61.88</desc><desc class="x centered">123.825461088619</desc><desc class="y centered">450.60200668896323</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="123.55627530364374" y="529.6153846153846"/><desc class="value">0: 62.3</desc><desc class="x centered">125.2386864597391</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907392" x="124.96950067476385" y="394.1638795986622"/><desc class="value">6: 62.72</desc><desc class="x centered">126.65191183085922</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="126.38272604588397" y="529.6153846153846"/><desc class="value">0: 63.14</desc><desc class="x centered">128.06513720197933</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="127.79595141700406" y="529.6153846153846"/><desc class="value">0: 63.56</desc><desc class="x centered">129.47836257309945</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907392" x="129.20917678812418" y="439.314381270903"/><desc class="value">4: 63.98</desc><desc class="x centered">130.89158794421957</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="130.6224021592443" y="529.6153846153846"/><desc class="value">0: 64.4</desc><desc class="x centered">132.3048133153397</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907392" x="132.03562753036437" y="439.314381270903"/><desc class="value">4: 64.82</desc><desc class="x centered">133.71803868645975</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="133.44885290148449" y="529.6153846153846"/><desc class="value">0: 65.24</desc><desc class="x centered">135.13126405757984</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190711" x="134.8620782726046" y="461.88963210702343"/><desc class="value">3: 65.66</desc><desc class="x centered">136.54448942869996</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="136.27530364372473" y="529.6153846153846"/><desc class="value">0: 66.08</desc><desc class="x centered">137.95771479982008</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="137.68852901484482" y="529.6153846153846"/><desc class="value">0: 66.5</desc><desc class="x centered">139.37094017094017</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907392" x="139.10175438596494" y="394.1638795986622"/><desc class="value">6: 66.92</desc><desc class="x centered">140.7841655420603</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="140.51497975708506" y="529.6153846153846"/><desc class="value">0: 67.34</desc><desc class="x centered">142.1973909131804</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="141.92820512820512" y="484.46488294314383"/><desc class="value">2: 67.76</desc><desc class="x centered">143.61061628430048</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="143.34143049932527" y="529.6153846153846"/><desc class="value">0: 68.18</desc><desc class="x centered">145.02384165542065</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="144.75465587044536" y="507.04013377926424"/><desc class="value">1: 68.6</desc><desc class="x centered">146.43706702654072</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="146.16788124156548" y="529.6153846153846"/><desc class="value">0: 69.02</desc><desc class="x centered">147.85029239766084</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="147.58110661268557" y="529.6153846153846"/><desc class="value">0: 69.44</desc><desc class="x centered">149.26351776878096</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907677" x="148.99433198380567" y="461.88963210702343"/><desc class="value">3: 69.86</desc><desc class="x centered">150.67674313990105</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="150.40755735492579" y="529.6153846153846"/><desc class="value">0: 70.28</desc><desc class="x centered">152.08996851102114</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="151.82078272604588" y="529.6153846153846"/><desc class="value">0: 70.7</desc><desc class="x centered">153.50319388214123</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="153.23400809716603" y="529.6153846153846"/><desc class="value">0: 71.12</desc><desc class="x centered">154.91641925326138</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="154.6472334682861" y="529.6153846153846"/><desc class="value">0: 71.54</desc><desc class="x centered">156.32964462438144</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="156.06045883940627" y="529.6153846153846"/><desc class="value">0: 71.96</desc><desc class="x centered">157.74286999550162</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="157.47368421052633" y="529.6153846153846"/><desc class="value">0: 72.38</desc><desc class="x centered">159.15609536662168</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="158.88690958164642" y="529.6153846153846"/><desc class="value">0: 72.8</desc><desc class="x centered">160.5693207377418</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="160.30013495276654" y="529.6153846153846"/><desc class="value">0: 73.22</desc><desc class="x centered">161.98254610886192</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="161.71336032388666" y="529.6153846153846"/><desc class="value">0: 73.64</desc><desc class="x centered">163.39577147998205</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="163.12658569500678" y="529.6153846153846"/><desc class="value">0: 74.06</desc><desc class="x centered">164.80899685110217</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="164.53981106612684" y="529.6153846153846"/><desc class="value">0: 74.48</desc><desc class="x centered">166.22222222222223</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="165.953036437247" y="529.6153846153846"/><desc class="value">0: 74.9</desc><desc class="x centered">167.63544759334238</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="167.36626180836708" y="529.6153846153846"/><desc class="value">0: 75.32</desc><desc class="x centered">169.04867296446244</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="168.77948717948723" y="529.6153846153846"/><desc class="value">0: 75.74</desc><desc class="x centered">170.4618983355826</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="170.1927125506073" y="529.6153846153846"/><desc class="value">0: 76.16</desc><desc class="x centered">171.87512370670265</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="171.60593792172742" y="507.04013377926424"/><desc class="value">1: 76.58</desc><desc class="x centered">173.28834907782277</desc><desc class="y centered">518.3277591973244</desc></g></g></g><g class="series serie-2 color-2"><g class="histbars"><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907392" x="230.22114260009" y="484.46488294314383"/><desc class="value">2: 94</desc><desc class="x centered">231.90355375618537</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="233.11488978857403" y="529.6153846153846"/><desc class="value">0: 94.86</desc><desc class="x centered">234.7973009446694</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="236.00863697705805" y="529.6153846153846"/><desc class="value">0: 95.72</desc><desc class="x centered">237.6910481331534</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="238.9023841655421" y="529.6153846153846"/><desc class="value">0: 96.58</desc><desc class="x centered">240.58479532163744</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="241.7961313540261" y="529.6153846153846"/><desc class="value">0: 97.44</desc><desc class="x centered">243.47854251012149</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121906824" x="244.68987854251017" y="529.6153846153846"/><desc class="value">0: 98.3</desc><desc class="x centered">246.37228969860553</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907677" x="247.58362573099416" y="484.46488294314383"/><desc class="value">2: 99.16</desc><desc class="x centered">249.26603688708954</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907392" x="250.47737291947817" y="529.6153846153846"/><desc class="value">0: 100.02</desc><desc class="x centered">252.15978407557355</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907392" x="253.37112010796224" y="507.04013377926424"/><desc class="value">1: 100.88</desc><desc class="x centered">255.0535312640576</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="256.26486729644625" y="484.46488294314383"/><desc class="value">2: 101.74</desc><desc class="x centered">257.9472784525416</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="259.1586144849303" y="529.6153846153846"/><desc class="value">0: 102.6</desc><desc class="x centered">260.8410256410257</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="262.0523616734143" y="507.04013377926424"/><desc class="value">1: 103.46</desc><desc class="x centered">263.73477282950967</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907677" x="264.9461088618983" y="461.88963210702343"/><desc class="value">3: 104.32</desc><desc class="x centered">266.6285200179937</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="267.8398560503824" y="507.04013377926424"/><desc class="value">1: 105.18</desc><desc class="x centered">269.52226720647775</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="270.7336032388664" y="529.6153846153846"/><desc class="value">0: 106.04</desc><desc class="x centered">272.4160143949618</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="273.62735042735045" y="529.6153846153846"/><desc class="value">0: 106.9</desc><desc class="x centered">275.30976158344583</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907677" x="276.5210976158345" y="439.314381270903"/><desc class="value">4: 107.76</desc><desc class="x centered">278.2035087719299</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="279.41484480431853" y="529.6153846153846"/><desc class="value">0: 108.62</desc><desc class="x centered">281.0972559604139</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="180.60200668896323" rx="0" ry="0" width="3.364822312190711" x="282.3085919928026" y="349.0133779264214"/><desc class="value">8: 109.48</desc><desc class="x centered">283.9910031488979</desc><desc class="y centered">439.314381270903</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="285.20233918128656" y="529.6153846153846"/><desc class="value">0: 110.34</desc><desc class="x centered">286.88475033738194</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.3648223121907677" x="288.0960863697706" y="416.7391304347826"/><desc class="value">5: 111.2</desc><desc class="x centered">289.778497525866</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="290.98983355825465" y="529.6153846153846"/><desc class="value">0: 112.06</desc><desc class="x centered">292.67224471435</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="293.8835807467387" y="529.6153846153846"/><desc class="value">0: 112.92</desc><desc class="x centered">295.56599190283407</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="296.7773279352227" y="529.6153846153846"/><desc class="value">0: 113.78</desc><desc class="x centered">298.45973909131806</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190711" x="299.67107512370677" y="461.88963210702343"/><desc class="value">3: 114.64</desc><desc class="x centered">301.35348627980215</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="302.56482231219076" y="529.6153846153846"/><desc class="value">0: 115.5</desc><desc class="x centered">304.24723346828614</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="305.4585695006748" y="529.6153846153846"/><desc class="value">0: 116.36</desc><desc class="x centered">307.1409806567702</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.364822312190711" x="308.35231668915884" y="416.7391304347826"/><desc class="value">5: 117.22</desc><desc class="x centered">310.0347278452542</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="311.2460638776429" y="529.6153846153846"/><desc class="value">0: 118.08</desc><desc class="x centered">312.9284750337382</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="314.13981106612687" y="529.6153846153846"/><desc class="value">0: 118.94</desc><desc class="x centered">315.82222222222225</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="519.2307692307692" rx="0" ry="0" width="3.364822312190711" x="317.0335582546109" y="10.384615384615472"/><desc class="value">23: 119.8</desc><desc class="x centered">318.7159694107063</desc><desc class="y centered">270.00000000000006</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="319.92730544309495" y="529.6153846153846"/><desc class="value">0: 120.66</desc><desc class="x centered">321.6097165991903</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190711" x="322.821052631579" y="461.88963210702343"/><desc class="value">3: 121.52</desc><desc class="x centered">324.5034637876744</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="325.714799820063" y="529.6153846153846"/><desc class="value">0: 122.38</desc><desc class="x centered">327.39721097615836</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="328.608547008547" y="484.46488294314383"/><desc class="value">2: 123.24</desc><desc class="x centered">330.29095816464235</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="331.50229419703106" y="529.6153846153846"/><desc class="value">0: 124.1</desc><desc class="x centered">333.18470535312645</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.364822312190711" x="334.39604138551516" y="439.314381270903"/><desc class="value">4: 124.96</desc><desc class="x centered">336.07845254161055</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="337.2897885739991" y="507.04013377926424"/><desc class="value">1: 125.82</desc><desc class="x centered">338.9721997300945</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="340.1835357624832" y="529.6153846153846"/><desc class="value">0: 126.68</desc><desc class="x centered">341.8659469185785</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907677" x="343.0772829509672" y="394.1638795986622"/><desc class="value">6: 127.54</desc><desc class="x centered">344.75969410706256</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="345.9710301394513" y="507.04013377926424"/><desc class="value">1: 128.4</desc><desc class="x centered">347.6534412955466</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="519.2307692307692" rx="0" ry="0" width="3.364822312190711" x="348.86477732793526" y="10.384615384615472"/><desc class="value">23: 129.26</desc><desc class="x centered">350.5471884840306</desc><desc class="y centered">270.00000000000006</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="351.7585245164193" y="529.6153846153846"/><desc class="value">0: 130.12</desc><desc class="x centered">353.4409356725147</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="354.6522717049033" y="529.6153846153846"/><desc class="value">0: 130.98</desc><desc class="x centered">356.33468286099867</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190711" x="357.5460188933874" y="461.88963210702343"/><desc class="value">3: 131.84</desc><desc class="x centered">359.22843004948277</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="360.43976608187137" y="529.6153846153846"/><desc class="value">0: 132.7</desc><desc class="x centered">362.12217723796675</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="363.3335132703554" y="484.46488294314383"/><desc class="value">2: 133.56</desc><desc class="x centered">365.01592442645074</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.364822312190711" x="366.2272604588395" y="416.7391304347826"/><desc class="value">5: 134.42</desc><desc class="x centered">367.90967161493484</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121908245" x="369.12100764732344" y="507.04013377926424"/><desc class="value">1: 135.28</desc><desc class="x centered">370.8034188034188</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="372.0147548358075" y="529.6153846153846"/><desc class="value">0: 136.14</desc><desc class="x centered">373.6971659919028</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="374.9085020242916" y="529.6153846153846"/><desc class="value">0: 137</desc><desc class="x centered">376.5909131803869</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190654" x="377.8022492127757" y="326.438127090301"/><desc class="value">9: 137.86</desc><desc class="x centered">379.484660368871</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="380.6959964012596" y="529.6153846153846"/><desc class="value">0: 138.72</desc><desc class="x centered">382.378407557355</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="383.7792642140468" rx="0" ry="0" width="3.364822312190711" x="383.5897435897436" y="145.83612040133784"/><desc class="value">17: 139.58</desc><desc class="x centered">385.272154745839</desc><desc class="y centered">337.7257525083612</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="386.4834907782277" y="529.6153846153846"/><desc class="value">0: 140.44</desc><desc class="x centered">388.1659019343231</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121908245" x="389.3772379667117" y="484.46488294314383"/><desc class="value">2: 141.3</desc><desc class="x centered">391.05964912280706</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="392.2709851551957" y="529.6153846153846"/><desc class="value">0: 142.16</desc><desc class="x centered">393.9533963112911</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="395.1647323436797" y="529.6153846153846"/><desc class="value">0: 143.02</desc><desc class="x centered">396.84714349977503</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="398.0584795321638" y="529.6153846153846"/><desc class="value">0: 143.88</desc><desc class="x centered">399.7408906882591</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121908245" x="400.9522267206478" y="529.6153846153846"/><desc class="value">0: 144.74</desc><desc class="x centered">402.6346378767432</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="403.8459739091318" y="507.04013377926424"/><desc class="value">1: 145.6</desc><desc class="x centered">405.5283850652272</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="406.7397210976159" y="529.6153846153846"/><desc class="value">0: 146.46</desc><desc class="x centered">408.4221322537113</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="409.6334682860999" y="507.04013377926424"/><desc class="value">1: 147.32</desc><desc class="x centered">411.3158794421953</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="412.52721547458395" y="529.6153846153846"/><desc class="value">0: 148.18</desc><desc class="x centered">414.20962663067934</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="415.42096266306794" y="529.6153846153846"/><desc class="value">0: 149.04</desc><desc class="x centered">417.10337381916327</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="203.17725752508363" rx="0" ry="0" width="3.364822312190711" x="418.31470985155204" y="326.438127090301"/><desc class="value">9: 149.9</desc><desc class="x centered">419.99712100764737</desc><desc class="y centered">428.0267558528428</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="421.208457040036" y="529.6153846153846"/><desc class="value">0: 150.76</desc><desc class="x centered">422.89086819613135</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907677" x="424.10220422852007" y="484.46488294314383"/><desc class="value">2: 151.62</desc><desc class="x centered">425.78461538461545</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="426.99595141700405" y="529.6153846153846"/><desc class="value">0: 152.48</desc><desc class="x centered">428.67836257309943</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="429.88969860548815" y="529.6153846153846"/><desc class="value">0: 153.34</desc><desc class="x centered">431.57210976158353</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190654" x="432.7834457939722" y="507.04013377926424"/><desc class="value">1: 154.2</desc><desc class="x centered">434.4658569500675</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="435.67719298245623" y="529.6153846153846"/><desc class="value">0: 155.06</desc><desc class="x centered">437.3596041385516</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="438.57094017094033" y="507.04013377926424"/><desc class="value">1: 155.92</desc><desc class="x centered">440.2533513270357</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="441.4646873594242" y="529.6153846153846"/><desc class="value">0: 156.78</desc><desc class="x centered">443.1470985155196</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="444.3584345479083" y="529.6153846153846"/><desc class="value">0: 157.64</desc><desc class="x centered">446.04084570400363</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="447.2521817363924" y="529.6153846153846"/><desc class="value">0: 158.5</desc><desc class="x centered">448.93459289248773</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.364822312190711" x="450.14592892487644" y="416.7391304347826"/><desc class="value">5: 159.36</desc><desc class="x centered">451.82834008097177</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="453.0396761133603" y="529.6153846153846"/><desc class="value">0: 160.22</desc><desc class="x centered">454.7220872694557</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="455.9334233018444" y="529.6153846153846"/><desc class="value">0: 161.08</desc><desc class="x centered">457.61583445793974</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="458.8271704903285" y="529.6153846153846"/><desc class="value">0: 161.94</desc><desc class="x centered">460.50958164642384</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="461.7209176788126" y="529.6153846153846"/><desc class="value">0: 162.8</desc><desc class="x centered">463.40332883490794</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="464.6146648672964" y="529.6153846153846"/><desc class="value">0: 163.66</desc><desc class="x centered">466.2970760233918</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="467.5084120557805" y="529.6153846153846"/><desc class="value">0: 164.52</desc><desc class="x centered">469.1908232118759</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="470.4021592442646" y="529.6153846153846"/><desc class="value">0: 165.38</desc><desc class="x centered">472.08457040036</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="473.2959064327487" y="529.6153846153846"/><desc class="value">0: 166.24</desc><desc class="x centered">474.9783175888441</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="476.18965362123254" y="529.6153846153846"/><desc class="value">0: 167.1</desc><desc class="x centered">477.87206477732786</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="479.08340080971664" y="529.6153846153846"/><desc class="value">0: 167.96</desc><desc class="x centered">480.76581196581196</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="481.97714799820073" y="529.6153846153846"/><desc class="value">0: 168.82</desc><desc class="x centered">483.65955915429606</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="484.87089518668483" y="507.04013377926424"/><desc class="value">1: 169.68</desc><desc class="x centered">486.55330634278016</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="487.76464237516865" y="529.6153846153846"/><desc class="value">0: 170.54</desc><desc class="x centered">489.44705353126403</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="490.65838956365275" y="507.04013377926424"/><desc class="value">1: 171.4</desc><desc class="x centered">492.34080071974813</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="493.55213675213685" y="529.6153846153846"/><desc class="value">0: 172.26</desc><desc class="x centered">495.23454790823223</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="496.44588394062094" y="529.6153846153846"/><desc class="value">0: 173.12</desc><desc class="x centered">498.12829509671633</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121908245" x="499.33963112910493" y="529.6153846153846"/><desc class="value">0: 173.98</desc><desc class="x centered">501.0220422852003</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="502.23337831758886" y="529.6153846153846"/><desc class="value">0: 174.84</desc><desc class="x centered">503.9157894736842</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="505.12712550607296" y="529.6153846153846"/><desc class="value">0: 175.7</desc><desc class="x centered">506.8095366621683</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="508.020872694557" y="529.6153846153846"/><desc class="value">0: 176.56</desc><desc class="x centered">509.7032838506524</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="510.9146198830411" y="507.04013377926424"/><desc class="value">1: 177.42</desc><desc class="x centered">512.5970310391365</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="513.808367071525" y="529.6153846153846"/><desc class="value">0: 178.28</desc><desc class="x centered">515.4907782276204</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190654" x="516.7021142600091" y="507.04013377926424"/><desc class="value">1: 179.14</desc><desc class="x centered">518.3845254161045</desc><desc class="y centered">518.3277591973244</desc></g></g></g><g class="series serie-3 color-3"><g class="histbars"><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907392" x="250.4100764732344" y="484.46488294314383"/><desc class="value">2: 100</desc><desc class="x centered">252.09248762932975</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="253.77489878542514" y="529.6153846153846"/><desc class="value">0: 101</desc><desc class="x centered">255.45730994152052</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="257.1397210976159" y="529.6153846153846"/><desc class="value">0: 102</desc><desc class="x centered">258.8221322537113</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="260.5045434098066" y="529.6153846153846"/><desc class="value">0: 103</desc><desc class="x centered">262.18695456590194</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="263.8693657219973" y="529.6153846153846"/><desc class="value">0: 104</desc><desc class="x centered">265.5517768780927</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="267.2341880341881" y="529.6153846153846"/><desc class="value">0: 105</desc><desc class="x centered">268.9165991902835</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="270.5990103463788" y="529.6153846153846"/><desc class="value">0: 106</desc><desc class="x centered">272.2814215024742</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="273.9638326585696" y="529.6153846153846"/><desc class="value">0: 107</desc><desc class="x centered">275.6462438146649</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="277.3286549707603" y="484.46488294314383"/><desc class="value">2: 108</desc><desc class="x centered">279.01106612685567</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="280.693477282951" y="529.6153846153846"/><desc class="value">0: 109</desc><desc class="x centered">282.3758884390464</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="248.32775919732444" rx="0" ry="0" width="3.364822312190711" x="284.05829959514176" y="281.2876254180602"/><desc class="value">11: 110</desc><desc class="x centered">285.7407107512371</desc><desc class="y centered">405.4515050167224</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="287.4231219073325" y="529.6153846153846"/><desc class="value">0: 111</desc><desc class="x centered">289.10553306342786</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907677" x="290.7879442195232" y="439.314381270903"/><desc class="value">4: 112</desc><desc class="x centered">292.47035537561857</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="294.15276653171395" y="529.6153846153846"/><desc class="value">0: 113</desc><desc class="x centered">295.8351776878093</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="297.51758884390466" y="507.04013377926424"/><desc class="value">1: 114</desc><desc class="x centered">299.20000000000005</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="300.88241115609543" y="529.6153846153846"/><desc class="value">0: 115</desc><desc class="x centered">302.5648223121908</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="304.24723346828614" y="529.6153846153846"/><desc class="value">0: 116</desc><desc class="x centered">305.92964462438147</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="307.61205578047685" y="507.04013377926424"/><desc class="value">1: 117</desc><desc class="x centered">309.29446693657223</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="310.9768780926676" y="484.46488294314383"/><desc class="value">2: 118</desc><desc class="x centered">312.659289248763</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="314.34170040485833" y="529.6153846153846"/><desc class="value">0: 119</desc><desc class="x centered">316.0241115609537</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="316.05351170568565" rx="0" ry="0" width="3.364822312190711" x="317.7065227170491" y="213.561872909699"/><desc class="value">14: 120</desc><desc class="x centered">319.3889338731444</desc><desc class="y centered">371.5886287625418</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="321.0713450292398" y="529.6153846153846"/><desc class="value">0: 121</desc><desc class="x centered">322.7537561853352</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="324.4361673414305" y="507.04013377926424"/><desc class="value">1: 122</desc><desc class="x centered">326.1185784975259</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="327.8009896536213" y="507.04013377926424"/><desc class="value">1: 123</desc><desc class="x centered">329.4834008097166</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.3648223121907677" x="331.165811965812" y="439.314381270903"/><desc class="value">4: 124</desc><desc class="x centered">332.8482231219074</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="158.02675585284283" rx="0" ry="0" width="3.364822312190711" x="334.53063427800276" y="371.5886287625418"/><desc class="value">7: 125</desc><desc class="x centered">336.21304543409815</desc><desc class="y centered">450.60200668896323</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.364822312190711" x="337.8954565901935" y="484.46488294314383"/><desc class="value">2: 126</desc><desc class="x centered">339.5778677462888</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="341.2602789023842" y="529.6153846153846"/><desc class="value">0: 127</desc><desc class="x centered">342.94269005847957</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.364822312190711" x="344.62510121457495" y="394.1638795986622"/><desc class="value">6: 128</desc><desc class="x centered">346.30751237067034</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="347.98992352676566" y="529.6153846153846"/><desc class="value">0: 129</desc><desc class="x centered">349.67233468286105</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="293.47826086956525" rx="0" ry="0" width="3.364822312190711" x="351.35474583895643" y="236.1371237458194"/><desc class="value">13: 130</desc><desc class="x centered">353.03715699505176</desc><desc class="y centered">382.876254180602</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="354.71956815114714" y="529.6153846153846"/><desc class="value">0: 131</desc><desc class="x centered">356.4019793072425</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="112.87625418060202" rx="0" ry="0" width="3.3648223121907677" x="358.08439046333785" y="416.7391304347826"/><desc class="value">5: 132</desc><desc class="x centered">359.76680161943324</desc><desc class="y centered">473.17725752508363</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="361.4492127755286" y="529.6153846153846"/><desc class="value">0: 133</desc><desc class="x centered">363.13162393162395</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907677" x="364.81403508771933" y="461.88963210702343"/><desc class="value">3: 134</desc><desc class="x centered">366.4964462438147</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="368.1788573999101" y="507.04013377926424"/><desc class="value">1: 135</desc><desc class="x centered">369.8612685560055</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907677" x="371.5436797121008" y="484.46488294314383"/><desc class="value">2: 136</desc><desc class="x centered">373.2260908681962</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="374.9085020242916" y="529.6153846153846"/><desc class="value">0: 137</desc><desc class="x centered">376.5909131803869</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907677" x="378.2733243364823" y="461.88963210702343"/><desc class="value">3: 138</desc><desc class="x centered">379.9557354925777</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="381.63814664867306" y="529.6153846153846"/><desc class="value">0: 139</desc><desc class="x centered">383.32055780476844</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="338.628762541806" rx="0" ry="0" width="3.364822312190711" x="385.00296896086377" y="190.98662207357864"/><desc class="value">15: 140</desc><desc class="x centered">386.6853801169591</desc><desc class="y centered">360.3010033444816</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="388.3677912730545" y="529.6153846153846"/><desc class="value">0: 141</desc><desc class="x centered">390.05020242914986</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="391.73261358524525" y="507.04013377926424"/><desc class="value">1: 142</desc><desc class="x centered">393.41502474134063</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="395.09743589743596" y="529.6153846153846"/><desc class="value">0: 143</desc><desc class="x centered">396.7798470535313</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121908245" x="398.4622582096266" y="484.46488294314383"/><desc class="value">2: 144</desc><desc class="x centered">400.14466936572205</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="90.30100334448161" rx="0" ry="0" width="3.364822312190711" x="401.82708052181744" y="439.314381270903"/><desc class="value">4: 145</desc><desc class="x centered">403.5094916779128</desc><desc class="y centered">484.46488294314383</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="405.19190283400815" y="507.04013377926424"/><desc class="value">1: 146</desc><desc class="x centered">406.87431399010353</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="408.5567251461989" y="529.6153846153846"/><desc class="value">0: 147</desc><desc class="x centered">410.23913630229424</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190654" x="411.9215474583896" y="507.04013377926424"/><desc class="value">1: 148</desc><desc class="x centered">413.60395861448495</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121908245" x="415.2863697705803" y="529.6153846153846"/><desc class="value">0: 149</desc><desc class="x centered">416.96878092667566</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="180.60200668896323" rx="0" ry="0" width="3.364822312190711" x="418.6511920827711" y="349.0133779264214"/><desc class="value">8: 150</desc><desc class="x centered">420.33360323886643</desc><desc class="y centered">439.314381270903</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="422.0160143949618" y="529.6153846153846"/><desc class="value">0: 151</desc><desc class="x centered">423.6984255510572</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.364822312190711" x="425.3808367071526" y="461.88963210702343"/><desc class="value">3: 152</desc><desc class="x centered">427.06324786324797</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="428.7456590193433" y="529.6153846153846"/><desc class="value">0: 153</desc><desc class="x centered">430.4280701754387</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="432.11048133153406" y="507.04013377926424"/><desc class="value">1: 154</desc><desc class="x centered">433.7928924876294</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="435.47530364372477" y="529.6153846153846"/><desc class="value">0: 155</desc><desc class="x centered">437.15771479982016</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="438.84012595591554" y="529.6153846153846"/><desc class="value">0: 156</desc><desc class="x centered">440.5225371120109</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="442.20494826810625" y="529.6153846153846"/><desc class="value">0: 157</desc><desc class="x centered">443.88735942420163</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="445.569770580297" y="507.04013377926424"/><desc class="value">1: 158</desc><desc class="x centered">447.25218173639234</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="448.93459289248773" y="529.6153846153846"/><desc class="value">0: 159</desc><desc class="x centered">450.6170040485831</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="135.45150501672242" rx="0" ry="0" width="3.3648223121907677" x="452.29941520467844" y="394.1638795986622"/><desc class="value">6: 160</desc><desc class="x centered">453.9818263607738</desc><desc class="y centered">461.88963210702343</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="455.6642375168692" y="529.6153846153846"/><desc class="value">0: 161</desc><desc class="x centered">457.34664867296453</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="459.0290598290599" y="529.6153846153846"/><desc class="value">0: 162</desc><desc class="x centered">460.7114709851553</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="462.3938821412507" y="529.6153846153846"/><desc class="value">0: 163</desc><desc class="x centered">464.07629329734607</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="465.7587044534414" y="507.04013377926424"/><desc class="value">1: 164</desc><desc class="x centered">467.4411156095367</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121907677" x="469.1235267656321" y="507.04013377926424"/><desc class="value">1: 165</desc><desc class="x centered">470.8059379217275</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="472.4883490778229" y="529.6153846153846"/><desc class="value">0: 166</desc><desc class="x centered">474.17076023391826</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="475.8531713900136" y="529.6153846153846"/><desc class="value">0: 167</desc><desc class="x centered">477.53558254610897</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="479.21799370220435" y="529.6153846153846"/><desc class="value">0: 168</desc><desc class="x centered">480.9004048582997</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="482.58281601439506" y="529.6153846153846"/><desc class="value">0: 169</desc><desc class="x centered">484.26522717049045</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="67.72575250836121" rx="0" ry="0" width="3.3648223121907677" x="485.9476383265858" y="461.88963210702343"/><desc class="value">3: 170</desc><desc class="x centered">487.63004948268116</desc><desc class="y centered">495.75250836120404</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="489.31246063877654" y="529.6153846153846"/><desc class="value">0: 171</desc><desc class="x centered">490.99487179487187</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="492.67728295096725" y="529.6153846153846"/><desc class="value">0: 172</desc><desc class="x centered">494.35969410706264</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="496.042105263158" y="529.6153846153846"/><desc class="value">0: 173</desc><desc class="x centered">497.7245164192534</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190711" x="499.40692757534873" y="507.04013377926424"/><desc class="value">1: 174</desc><desc class="x centered">501.08933873144406</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="502.77174988753944" y="529.6153846153846"/><desc class="value">0: 175</desc><desc class="x centered">504.4541610436348</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190711" x="506.1365721997302" y="529.6153846153846"/><desc class="value">0: 176</desc><desc class="x centered">507.8189833558256</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="509.5013945119209" y="529.6153846153846"/><desc class="value">0: 177</desc><desc class="x centered">511.1838056680163</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190654" x="512.8662168241117" y="507.04013377926424"/><desc class="value">1: 178</desc><desc class="x centered">514.548627980207</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="516.2310391363023" y="529.6153846153846"/><desc class="value">0: 179</desc><desc class="x centered">517.9134502923978</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="45.15050167224081" rx="0" ry="0" width="3.3648223121907677" x="519.5958614484931" y="484.46488294314383"/><desc class="value">2: 180</desc><desc class="x centered">521.2782726045884</desc><desc class="y centered">507.04013377926424</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="522.9606837606839" y="529.6153846153846"/><desc class="value">0: 181</desc><desc class="x centered">524.6430949167792</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121908813" x="526.3255060728745" y="529.6153846153846"/><desc class="value">0: 182</desc><desc class="x centered">528.00791722897</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="529.6903283850654" y="529.6153846153846"/><desc class="value">0: 183</desc><desc class="x centered">531.3727395411607</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="533.0551506972561" y="529.6153846153846"/><desc class="value">0: 184</desc><desc class="x centered">534.7375618533514</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121908813" x="536.4199730094467" y="529.6153846153846"/><desc class="value">0: 185</desc><desc class="x centered">538.1023841655422</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="539.7847953216376" y="529.6153846153846"/><desc class="value">0: 186</desc><desc class="x centered">541.4672064777329</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="543.1496176338283" y="529.6153846153846"/><desc class="value">0: 187</desc><desc class="x centered">544.8320287899237</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="546.514439946019" y="529.6153846153846"/><desc class="value">0: 188</desc><desc class="x centered">548.1968511021144</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="549.8792622582098" y="529.6153846153846"/><desc class="value">0: 189</desc><desc class="x centered">551.5616734143051</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="553.2440845704004" y="529.6153846153846"/><desc class="value">0: 190</desc><desc class="x centered">554.9264957264959</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="556.6089068825912" y="529.6153846153846"/><desc class="value">0: 191</desc><desc class="x centered">558.2913180386865</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.3648223121908813" x="559.9737291947819" y="507.04013377926424"/><desc class="value">1: 192</desc><desc class="x centered">561.6561403508773</desc><desc class="y centered">518.3277591973244</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="563.3385515069727" y="529.6153846153846"/><desc class="value">0: 193</desc><desc class="x centered">565.0209626630681</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="566.7033738191634" y="529.6153846153846"/><desc class="value">0: 194</desc><desc class="x centered">568.3857849752587</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121908813" x="570.0681961313541" y="529.6153846153846"/><desc class="value">0: 195</desc><desc class="x centered">571.7506072874495</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.364822312190654" x="573.4330184435449" y="529.6153846153846"/><desc class="value">0: 196</desc><desc class="x centered">575.1154295996403</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="576.7978407557356" y="529.6153846153846"/><desc class="value">0: 197</desc><desc class="x centered">578.4802519118309</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="0.0" rx="0" ry="0" width="3.3648223121907677" x="580.1626630679264" y="529.6153846153846"/><desc class="value">0: 198</desc><desc class="x centered">581.8450742240218</desc><desc class="y centered">529.6153846153846</desc></g><g class="histbar"><rect class="rect reactive tooltip-trigger" height="22.575250836120404" rx="0" ry="0" width="3.364822312190654" x="583.5274853801171" y="507.04013377926424"/><desc class="value">1: 199</desc><desc class="x centered">585.2098965362125</desc><desc class="y centered">518.3277591973244</desc></g></g></g></g><g class="titles"/><g class="plot overlay" transform="translate(181, 20)"><g class="series serie-0 color-0"/><g class="series serie-1 color-1"/><g class="series serie-2 color-2"/><g class="series serie-3 color-3"/></g><g class="plot text-overlay" transform="translate(181, 20)"><g class="series serie-0 color-0"/><g class="series serie-1 color-1"/><g class="series serie-2 color-2"/><g class="series serie-3 color-3"/></g><g class="plot tooltip-overlay" transform="translate(181, 20)"><g class="tooltip" style="opacity: 0" transform="translate(0 0)"><rect class="tooltip-box" height="0" rx="0" ry="0" width="0"/><g class="text"/></g></g><g class="legends" transform="translate(10, 30)"><g class="legend reactive activate-serie" id="activate-serie-0"><rect class="color-0 reactive" height="12" width="12" x="0.0" y="1.0"/><text x="17.0" y="11.2">No Disease age</text></g><g class="legend reactive activate-serie" id="activate-serie-1"><rect class="color-1 reactive" height="12" width="12" x="0.0" y="22.0"/><text x="17.0" y="32.2">Disease age</text></g><g class="legend reactive activate-serie" id="activate-serie-2"><rect class="color-2 reactive" height="12" width="12" x="0.0" y="43.0"/><text x="17.0" y="53.2">No Disease </text></g><g class="legend reactive activate-serie" id="activate-serie-3"><rect class="color-3 reactive" height="12" width="12" x="0.0" y="64.0"/><text x="17.0" y="74.2">Disease</text></g></g><g class="legends" transform="translate(790, 30)"/></g></svg>'
    </figure>
  </body>
</html>




## Bar Plot / Box Plot / Pair Plot
Lets first take a look at the average age of people who have heart disease vs who does not. And we can observe that people who are slightly older have more chance of having heart disease. (only from this data set.)


```python
# average age of people with / out heart dieases
# plt.figure(figsize=(8,8))
sns.barplot(x='num', y='age', data=df)
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_41_0.png)


Again, when we create a box plot related to the average of people who have / doesn’t have heart disease we can observe the younger people are less likely to have heart disease.


```python
# box plot 
# plt.figure(figsize=(8,8))
sns.boxplot(x="num", y='age', data=df)
plt.show()
```


![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_43_0.png)


And finally, I wanted to show the pair plot against few of the attributes such as age, thal, ca 
(chest pain type), thalach ( maximum heart rate achieved) and presence of heart disease. And as seen in the correlation matrix we can observe a strong negative correlation between age and thalach.


```python
# show pair plot
plt.figure(figsize=(14,14))
sns.pairplot(df[['age','thal','ca','thalach','num']],hue='num')
plt.show()
```


    <Figure size 1008x1008 with 0 Axes>



![png](02%20-%20Basic%20Medical%20Data%20Visualization_files/02%20-%20Basic%20Medical%20Data%20Visualization_45_1.png)


## Uniform Manifold Approximation and Projection embedding (UMAP) t-distributed Stochastic Neighbor Embedding (t-SNE)

Run the following command from the terminal.

```bash
python Manifold_Approximation_and_Projection.py
```
