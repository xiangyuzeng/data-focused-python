---
layout: default
title: 02 - NumPy Data analysis
parent: Week 05 - Data Processing and Visualization Part 2
grand_parent: Lectures
nav_order: 3
---

# NumPy Tutorial: Data analysis with Python
[Source](https://www.dataquest.io/blog/numpy-tutorial-python/)

NumPy is a commonly used Python data analysis package. By using NumPy, you can speed up your workflow, and interface with other packages in the Python ecosystem, like scikit-learn, that use NumPy under the hood. NumPy was originally developed in the mid 2000s, and arose from an even older package called Numeric. This longevity means that almost every data analysis or machine learning package for Python leverages NumPy in some way.

In this tutorial, we'll walk through using NumPy to analyze data on wine quality. The data contains information on various attributes of wines, such as pH and fixed acidity, along with a quality score between 0 and 10 for each wine. The quality score is the average of at least 3 human taste testers. As we learn how to work with NumPy, we'll try to figure out more about the perceived quality of wine.

The wines we'll be analyzing are from the Minho region of Portugal.

The data was downloaded from the UCI Machine Learning Repository, and is available [here](https://archive.ics.uci.edu/ml/datasets/Wine+Quality). Here are the first few rows of the winequality-red.csv file, which we'll be using throughout this tutorial:

``` text
"fixed acidity";"volatile acidity";"citric acid";"residual sugar";"chlorides";"free sulfur dioxide";"total sulfur dioxide";"density";"pH";"sulphates";"alcohol";"quality"
7.4;0.7;0;1.9;0.076;11;34;0.9978;3.51;0.56;9.4;5
7.8;0.88;0;2.6;0.098;25;67;0.9968;3.2;0.68;9.8;5
```

The data is in what I'm going to call ssv (semicolon separated values) format -- each record is separated by a semicolon (;), and rows are separated by a new line. There are 1600 rows in the file, including a header row, and 12 columns.

Before we get started, a quick version note -- we'll be using Python 3.5. Our code examples will be done using Jupyter notebook.

If you want to jump right into a specific area, here are the topics:
* Creating an Array
* Reading Text Files
* Array Indexing
* N-Dimensional Arrays
* Data Types
* Array Math
* Array Methods
* Array Comparison and Filtering
* Reshaping and Combining Arrays

Lists Of Lists for CSV Data
Before using NumPy, we'll first try to work with the data using Python and the csv package. We can read in the file using the csv.reader object, which will allow us to read in and split up all the content from the ssv file.

In the below code, we:

* Import the csv library.
* Open the winequality-red.csv file.
    * With the file open, create a new csv.reader object.
        * Pass in the keyword argument delimiter=";" to make sure that the records are split up on the semicolon character instead of the default comma character.
    * Call the list type to get all the rows from the file.
    * Assign the result to wines.


```python
import csv

with open("winequality-red.csv", 'r') as f:
    wines = list(csv.reader(f, delimiter=";"))
#     print(wines[:3])
    
headers = wines[0]
wines_only = wines[1:]
```


```python
# print the headers
print(headers)
```

    ['fixed acidity', 'volatile acidity', 'citric acid', 'residual sugar', 'chlorides', 'free sulfur dioxide', 'total sulfur dioxide', 'density', 'pH', 'sulphates', 'alcohol', 'quality']



```python
# print the 1st row of data
print(wines_only[0])
```

    ['7.4', '0.7', '0', '1.9', '0.076', '11', '34', '0.9978', '3.51', '0.56', '9.4', '5']



```python
# print the 1st three rows of data
print(wines_only[:3])
```

    [['7.4', '0.7', '0', '1.9', '0.076', '11', '34', '0.9978', '3.51', '0.56', '9.4', '5'], ['7.8', '0.88', '0', '2.6', '0.098', '25', '67', '0.9968', '3.2', '0.68', '9.8', '5'], ['7.8', '0.76', '0.04', '2.3', '0.092', '15', '54', '0.997', '3.26', '0.65', '9.8', '5']]


The data has been read into a list of lists. Each inner list is a row from the ssv file. As you may have noticed, each item in the entire list of lists is represented as a string, which will make it harder to do computations.

As you can see from the table above, we've read in three rows, the first of which contains column headers. Each row after the header row represents a wine. The first element of each row is the fixed acidity, the second is the volatile acidity, and so on. 

## Calculate Average Wine Quality

We can find the average quality of the wines. The below code will:

* Extract the last element from each row after the header row.
* Convert each extracted element to a float.
* Assign all the extracted elements to the list qualities.
* Divide the sum of all the elements in qualities by the total number of elements in qualities to the get the mean.


```python
# calculate average wine quality with a loop
qualities = []
for row in wines[1:]:
    qualities.append(float(row[-1]))

sum(qualities) / len(wines[1:])
```




    5.6360225140712945




```python
# calculate average wine quality with a list comprehension
qualities = [float(row[-1]) for row in wines[1:]]

sum(qualities) / len(wines[1:])
```




    5.6360225140712945



Although we were able to do the calculation we wanted, the code is fairly complex, and it won't be fun to have to do something similar every time we want to compute a quantity. Luckily, we can use NumPy to make it easier to work with our data.

# Numpy 2-Dimensional Arrays

With NumPy, we work with multidimensional arrays. We'll dive into all of the possible types of multidimensional arrays later on, but for now, we'll focus on 2-dimensional arrays. A 2-dimensional array is also known as a matrix, and is something you should be familiar with. In fact, it's just a different way of thinking about a list of lists. A matrix has rows and columns. By specifying a row number and a column number, we're able to extract an element from a matrix.

If we picked the element at the first row and the second column, we'd get volatile acidity. If we picked the element in the third row and the second column, we'd get 0.88.

In a NumPy array, the number of dimensions is called the **rank**, and each dimension is called an **axis**. So 
* the rows are the first axis
* the columns are the second axis

Now that you understand the basics of matrices, let's see how we can get from our list of lists to a NumPy array.

## Creating A NumPy Array

We can create a NumPy array using the numpy.array function. If we pass in a list of lists, it will automatically create a NumPy array with the same number of rows and columns. Because we want all of the elements in the array to be float elements for easy computation, we'll leave off the header row, which contains strings. One of the limitations of NumPy is that all the elements in an array have to be of the same type, so if we include the header row, all the elements in the array will be read in as strings. Because we want to be able to do computations like find the average quality of the wines, we need the elements to all be floats.

In the below code, we:

* Import the ```numpy``` package.
* Pass the ```list``` of lists wines into the array function, which converts it into a NumPy array.
    * Exclude the header row with list slicing.
    * Specify the keyword argument ```dtype``` to make sure each element is converted to a ```float```. We'll dive more into what the ```dtype``` is later on.


```python
import numpy as np
np.set_printoptions(precision=2) # set the output print precision for readability

# create the numpy array skipping the headers
wines = np.array(wines[1:], dtype=np.float)
```


```python
# If we display wines, we'll now get a NumPy array:
print(type(wines), wines)
```

    <class 'numpy.ndarray'> [[ 7.4   0.7   0.   ...  0.56  9.4   5.  ]
     [ 7.8   0.88  0.   ...  0.68  9.8   5.  ]
     [ 7.8   0.76  0.04 ...  0.65  9.8   5.  ]
     ...
     [ 6.3   0.51  0.13 ...  0.75 11.    6.  ]
     [ 5.9   0.65  0.12 ...  0.71 10.2   5.  ]
     [ 6.    0.31  0.47 ...  0.66 11.    6.  ]]



```python
# We can check the number of rows and columns in our data using the shape property of NumPy arrays:
wines.shape
```




    (1599, 12)



## Alternative NumPy Array Creation Methods

There are a variety of methods that you can use to create NumPy arrays. It's useful to create an array with all zero elements in cases when you need an array of fixed size, but don't have any values for it yet. To start with, you can create an array where every element is zero. The below code will create an array with 3 rows and 4 columns, where every element is 0, using ```numpy.zeros```:


```python
empty_array = np.zeros((3, 4))
empty_array
```




    array([[0., 0., 0., 0.],
           [0., 0., 0., 0.],
           [0., 0., 0., 0.]])



Creating arrays full of random numbers can be useful when you want to quickly test your code with sample arrays. You can also create an array where each element is a random number using ```numpy.random.rand```.


```python
np.random.rand(2, 3)
```




    array([[0.86, 0.94, 0.87],
           [0.85, 0.5 , 0.95]])



### Using NumPy To Read In Files
It's possible to use NumPy to directly read ```csv``` or other files into arrays. We can do this using the ```numpy.genfromtxt``` function. We can use it to read in our initial data on red wines.

In the below code, we:

* Use the ``` genfromtxt ``` function to read in the ``` winequality-red.csv ``` file.
* Specify the keyword argument ``` delimiter=";" ``` so that the fields are parsed properly.
* Specify the keyword argument ``` skip_header=1 ``` so that the header row is skipped.


```python
wines = np.genfromtxt("winequality-red.csv", delimiter=";", skip_header=1)
wines
```




    array([[ 7.4 ,  0.7 ,  0.  , ...,  0.56,  9.4 ,  5.  ],
           [ 7.8 ,  0.88,  0.  , ...,  0.68,  9.8 ,  5.  ],
           [ 7.8 ,  0.76,  0.04, ...,  0.65,  9.8 ,  5.  ],
           ...,
           [ 6.3 ,  0.51,  0.13, ...,  0.75, 11.  ,  6.  ],
           [ 5.9 ,  0.65,  0.12, ...,  0.71, 10.2 ,  5.  ],
           [ 6.  ,  0.31,  0.47, ...,  0.66, 11.  ,  6.  ]])



Wines will end up looking the same as if we read it into a list then converted it to an array of ```floats```. NumPy will automatically pick a data type for the elements in an array based on their format.

## Indexing NumPy Arrays

We now know how to create arrays, but unless we can retrieve results from them, there isn't a lot we can do with NumPy. We can use array indexing to select individual elements, groups of elements, or entire rows and columns. 

One important thing to keep in mind is that just like Python lists, NumPy is **zero-indexed**, meaning that:

* The index of the first row is 0
* The index of the first column is 0 
* If we want to work with the fourth row, we'd use index 3
* If we want to work with the second row, we'd use index 1, and so on. 

We'll again work with the wines array:

|||||||||||||
|-:|-:|-:|-:|-:|-:|-:|-:|-:|-:|-:|-:|
|7.4 |0.70	|0.00	|1.9	|0.076	|11	|34	|0.9978	|3.51	|0.56	|9.4	|5|
|7.8 |0.88	|0.00	|2.6	|0.098	|25	|67	|0.9968	|3.20	|0.68	|9.8	|5|
|7.8 |0.76	|0.04	|2.3	|0.092	|15	|54	|0.9970	|3.26	|0.65	|9.8	|5|
|11.2|0.28	|0.56	|1.9	|0.075	|17	|60	|0.9980	|3.16	|0.58	|9.8	|6|
|7.4 |0.70	|0.00	|1.9	|0.076	|11	|34	|0.9978	|3.51	|0.56	|9.4	|5|

Let's select the element at **row 3** and **column 4**.

We pass:
* 2 as the row index
* 3 as the column index. 

This retrieves the value from the **third row** and **fourth column**


```python
wines[2, 3]
```




    2.3




```python
wines[2][3]
```




    2.3



Since we're working with a 2-dimensional array in NumPy we specify 2 indexes to retrieve an element. 

* The first index is the row, or **axis 1**, index
* The second index is the column, or **axis 2**, index 

Any element in wines can be retrieved using 2 indexes.


```python
# rows 1, 2, 3 and column 4
wines[0:3, 3]
```




    array([1.9, 2.6, 2.3])




```python
# all rows and column 3
wines[:, 2]
```




    array([0.  , 0.  , 0.04, ..., 0.13, 0.12, 0.47])



Just like with ```list``` slicing, it's possible to omit the 0 to just retrieve all the elements from the beginning up to element 3:


```python
# rows 1, 2, 3 and column 4
wines[:3, 3]
```




    array([1.9, 2.6, 2.3])



We can select an entire column by specifying that we want all the elements, from the first to the last. We specify this by just using the colon ```:```, with no starting or ending indices. The below code will select the entire fourth column:


```python
# all rows and column 4
wines[:, 3]
```




    array([1.9, 2.6, 2.3, ..., 2.3, 2. , 3.6])



We selected an entire column above, but we can also extract an entire row:


```python
# row 4 and all columns
wines[3, :]
```




    array([11.2 ,  0.28,  0.56,  1.9 ,  0.07, 17.  , 60.  ,  1.  ,  3.16,
            0.58,  9.8 ,  6.  ])



If we take our indexing to the extreme, we can select the entire array using two colons to select all the rows and columns in wines. This is a great party trick, but doesn't have a lot of good applications:


```python
wines[:, :]
```




    array([[ 7.40,  0.70,  0.00, ...,  0.56,  9.40,  5.00],
           [ 7.80,  0.88,  0.00, ...,  0.68,  9.80,  5.00],
           [ 7.80,  0.76,  0.04, ...,  0.65,  9.80,  5.00],
           ...,
           [ 6.30,  0.51,  0.13, ...,  0.75, 11.00,  6.00],
           [ 5.90,  0.65,  0.12, ...,  0.71, 10.20,  5.00],
           [ 6.00,  0.31,  0.47, ...,  0.66, 11.00,  6.00]])



## Assigning Values To NumPy Arrays
We can also use indexing to assign values to certain elements in arrays. We can do this by assigning directly to the indexed value:


```python
# assign the value of 10 to the 2nd row and 6th column
print('Before', wines[1, 4:7])
wines[1, 5] = 10
print('After', wines[1, 4:7])
```

    Before [ 0.1 25.  67. ]
    After [ 0.1 10.  67. ]


We can do the same for slices. To overwrite an entire column, we can do this:


```python
# Overwrites all the values in the eleventh column with 50.
print('Before', wines[:, 9:12])
wines[:, 10] = 50
print('After', wines[:, 9:12])
```

    Before [[ 0.56  9.4   5.  ]
     [ 0.68  9.8   5.  ]
     [ 0.65  9.8   5.  ]
     ...
     [ 0.75 11.    6.  ]
     [ 0.71 10.2   5.  ]
     [ 0.66 11.    6.  ]]
    After [[ 0.56 50.    5.  ]
     [ 0.68 50.    5.  ]
     [ 0.65 50.    5.  ]
     ...
     [ 0.75 50.    6.  ]
     [ 0.71 50.    5.  ]
     [ 0.66 50.    6.  ]]


## 1-Dimensional NumPy Arrays

So far, we've worked with 2-dimensional arrays, such as wines. However, NumPy is a package for working with multidimensional arrays. 

One of the most common types of multidimensional arrays is the **1-dimensional array**, or **vector**. As you may have noticed above, when we sliced wines, we retrieved a 1-dimensional array. 

* A 1-dimensional array only needs a single index to retrieve an element. 
* Each row and column in a 2-dimensional array is a 1-dimensional array. 

Just like a list of lists is analogous to a 2-dimensional array, a single list is analogous to a 1-dimensional array. 

If we slice wines and only retrieve the third row, we get a 1-dimensional array:


```python
third_wine = wines[3,:]
third_wine
```




    array([11.2 ,  0.28,  0.56,  1.9 ,  0.07, 17.  , 60.  ,  1.  ,  3.16,
            0.58, 50.  ,  6.  ])



We can retrieve individual elements from ```third_wine``` using a single index. 


```python
# display the second item in third_wine
third_wine[1]
```




    0.28



Most NumPy functions that we've worked with, such as ```numpy.random.rand```, can be used with multidimensional arrays. Here's how we'd use ```numpy.random.rand``` to generate a random vector:


```python
np.random.rand(3)
```




    array([0.94, 0.41, 0.96])



Previously, when we called ```np.random.rand```, we passed in a shape for a 2-dimensional array, so the result was a 2-dimensional array. This time, we passed in a shape for a single dimensional array. The shape specifies the number of dimensions, and the size of the array in each dimension. 

A shape of ```(10,10)``` will be a 2-dimensional array with **10 rows** and **10 columns**. A shape of ```(10,)``` will be a **1-dimensional** array with **10 elements**.

Where NumPy gets more complex is when we start to deal with arrays that have more than 2 dimensions.

## N-Dimensional NumPy Arrays

This doesn't happen extremely often, but there are cases when you'll want to deal with arrays that have greater than 3 dimensions. One way to think of this is as a list of lists of lists. Let's say we want to store the monthly earnings of a store, but we want to be able to quickly lookup the results for a quarter, and for a year. The earnings for one year might look like this:

``` python
[500, 505, 490, 810, 450, 678, 234, 897, 430, 560, 1023, 640]
```

The store earned \$500 in January, \$505 in February, and so on. We can split up these earnings by quarter into a list of lists:


```python
year_one = [
    [500,505,490], # 1st quarter
    [810,450,678], # 2nd quarter
    [234,897,430], # 3rd quarter
    [560,1023,640] # 4th quarter
]
```

We can retrieve the earnings from January by calling ``` year_one[0][0] ```. If we want the results for a whole quarter, we can call ``` year_one[0] ``` or ``` year_one[1] ```. 

We now have a 2-dimensional array, or matrix. But what if we now want to add the results from another year? We have to add a third dimension:


```python
earnings = [
            [ # year 1
                [500,505,490], # year 1, 1st quarter
                [810,450,678], # year 1, 2nd quarter
                [234,897,430], # year 1, 3rd quarter
                [560,1023,640] # year 1, 4th quarter
            ],
            [ # year =2
                [600,605,490], # year 2, 1st quarter
                [345,900,1000],# year 2, 2nd quarter
                [780,730,710], # year 2, 3rd quarter
                [670,540,324]  # year 2, 4th quarter
            ]
          ]
```

We can retrieve the earnings from January of the first year by calling ``` earnings[0][0][0] ```. 

We now need three indexes to retrieve a single element. A three-dimensional array in NumPy is much the same. In fact, we can convert earnings to an array and then get the earnings for January of the first year:


```python
earnings = np.array(earnings)
```


```python
# year 1, 1st quarter, 1st month (January)
earnings[0,0,0] 
```




    500




```python
# year 2, 3rd quarter, 1st month (July)
earnings[1,2,0] 
```




    780




```python
# we can also find the shape of the array
earnings.shape
```




    (2, 4, 3)



Indexing and slicing work the exact same way with a 3-dimensional array, but now we have an extra axis to pass in. If we wanted to get the earnings for **January of all years**, we could do this:


```python
# all years, 1st quarter, 1st month (January)
earnings[:,0,0]
```




    array([500, 600])



If we wanted to get first quarter earnings from both years, we could do this:


```python
# all years, 1st quarter, all months (January, February, March)
earnings[:,0,:]
```




    array([[500, 505, 490],
           [600, 605, 490]])



Adding more dimensions can make it much easier to query your data if it's organized in a certain way. As we go from 3-dimensional arrays to 4-dimensional and larger arrays, the same properties apply, and they can be indexed and sliced in the same ways.

## NumPy Data Types

As we mentioned earlier, each NumPy array can store elements of a single data type. For example, wines contains only float values. 

NumPy stores values using its own data types, **which are distinct from Python types** like ```float``` and ```str```. 

This is because the core of NumPy is written in a programming language called ```C```, **which stores data differently than the Python data types**. NumPy data types map between Python and C, allowing us to use NumPy arrays without any conversion hitches.

You can find the data type of a NumPy array by accessing the dtype property:


```python
wines.dtype
```




    dtype('float64')



NumPy has several different data types, which mostly map to Python data types, like ```float```, and ```str```. You can find a full listing of NumPy data types [here](https://www.dataquest.io/blog/numpy-tutorial-python/), but here are a few important ones:

* ```float``` -- numeric floating point data.
* ```int``` -- integer data.
* ```string``` -- character data.
* ```object``` -- Python objects.

Data types additionally end with a suffix that indicates how many bits of memory they take up. So ```int32``` is a **32 bit integer data type**, and ```float64``` is a **64 bit float data type**.

### Converting Data Types

You can use the numpy.ndarray.astype method to convert an array to a different type. The method will actually **copy the array**, and **return a new array with the specified data type**. 

For instance, we can convert wines to the ```int``` data type:


```python
# convert wines to the int data type
wines.astype(int)
```




    array([[ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           ...,
           [ 6,  0,  0, ...,  0, 50,  6],
           [ 5,  0,  0, ...,  0, 50,  5],
           [ 6,  0,  0, ...,  0, 50,  6]])



As you can see above, all of the items in the resulting array are integers. Note that we used the Python ```int``` type instead of a NumPy data type when converting wines. This is because several Python data types, including ```float```, ```int```, and ```string```, can be used with NumPy, and are automatically converted to NumPy data types.

We can check the name property of the ```dtype``` of the resulting array to see what data type NumPy mapped the resulting array to:


```python
# convert to int
int_wines = wines.astype(int)

# check the data type
int_wines.dtype.name
```




    'int64'



The array has been converted to a **64-bit integer** data type. This allows for very long integer values, **but takes up more space in memory** than storing the values as 32-bit integers.

If you want more control over how the array is stored in memory, you can directly create NumPy dtype objects like ```numpy.int32```


```python
np.int32
```




    numpy.int32



You can use these directly to convert between types:


```python
# convert to a 64-bit integer
wines.astype(np.int64)
```




    array([[ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           ...,
           [ 6,  0,  0, ...,  0, 50,  6],
           [ 5,  0,  0, ...,  0, 50,  5],
           [ 6,  0,  0, ...,  0, 50,  6]])




```python
# convert to a 32-bit integer
wines.astype(np.int32)
```




    array([[ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           ...,
           [ 6,  0,  0, ...,  0, 50,  6],
           [ 5,  0,  0, ...,  0, 50,  5],
           [ 6,  0,  0, ...,  0, 50,  6]], dtype=int32)




```python
# convert to a 16-bit integer
wines.astype(np.int16)
```




    array([[ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           ...,
           [ 6,  0,  0, ...,  0, 50,  6],
           [ 5,  0,  0, ...,  0, 50,  5],
           [ 6,  0,  0, ...,  0, 50,  6]], dtype=int16)




```python
# convert to a 8-bit integer
wines.astype(np.int8)
```




    array([[ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           [ 7,  0,  0, ...,  0, 50,  5],
           ...,
           [ 6,  0,  0, ...,  0, 50,  6],
           [ 5,  0,  0, ...,  0, 50,  5],
           [ 6,  0,  0, ...,  0, 50,  6]], dtype=int8)



## NumPy Array Operations

NumPy makes it simple to perform mathematical operations on arrays. This is one of the primary advantages of NumPy, and makes it quite easy to do computations.

### Single Array Math
If you do any of the basic mathematical operations ```/```, ```*```, ```-```, ```+```, ```^``` with an array and a value, it will apply the operation to each of the elements in the array.

Let's say we want to add 10 points to each quality score because we're feeling generous. Here's how we'd do that:


```python
# add 10 points to the quality score
wines[:,-1] + 10
```




    array([15., 15., 15., ..., 16., 15., 16.])



*Note: that the above operation won't change the wines array -- it will return a new 1-dimensional array where 10 has been added to each element in the quality column of wines.*

If we instead did ```+=```, we'd modify the array in place:


```python
print('Before', wines[:,11])

# modify the data in place
wines[:,11] += 10

print('After', wines[:,11])
```

    Before [5. 5. 5. ... 6. 5. 6.]
    After [15. 15. 15. ... 16. 15. 16.]


All the other operations work the same way. For example, if we want to multiply each of the quality score by 2, we could do it like this:


```python
# multiply the quality score by 2
wines[:,11] * 2
```




    array([30., 30., 30., ..., 32., 30., 32.])



### Multiple Array Math

It's also possible to do mathematical operations between arrays. This will apply the operation to pairs of elements. For example, if we add the quality column to itself, here's what we get:


```python
# add the quality column to itself
wines[:,11] + wines[:,11]
```




    array([30., 30., 30., ..., 32., 30., 32.])



Note that this is equivalent to ```wines[:,11] * 2``` -- this is because NumPy adds each pair of elements. The first element in the first array is added to the first element in the second array, the second to the second, and so on.


```python
# add the quality column to itself
wines[:,11] * 2
```




    array([30., 30., 30., ..., 32., 30., 32.])



We can also use this to multiply arrays. Let's say we want to pick a wine that maximizes alcohol content and quality. We'd multiply alcohol by quality, and select the wine with the highest score:


```python
# multiply alcohol content by quality
alcohol_by_quality = wines[:,10] * wines[:,11]
print(alcohol_by_quality)
```

    [750. 750. 750. ... 800. 750. 800.]



```python
alcohol_by_quality.sort()
print(alcohol_by_quality, alcohol_by_quality[-1])
```

    [650. 650. 650. ... 900. 900. 900.] 900.0


All of the common operations ```/```, ```*```, ```-```, ```+```, ```^``` will work between arrays.

## NumPy Array Methods

In addition to the common mathematical operations, NumPy also has several methods that you can use for more complex calculations on arrays. An example of this is the ```numpy.ndarray.sum``` method. This finds the sum of all the elements in an array by default:


```python
# find the sum of all rows and the quality column
total = 0
for row in wines:
    total += row[11]
print(total)
```

    25002.0



```python
# find the sum of all rows and the quality column
wines[:,11].sum(axis=0)
```




    25002.0




```python
# find the sum of the rows 1, 2, and 3 across all columns
totals = []
for i in range(3):
    total = 0
    for col in wines[i,:]:
        total += col
    totals.append(total)
print(totals)
```

    [125.1438, 158.2548, 149.899]



```python
# find the sum of the rows 1, 2, and 3 across all columns
wines[0:3,:].sum(axis=1)
```




    array([125.14, 158.25, 149.9 ])



We can pass the ```axis``` keyword argument into the sum method to find sums over an axis. 

If we call sum across the wines matrix, and pass in ```axis=0```, we'll find the sums over the first axis of the array. This will give us the **sum of all the values in every column**. 

This may seem backwards that the sums over the first axis would give us the sum of each column, but one way to think about this is that **the specified axis is the one "going away"**. 

So if we specify ```axis=0```, we want the **rows to go away**, and we want to find **the sums for each of the remaining axes across each row**:


```python
# sum each column for all rows
totals = [0] * len(wines[0])
for i, total in enumerate(totals):
    for row_val in wines[:,i]:
        total += row_val
    totals[i] = total
print(totals)
```

    [13303.100000000046, 843.9850000000005, 433.2899999999982, 4059.550000000003, 139.8589999999996, 25369.0, 74302.0, 1593.7979399999986, 5294.470000000001, 1052.3800000000006, 79950.0, 25002.0]



```python
# sum each column for all rows
wines.sum(axis=0)
```




    array([13303.1 ,   843.99,   433.29,  4059.55,   139.86, 25369.  ,
           74302.  ,  1593.8 ,  5294.47,  1052.38, 79950.  , 25002.  ])



We can verify that we did the sum correctly by checking the shape. The shape should be 12, corresponding to the number of columns:


```python
wines.sum(axis=0).shape
```




    (12,)



If we pass in axis=1, we'll find the sums over the second axis of the array. This will give us the sum of each row:


```python
# sum each row for all columns
totals = [0] * len(wines)
for i, total in enumerate(totals):
    for col_val in wines[i,:]:
        total += col_val
    totals[i] = total
print(totals[0:3], '...', totals[-3:])
```

    [125.1438, 158.2548, 149.899] ... [149.48174, 155.01547, 141.49249]



```python
# sum each row for all columns
wines.sum(axis=1)
```




    array([125.14, 158.25, 149.9 , ..., 149.48, 155.02, 141.49])




```python
wines.sum(axis=1).shape
```




    (1599,)



There are several other methods that behave like the sum method, including:

* ```numpy.ndarray.mean``` — finds the mean of an array.
* ```numpy.ndarray.std``` — finds the standard deviation of an array.
* ```numpy.ndarray.min``` — finds the minimum value in an array.
* ```numpy.ndarray.max``` — finds the maximum value in an array.

You can find a full list of array methods [here](http://docs.scipy.org/doc/numpy/reference/arrays.ndarray.html).

## NumPy Array Comparisons

NumPy makes it possible to test to see if rows match certain values using mathematical comparison operations like ```<```, ```>```, ```>=```, ```<=```, and ```==```. For example, if we want to see which wines have a quality rating higher than 5, we can do this:


```python
# return True for all rows in the Quality column that are greater than 5
wines[:,11] > 5
```




    array([ True,  True,  True, ...,  True,  True,  True])



We get a Boolean array that tells us which of the wines have a quality rating greater than 5. We can do something similar with the other operators. For instance, we can see if any wines have a quality rating equal to 10:


```python
# return True for all rows that have a Quality rating of 10
wines[:,11] == 10
```




    array([False, False, False, ..., False, False, False])



### Subsetting

One of the powerful things we can do with a Boolean array and a NumPy array is select only certain rows or columns in the NumPy array. For example, the below code will only select rows in wines where the quality is over 7:


```python
# create a boolean array for wines with quality greater than 15
high_quality = wines[:,11] > 15
print(len(high_quality), high_quality)
```

    1599 [False False False ...  True False  True]



```python
# use boolean indexing to find high quality wines
high_quality_wines = wines[high_quality,:]
print(len(high_quality_wines), high_quality_wines)
```

    855 [[1.12e+01 2.80e-01 5.60e-01 ... 5.80e-01 5.00e+01 1.60e+01]
     [7.30e+00 6.50e-01 0.00e+00 ... 4.70e-01 5.00e+01 1.70e+01]
     [7.80e+00 5.80e-01 2.00e-02 ... 5.70e-01 5.00e+01 1.70e+01]
     ...
     [5.90e+00 5.50e-01 1.00e-01 ... 7.60e-01 5.00e+01 1.60e+01]
     [6.30e+00 5.10e-01 1.30e-01 ... 7.50e-01 5.00e+01 1.60e+01]
     [6.00e+00 3.10e-01 4.70e-01 ... 6.60e-01 5.00e+01 1.60e+01]]


We select only the rows where ```high_quality``` contains a ```True``` value, and all of the columns. This subsetting makes it simple to filter arrays for certain criteria. 

For example, we can look for wines with a lot of alcohol and high quality. In order to specify multiple conditions, we have to place each condition in **parentheses** ```(...)```, and separate conditions with an **ampersand** ```&```:


```python
# create a boolean array for high alcohol content and high quality
high_alcohol_and_quality = (wines[:,11] > 7) & (wines[:,10] > 10)
print(high_alcohol_and_quality)

# use boolean indexing to select out the wines
wines[high_alcohol_and_quality,:]
```

    [ True  True  True ...  True  True  True]





    array([[7.40e+00, 7.00e-01, 0.00e+00, ..., 5.60e-01, 5.00e+01, 1.50e+01],
           [7.80e+00, 8.80e-01, 0.00e+00, ..., 6.80e-01, 5.00e+01, 1.50e+01],
           [7.80e+00, 7.60e-01, 4.00e-02, ..., 6.50e-01, 5.00e+01, 1.50e+01],
           ...,
           [6.30e+00, 5.10e-01, 1.30e-01, ..., 7.50e-01, 5.00e+01, 1.60e+01],
           [5.90e+00, 6.45e-01, 1.20e-01, ..., 7.10e-01, 5.00e+01, 1.50e+01],
           [6.00e+00, 3.10e-01, 4.70e-01, ..., 6.60e-01, 5.00e+01, 1.60e+01]])



We can combine subsetting and assignment to overwrite certain values in an array:


```python
high_alcohol_and_quality = (wines[:,10] > 10) & (wines[:,11] > 7)
wines[high_alcohol_and_quality,10:] = 20
```

## Reshaping NumPy Arrays

We can change the shape of arrays while still preserving all of their elements. This often can make it easier to access array elements. The simplest reshaping is to flip the axes, so rows become columns, and vice versa. We can accomplish this with the ```numpy.transpose``` function:


```python
np.transpose(wines).shape
```




    (12, 1599)



We can use the ```numpy.ravel``` function to turn an array into a one-dimensional representation. It will essentially flatten an array into a long sequence of values:


```python
wines.ravel()
```




    array([ 7.4 ,  0.7 ,  0.  , ...,  0.66, 50.  , 16.  ])



Here's an example where we can see the ordering of ```numpy.ravel```:


```python
array_one = np.array(
    [
        [1, 2, 3, 4], 
        [5, 6, 7, 8]
    ]
)

array_one.ravel()
```




    array([1, 2, 3, 4, 5, 6, 7, 8])



Finally, we can use the numpy.reshape function to reshape an array to a certain shape we specify. The below code will turn the second row of wines into a 2-dimensional array with 2 rows and 6 columns:


```python
# print the current shape of the 2nd row and all columns
wines[1,:].shape
```




    (12,)




```python
# reshape the 2nd row to a 2 by 6 matrix
wines[1,:].reshape((2,6))
```




    array([[ 7.8 ,  0.88,  0.  ,  2.6 ,  0.1 , 10.  ],
           [67.  ,  1.  ,  3.2 ,  0.68, 50.  , 15.  ]])



## Combining NumPy Arrays

With NumPy, it's very common to combine multiple arrays into a single unified array. We can use ```numpy.vstack``` to vertically stack multiple arrays. 

Think of it like the second arrays's items being added as new rows to the first array. We can read in the ```winequality-white.csv``` dataset that contains information on the quality of white wines, then combine it with our existing dataset, wines, which contains information on red wines.

In the below code, we:

* Read in ```winequality-white.csv```.
* Display the shape of white_wines.


```python
white_wines = np.genfromtxt("winequality-white.csv", delimiter=";", skip_header=1)
white_wines.shape
```




    (4898, 12)



As you can see, we have attributes for 4898 wines. Now that we have the white wines data, we can combine all the wine data.

In the below code, we:

* Use the ```vstack``` function to combine wines and white_wines.
* Display the shape of the result.


```python
all_wines = np.vstack((wines, white_wines))
all_wines.shape
```




    (6497, 12)



As you can see, the result has 6497 rows, which is the sum of the number of rows in wines and the number of rows in red_wines.

If we want to combine arrays horizontally, where the number of rows stay constant, but the columns are joined, then we can use the ```numpy.hstack``` function. The arrays we combine need to have the same number of rows for this to work.

Finally, we can use ```numpy.concatenate``` as a general purpose version of ```hstack``` and ```vstack```. If we want to concatenate two arrays, we pass them into concatenate, then specify the axis keyword argument that we want to concatenate along. 

* Concatenating along the first axis is similar to ```vstack```
* Concatenating along the second axis is similar to ```hstack```:


```python
x = np.concatenate((wines, white_wines), axis=0)
print(x.shape, x)
```

    (6497, 12) [[7.40e+00 7.00e-01 0.00e+00 ... 5.60e-01 5.00e+01 1.50e+01]
     [7.80e+00 8.80e-01 0.00e+00 ... 6.80e-01 5.00e+01 1.50e+01]
     [7.80e+00 7.60e-01 4.00e-02 ... 6.50e-01 5.00e+01 1.50e+01]
     ...
     [6.50e+00 2.40e-01 1.90e-01 ... 4.60e-01 9.40e+00 6.00e+00]
     [5.50e+00 2.90e-01 3.00e-01 ... 3.80e-01 1.28e+01 7.00e+00]
     [6.00e+00 2.10e-01 3.80e-01 ... 3.20e-01 1.18e+01 6.00e+00]]


## Broadcasting

Unless the arrays that you're operating on are the exact same size, it's not possible to do elementwise operations. In cases like this, NumPy performs broadcasting to try to match up elements. Essentially, broadcasting involves a few steps:

* The last dimension of each array is compared.
    * If the dimension lengths are equal, or one of the dimensions is of length 1, then we keep going.
    * If the dimension lengths aren't equal, and none of the dimensions have length 1, then there's an error.
* Continue checking dimensions until the shortest array is out of dimensions.

For example, the following two shapes are compatible:

``` python
A: (50,3)
B  (3,)
```

This is because the length of the trailing dimension of array A is 3, and the length of the trailing dimension of array B is 3. They're equal, so that dimension is okay. Array B is then out of elements, so we're okay, and the arrays are compatible for mathematical operations.

The following two shapes are also compatible:

``` python
A: (1,2)
B  (50,2)
```

The last dimension matches, and A is of length 1 in the first dimension.

These two arrays don't match:

``` python
A: (50,50)
B: (49,49)
```

The lengths of the dimensions aren't equal, and neither array has either dimension length equal to 1.

There's a detailed explanation of broadcasting [here](http://docs.scipy.org/doc/numpy/user/basics.broadcasting.html), but we'll go through a few examples to illustrate the principle:



```python
wines * np.array([1,2])
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-98-821086ccaf65> in <module>()
    ----> 1 wines * np.array([1,2])
    

    ValueError: operands could not be broadcast together with shapes (1599,12) (2,) 


The above example didn't work because the two arrays don't have a matching trailing dimension. Here's an example where the last dimension does match:


```python
array_one = np.array(
    [
        [1,2],
        [3,4]
    ]
)
array_two = np.array([4,5])

array_one + array_two
```




    array([[5, 7],
           [7, 9]])



As you can see, array_two has been broadcasted across each row of array_one. Here's an example with our wines data:


```python
rand_array = np.random.rand(12)
wines + rand_array
```




    array([[ 8.11,  1.46,  0.23, ...,  1.28, 50.29, 15.88],
           [ 8.51,  1.64,  0.23, ...,  1.4 , 50.29, 15.88],
           [ 8.51,  1.52,  0.27, ...,  1.37, 50.29, 15.88],
           ...,
           [ 7.01,  1.27,  0.36, ...,  1.47, 50.29, 16.88],
           [ 6.61,  1.4 ,  0.35, ...,  1.43, 50.29, 15.88],
           [ 6.71,  1.07,  0.7 , ...,  1.38, 50.29, 16.88]])


