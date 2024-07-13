![image](https://github.com/user-attachments/assets/bd73bccf-42a3-42de-a21c-306e4ba51189)### NumPy: The Absolute Basics for Beginners

**Welcome to the absolute beginner’s guide to NumPy!**

**NumPy (Numerical Python)** is an open-source Python library extensively used in scientific and engineering domains. It provides multidimensional array data structures, such as the homogeneous, N-dimensional `ndarray`, and a comprehensive library of functions to operate efficiently on these structures. 

For more information about NumPy, visit [What is NumPy](https://numpy.org/). If you have comments or suggestions, please reach out!

---
## Installation of NumPy

`To install NumPy`, you can use pip, a package installer for Python. Open your command prompt or terminal and run the following command:

```
pip install numpy
```
This will download and install the latest version of NumPy from PyPI. If you're using a different version of Python, replace pip with pip3. 

For example, if you're using Python 3, you would use:
```
pip3 install numpy
```
If you're using a Linux system and encounter issues with package location, you might need to specify the Python version in the command. For Python 3, this would look like:
```
sudo apt-get install python3-numpy
```
Remember to ensure that Python and pip are updated to their latest versions before installing NumPy.

You can also use the `brew`commmand, for eaxmple:
```
brew install numpy
```

`To check if NumPy is installed`, you can run a Python script with the following command:

```python

import numpy
print(numpy.__version__)
````
If the script runs without raising any errors and prints the version number, then NumPy is installed. If the script raises a ModuleNotFoundError, it means that NumPy is not installed or not accessible in your Python environment. In this case, you can follow the previous instructions to install NumPy.

### How to Import NumPy

After installing NumPy, you can import it into your Python code using the following convention:

```python
import numpy as np
```

This widely-used convention allows access to NumPy features with a short, recognizable prefix (`np.`), distinguishing them from other similarly named features.

---

### Reading the Example Code

In the NumPy documentation, you will encounter blocks of code like:

```python
>>> a = np.array([[1, 2, 3],
...              [4, 5, 6]])
>>>a.shape
(2, 3)
```

- Text preceded by `>>>` or `...` represents input, the code you would enter in a script or at a Python prompt.
- Everything else is output, the results of running your code.
- Note that `>>>` and `...` are not part of the code and may cause an error if entered at a Python prompt.

---

### Why Use NumPy?

Python lists are excellent, general-purpose containers. They are "heterogeneous," meaning they can contain elements of various types, and are quite fast for performing individual operations on a few elements.

However, depending on the data characteristics and operation types, other containers might be more suitable. By leveraging these characteristics, we can improve speed, reduce memory consumption, and provide a high-level syntax for common processing tasks.

**NumPy excels when dealing with large quantities of "homogeneous" (same-type) data that need processing on the CPU.**

---

### Key Points:

- **Homogeneous Data Handling:** Ideal for large, same-type datasets.
- **Efficiency:** Offers functions that operate efficiently on large datasets.
- **High-Level Syntax:** Simplifies performing common processing tasks.

### Arrays in Computer Programming

#### What is an Array?

- **Definition**: An array is a data structure that stores a collection of elements, typically of the same type, in a contiguous block of memory. It allows for efficient storage and retrieval of data.

`Contiguous` generally means being in contact or next to each other, without anything in between.

- **Types of Arrays**:
  - **One-Dimensional (1D) Array**: A linear sequence of elements, similar to a list.
  - **Two-Dimensional (2D) Array**: A grid or table of elements with rows and columns.
  - **Three-Dimensional (3D) Array**: A collection of 2D arrays, conceptualized as a stack of tables.
  - **N-Dimensional (N-D) Array**: Generalization to an arbitrary number of dimensions, used in NumPy as `ndarray`.

#### Characteristics of NumPy Arrays

- **Homogeneous Elements**: All elements must be of the same data type, ensuring consistent data representation and efficient memory usage.
- **Fixed Size**: The total number of elements in the array cannot change once the array is created.
- **Rectangular Shape**: The array must be rectangular, meaning each row in a 2D array (or higher dimension) must have the same number of columns.
- **Efficiency**: NumPy arrays are designed to be faster, more memory-efficient, and more convenient than less restrictive data structures due to these characteristics.

#### Array Fundamentals

- **Initialization from Python Sequence**:
  ```python
  import numpy as np
  a = np.array([1, 2, 3, 4, 5, 6])
  ```
- **Accessing Elements**:
  - **Individual Element**: Use the integer index within square brackets.
    ```python
    a[0]  # Accesses the first element
    # Output: 1
    ```
  - **0-Indexed**: NumPy arrays are 0-indexed, meaning the first element is accessed using index 0.
  - **Mutability**: Elements of the array can be modified.
    ```python
    a[0] = 10
    # Output: array([10, 2, 3, 4, 5, 6])
    ```

- **Slicing**:
  - **Slice Notation**: Use Python slice notation for indexing.
    ```python
    a[:3]  # Accesses the first three elements
    # Output: array([10, 2, 3])
    ```
  - **View vs. Copy**: Slicing an array returns a view, which refers to the data in the original array, allowing mutations through the view.
    ```python
    b = a[3:]
    b[0] = 40
    # Output: array([10, 2, 3, 40, 5, 6])
    ```

- **Two-Dimensional Arrays**:
  - **Initialization**:
    ```python
    a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
    ```
  - **Accessing Elements**:
    - **Using Row and Column Indices**: Specify the index along each axis within a single set of square brackets, separated by commas.
      ```python
      a[1, 3]  # Accesses the element in row 1, column 3
      # Output: 8
      ```

#### Array Attributes

- **Number of Dimensions (`ndim`)**:
  ```python
  a.ndim  # Returns the number of dimensions
  # Output: 2
  ```
- **Shape (`shape`)**:
  ```python
  a.shape  # Returns a tuple with the size along each dimension
  # Output: (3, 4)
  ```
- **Total Number of Elements (`size`)**:
  ```python
  a.size  # Returns the total number of elements
  # Output: 12
  ```
- **Data Type (`dtype`)**:
  ```python
  a.dtype  # Returns the data type of elements
  # Output: dtype('int64')
  ```

#### Creating Basic Arrays

- **Array Filled with Zeros**:
  ```python
  np.zeros(2)
  # Output: array([0., 0.])
  ```
- **Array Filled with Ones**:
  ```python
  np.ones(2)
  # Output: array([1., 1.])
  ```
- **Empty Array**:
  ```python
  np.empty(2)  # Creates an array with uninitialized elements
  # Output: array([3.14, 42.])  # Values may vary
  ```
- **Array with Range of Elements**:
  ```python
  np.arange(4)
  # Output: array([0, 1, 2, 3])
  ```
- **Array with Range of Evenly Spaced Intervals**:
  ```python
  np.arange(2, 9, 2)
  # Output: array([2, 4, 6, 8])
  ```
- **Array with Linearly Spaced Values**:
  ```python
  np.linspace(0, 10, num=5)
  # Output: array([ 0. ,  2.5,  5. ,  7.5, 10. ])
  ```

- **Specifying Data Type**:
  ```python
  x = np.ones(2, dtype=np.int64)
  # Output: array([1, 1])
  ```

### Additional Notes

- **Axis Terminology**: In NumPy, a dimension is often referred to as an "axis". For example, a 2D array has two axes: rows and columns.
- **Mathematical Terms**:
  - **0-D Array**: Scalar
  - **1-D Array**: Vector
  - **2-D Array**: Matrix
  - **N-D Array**: Tensor (N > 2)
  - Note: These terms should be used cautiously as they have specific mathematical meanings that differ from array operations.

More about arrays :[Numpy Quickstart](https://github.com/shyama7004/OpenCV-Personal-Documentation/blob/main/readme.md/Other%20Defs/NumPy%20quickstart.md)

### Study Notes: Adding, Removing, and Sorting Elements in NumPy

#### Sorting Elements

- **Basic Sorting**: Using `np.sort()`, you can sort the elements of an array in ascending order. You can also specify the axis, kind, and order.
  ```python
  import numpy as np
  arr = np.array([2, 1, 5, 3, 7, 4, 6, 8])
  np.sort(arr)
  # Output: array([1, 2, 3, 4, 5, 6, 7, 8])
  ```

- **Other Sorting Functions**:
  - **`argsort`**: Returns the indices that would sort an array.
  - **`lexsort`**: Performs an indirect stable sort using multiple keys.
  - **`searchsorted`**: Finds elements in a sorted array.
    ```py
     import numpy as np 

    edges =np.array([-1, 3.3, 9.1, 10.0])
    values = np.array([0.0, 4.1, 12.0])

    result = np.searchsorted(edges, values)
    print(result())  # Output: [1, 2, 4]
    ```
  - **`partition`**: Partially sorts the array.

#### Concatenating Arrays

- **Basic Concatenation**: Using `np.concatenate()`, you can combine two arrays along a specified axis.
  ```python
  a = np.array([1, 2, 3, 4])
  b = np.array([5, 6, 7, 8])
  np.concatenate((a, b))
  # Output: array([1, 2, 3, 4, 5, 6, 7, 8])
  ```

- **Concatenating 2D Arrays**:
  ```python
  x = np.array([[1, 2], [3, 4]])
  y = np.array([[5, 6]])
  np.concatenate((x, y), axis=0)
  # Output: array([[1, 2],
  #                [3, 4],
  #                [5, 6]])
  ```

Axes in NumPy Arrays

•	0-axis (axis=0): The first axis of the array, which refers to the rows in a 2D array.

•	1-axis (axis=1): The second axis of the array, which refers to the columns in a 2D array.

For a 2D array:

•	axis=0 means concatenation is done along rows (vertically).

•	axis=1 means concatenation is done along columns (horizontally).


#### Removing Elements

- **Using Indexing**: To remove elements from an array, use indexing to select the elements you want to keep.
  ```python
  arr = np.array([1, 2, 3, 4, 5, 6])
  arr = np.delete(arr, [1, 3])
  # Output: array([1, 3, 5, 6])
  ```

### Shape and Size of an Array

For example,
```py
array_example = np.array([[[0, 1, 2, 3],
                           [4, 5, 6, 7]],

                          [[0, 1, 2, 3],
                           [4, 5, 6, 7]],

                          [[0 ,1 ,2, 3],
                           [4, 5, 6, 7]]])
```

- **Number of Dimensions (`ndim`)**: will tell you the number of axes, or dimensions, of the array.
  
  ```python
  array_example.ndim  # Number of axes (dimensions)
  # Output: 3
  ```

- **Total Number of Elements (`size`)**: will tell you the total number of elements of the array. This is the product of the elements of the array’s shape.
  
  ```python
  array_example.size  # Total number of elements
  # Output: 24
  ```

- **Shape of the Array (`shape`)**: will display a tuple of integers that indicate the number of elements stored along each dimension of the array. If, for example, you have a 2-D array with 2 rows and 3 columns, the shape of your array is (2, 3).
  
  ```python
  array_example.shape  # Tuple of integers indicating the number of elements along each dimension
  # Output: (3, 2, 4)
  ```

### Reshaping Arrays

- **Using `reshape()`**: Changes the shape of an array without altering its data.
  ```python
  a = np.arange(6)
  b = a.reshape(3, 2)
  # Output: array([[0, 1],
  #                [2, 3],
  #                [4, 5]])
  ```

- **Optional Parameters**:
  - **`newshape`**: The new shape, specified as an integer or tuple of integers.
  - **`order`**: Specifies whether to read/write elements using C-like (row-major) or Fortran-like (column-major) order.
  ```python
  np.reshape(a, newshape=(1, 6), order='C')
  # Output: array([[0, 1, 2, 3, 4, 5]])
  ```

### Adding a New Axis

- **Using `np.newaxis`**: Increases the dimensions of an array by one dimension.
  ```python
  a = np.array([1, 2, 3, 4, 5, 6])
  a2 = a[np.newaxis, :]
  # Output: (1, 6)
  ```

- **Converting to Row Vector**:
  ```python
  row_vector = a[np.newaxis, :]
  # Output: (1, 6)
  ```

- **Converting to Column Vector**:
  ```python
  col_vector = a[:, np.newaxis]
  # Output: (6, 1)
  ```

- **Using `np.expand_dims`**: Adds a new axis at a specified position.
  ```python
  b = np.expand_dims(a, axis=1)
  # Output: (6, 1)

  c = np.expand_dims(a, axis=0)
  # Output: (1, 6)
  ```

### Additional Resources

- [NumPy Documentation on Sorting](https://numpy.org/doc/stable/reference/routines.sort.html)
- [NumPy Documentation on Concatenation](https://numpy.org/doc/stable/reference/generated/numpy.concatenate.html)
- [NumPy Documentation on Shape Manipulation](https://numpy.org/doc/stable/reference/routines.shape.html)
- [NumPy Documentation on Adding a New Axis](https://numpy.org/doc/stable/reference/arrays.indexing.html#arrays-indexing)


## Indexing and Slicing in NumPy

Indexing and slicing in NumPy arrays can be done similarly to how it's done with Python lists.

### Basic Indexing and Slicing

Given an array:
```python
data = np.array([1, 2, 3])
```

- Access a single element:
  ```python
  data[1]
  # Output: 2
  ```

- Slice a portion of the array:
  ```python
  data[0:2]
  # Output: array([1, 2])
  ```

- Slice from a specific index to the end:
  ```python
  data[1:]
  # Output: array([2, 3])
  ```

- Slice the last two elements:
  ```python
  data[-2:]
  # Output: array([2, 3])
  ```
  You can visualize it this way:
  <img src ="https://numpy.org/doc/stable/_images/np_indexing.png">

### Visual Representation
Indexing and slicing can help you select specific sections or elements from your array for further analysis or operations.

### Conditional Selection

Select elements based on conditions:

#### Example Array
```python
a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

- Select elements less than 5:
  ```python
  print(a[a < 5])
  # Output: [1 2 3 4]
  ```

- Select elements greater than or equal to 5:
  ```python
  five_up = (a >= 5)
  print(a[five_up])
  # Output: [ 5  6  7  8  9 10 11 12]
  ```

- Select elements divisible by 2:
  ```python
  divisible_by_2 = a[a % 2 == 0]
  print(divisible_by_2)
  # Output: [ 2  4  6  8 10 12]
  ```

- Select elements that satisfy multiple conditions:
  ```python
  c = a[(a > 2) & (a < 11)]
  print(c)
  # Output: [ 3  4  5  6  7  8  9 10]
  ```

### Boolean Array

Logical operators return boolean values indicating if conditions are met:

- Example with `&` and `|` operators:
  ```python
  five_up = (a > 5) | (a == 5)
  print(five_up)
  # Output: 
  # [[False False False False]
  #  [ True  True  True  True]
  #  [ True  True  True True]]
  ```

### Using `np.nonzero()`

Identify indices of elements that meet certain conditions:

#### Example Array
```python
a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

- Find indices of elements less than 5:
  ```python
  b = np.nonzero(a < 5)
  print(b)
  # Output: (array([0, 0, 0, 0]), array([0, 1, 2, 3]))
  ```
In this example, a tuple of arrays was returned: one for each dimension. The first array represents the row indices where these values are found, and the second array represents the column indices where the values are found.

- Generate a list of coordinates:

If you want to generate a list of coordinates where the elements exist, you can zip the arrays, iterate over the list of coordinates, and print them. For example:

 ```python
list_of_coordinates = list(zip(b[0], b[1]))

for coord in list_of_coordinates:
    print(coord)
```

### Explanation:

1. **`zip(b[0], b[1])`**:
    - The `zip` function is used to combine elements from multiple iterables (lists, tuples, etc.) into tuples.
      
    - Here, `b[0]` and `b[1]` are likely lists (or arrays) containing coordinates.

    - `zip(b[0], b[1])` pairs up elements from `b[0]` and `b[1]` at corresponding positions, creating an iterator of tuples.

    - For example, if `b[0] = [1, 2, 3]` and `b[1] = [4, 5, 6]`, then `zip(b[0], b[1])` will produce an iterator containing `(1, 4)`, `(2, 5)`, and `(3, 6)`.

2. **`list(zip(b[0], b[1]))`**:
    - The `list` function converts the iterator returned by `zip` into a list.

    - This results in a list of tuples, where each tuple contains paired elements from `b[0]` and `b[1]`.

    - Continuing the previous example, `list(zip(b[0], b[1]))` will produce `[(1, 4), (2, 5), (3, 6)]`.

3. **`list_of_coordinates`**:
    - This variable now holds the list of tuples generated by the `zip` function.

    - Each tuple represents a pair of coordinates from the corresponding positions in `b[0]` and `b[1]`.

4. **`for coord in list_of_coordinates:`**:
    - This is a `for` loop that iterates over each element (tuple) in `list_of_coordinates`.

5. **`print(coord)`**:
    - Inside the loop, each `coord` (which is a tuple) is printed.

    - If `list_of_coordinates` contains `[(1, 4), (2, 5), (3, 6)]`, the loop will print `(1, 4)`, `(2, 5)`, and `(3, 6)` on separate lines.

### Example:

If `b` is defined as follows:
```python
b = [
    [1, 2, 3],  # b[0]
    [4, 5, 6]   # b[1]
]
```

The code execution will be:
1. `zip(b[0], b[1])` will produce an iterator that yields `(1, 4)`, `(2, 5)`, and `(3, 6)`.

2. `list(zip(b[0], b[1]))` will convert this into `[(1, 4), (2, 5), (3, 6)]`.

3. `list_of_coordinates` will be `[(1, 4), (2, 5), (3, 6)]`.

4. The `for` loop will iterate over this list, and `print(coord)` will output:
    ```
    (1, 4)
    (2, 5)
    (3, 6)
    ```

This code essentially pairs elements from two lists and prints each pair.

- Print elements less than 5:
  ```python
  print(a[b])
  # Output: [1 2 3 4]
  ```

- Handle elements not found:
  
If the element you’re looking for doesn’t exist in the array, then the returned array of indices will be empty. For example:


  ```python
  not_there = np.nonzero(a == 42)
  print(not_there)
  # Output: (array([], dtype=int64), array([], dtype=int64))
  ```

## Creating an Array from Existing Data

### Slicing and Indexing

Create a new array from a section of an existing array:

#### Example Array
```python
a = np.array([1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
```

- Create a new array by slicing:
  ```python
  arr1 = a[3:8]
  print(arr1)
  # Output: array([4, 5, 6, 7, 8])
  ```

### Stacking Arrays

#### Example Arrays
```python
a1 = np.array([[1, 1], [2, 2]])
a2 = np.array([[3, 3], [4, 4]])
```

- Stack vertically:
  ```python
  np.vstack((a1, a2))
  # Output: 
  # array([[1, 1],
  #        [2, 2],
  #        [3, 3],
  #        [4, 4]])
  ```

- Stack horizontally:
  ```python
  np.hstack((a1, a2))
  # Output: 
  # array([[1, 1, 3, 3],
  #        [2, 2, 4, 4]])
  ```

### Splitting Arrays

#### Example Array
```python
x = np.arange(1, 25).reshape(2, 12)
```

- Split into three equally shaped arrays:
  ```python
  np.hsplit(x, 3)
  # Output: 
  # [array([[ 1,  2,  3,  4],
  #         [13, 14, 15, 16]]), 
  #  array([[ 5,  6,  7,  8],
  #         [17, 18, 19, 20]]), 
  #  array([[ 9, 10, 11, 12],
  #         [21, 22, 23, 24]])]
  ```

- Split after specific columns:
  ```python
  np.hsplit(x, (3, 4))
  # Output: 
  # [array([[ 1,  2,  3],
  #         [13, 14, 15]]), 
  #  array([[ 4],
  #         [16]]), 
  #  array([[ 5,  6,  7,  8,  9, 10, 11, 12],
  #         [17, 18, 19, 20, 21, 22, 23, 24]])]
  ```

### Views and Copies

#### Example Array
```python
a = np.array([[1, 2, 3, 4], [5, 6, 7, 8], [9, 10, 11, 12]])
```

- Create a view and modify:
  ```python
  b1 = a[0, :]
  b1[0] = 99
  # b1 Output: array([99,  2,  3,  4])
  # a Output: 
  # array([[99,  2,  3,  4],
  #        [ 5,  6,  7,  8],
  #        [ 9, 10, 11, 12]])
  ```

- Create a copy to avoid modifying the original array:
  ```python
  b2 = a.copy()
  ```

## Basic Array Operations

### Arithmetic Operations

#### Example Arrays
Once you’ve created your arrays, you can start to work with them. Let’s say, for example, that you’ve created two arrays, one called “data” and one called “ones”
<img src="https://numpy.org/doc/stable/_images/np_array_dataones.png">

```python
data = np.array([1, 2])
ones = np.ones(2, dtype=int)
```

- Addition:
  ```python
  data + ones
  # Output: array([2, 3])
  ```
<img src="https://numpy.org/doc/stable/_images/np_data_plus_ones.png">

- Subtraction:
  ```python
  data - ones
  # Output: array([0, 1])
  ```

<img src="https://numpy.org/doc/stable/_images/np_sub_mult_divide.png">

- Multiplication:
  ```python
  data * data
  # Output: array([1, 4])
  ```

- Division:
  ```python
  data / data
  # Output: array([1., 1.])
  ```

### Summing Elements

#### Example Arrays
```python
a = np.array([1, 2, 3, 4])
b = np.array([[1, 1], [2, 2]])
```

- Sum of elements:
  ```python
  a.sum()
  # Output: 10
  ```

- Sum along rows:
  ```python
  b.sum(axis=0)
  # Output: array([3, 3])
  ```

- Sum along columns:
  ```python
  b.sum(axis=1)
  # Output: array([2, 4])
  ```

Learn more about basic operations [here](https://numpy.org/doc/stable/reference/routines.math.html).

### Broadcasting

**Concept Overview:**
Broadcasting is a powerful mechanism that allows NumPy to perform operations between arrays of different shapes. This is particularly useful when you need to carry out operations between an array and a single number (known as a scalar) or between arrays of different sizes.

**Example:**
Suppose you have an array `data` that contains distances in miles, and you want to convert these distances to kilometers. You can achieve this with the following code:

```python
import numpy as np

data = np.array([1.0, 2.0])
converted_data = data * 1.6
print(converted_data)
```

Output:
```python
array([1.6, 3.2])
```
<img src="https://numpy.org/doc/stable/_images/np_multiply_broadcasting.png">
**Explanation:**
NumPy understands that the multiplication should happen element-wise, applying the operation to each cell in the array `data`. This is known as broadcasting.

**Broadcasting Rules:**
- The dimensions of the arrays must be compatible. Dimensions are compatible when:
  - They are equal, or
  - One of them is 1.
- If the dimensions are not compatible, NumPy will raise a `ValueError`.

For more information, refer to the [NumPy documentation on broadcasting](https://numpy.org/doc/stable/user/basics.broadcasting.html).

---

### More Useful Array Operations

**Aggregation Functions:**
NumPy provides various aggregation functions to perform operations like finding the maximum, minimum, sum, mean, product, and standard deviation of elements in an array.

**Common Aggregation Functions:**
- `max()`: Returns the maximum value.
- `min()`: Returns the minimum value.
- `sum()`: Returns the sum of the elements.
- `mean()`: Returns the average value.
- `prod()`: Returns the product of the elements.
- `std()`: Returns the standard deviation.

**Example:**
```python
data = np.array([1.0, 2.0])

print("Max:", data.max())  # 2.0
print("Min:", data.min())  # 1.0
print("Sum:", data.sum())  # 3.0
print("Mean:", data.mean())  # 1.5
print("Product:", data.prod())  # 2.0
print("Standard Deviation:", data.std())  # 0.5
```
<img src="https://numpy.org/doc/stable/_images/np_aggregation.png">

**Aggregation with Axes:**
You can specify the axis along which to perform the aggregation. For example, to find the minimum value within each column:

```python
a = np.array([
    [0.45053314, 0.17296777, 0.34376245, 0.5510652],
    [0.54627315, 0.05093587, 0.40067661, 0.55645993],
    [0.12697628, 0.82485143, 0.26590556, 0.56917101]
])

min_per_column = a.min(axis=0)
print(min_per_column)
```

Output:
```python
array([0.12697628, 0.05093587, 0.26590556, 0.5510652])
```

For more details, refer to the [NumPy documentation on array methods](https://numpy.org/doc/stable/reference/routines.array-manipulation.html).

---

### Creating Matrices

**Creating a 2D Array:**
You can create a 2D array (or matrix) by passing a list of lists to `np.array`.

**Example:**
```python
data = np.array([[1, 2], [3, 4], [5, 6]])
print(data)
```

Output:
```python
array([[1, 2],
       [3, 4],
       [5, 6]])
```
<img src="https://numpy.org/doc/stable/_images/np_create_matrix.png">

**Indexing and Slicing:**
Indexing and slicing operations allow you to manipulate matrices.

**Examples:**
```python
print(data[0, 1])  # 2 (element at first row, second column)
print(data[1:3])  # Sub-matrix from second to third row
print(data[0:2, 0])  # First column elements from first to second row
```
<img src="https://numpy.org/doc/stable/_images/np_matrix_indexing.png">
**Aggregating Matrices:**
Aggregation functions can be applied to matrices as well.

**Examples:**
```python
print("Max:", data.max())  # 6
print("Min:", data.min())  # 1
print("Sum:", data.sum())  # 21
```
<img src="https://numpy.org/doc/stable/_images/np_matrix_aggregation.png">

You can also specify the axis for aggregation.

**Example:**
```python
data = np.array([[1, 2], [5, 3], [4, 6]])

print("Max per column:", data.max(axis=0))  # [5, 6]
print("Max per row:", data.max(axis=1))  # [2, 5, 6]
```
<img src="https://numpy.org/doc/stable/_images/np_matrix_aggregation_row.png">
**Matrix Arithmetic:**
You can perform arithmetic operations on matrices of the same size.

**Example:**
```python
data = np.array([[1, 2], [3, 4]])
ones = np.array([[1, 1], [1, 1]])

result = data + ones
print(result)
```

Output:
```python
array([[2, 3],
       [4, 5]])
```
<img src="https://numpy.org/doc/stable/_images/np_matrix_arithmetic.png">

For matrices of different sizes, broadcasting rules apply.

**Example:**
```python
data = np.array([[1, 2], [3, 4], [5, 6]])
ones_row = np.array([[1, 1]])

result = data + ones_row
print(result)
```

Output:
```python
array([[2, 3],
       [4, 5],
       [6, 7]])
```
<img src="https://numpy.org/doc/stable/_images/np_matrix_broadcasting.png">

**Printing N-Dimensional Arrays:**
When printing N-dimensional arrays, the last axis is the fastest-changing axis, while the first axis is the slowest.

**Example:**
```python
array = np.ones((4, 3, 2))
print(array)
```

Output:
```python
array([[[1., 1.],
        [1., 1.],
        [1., 1.]],

       [[1., 1.],
        [1., 1.],
        [1., 1.]],

       [[1., 1.],
        [1., 1.],
        [1., 1.]],

       [[1., 1.],
        [1., 1.],
        [1., 1.]]])
```

**Creating Initialized Arrays:**
NumPy offers functions like `ones()`, `zeros()`, and random number generators to initialize arrays.

**Examples:**
```python
print(np.ones(3))  # [1., 1., 1.]
print(np.zeros(3))  # [0., 0., 0.]
rng = np.random.default_rng()
print(rng.random(3))  # [0.63696169, 0.26978671, 0.04097352]
```
<img src="https://numpy.org/doc/stable/_images/np_ones_zeros_random.png">

You can create 2D arrays by passing a tuple describing the dimensions.

**Examples:**
```python
np.ones((3, 2))
array([[1., 1.],
       [1., 1.],
       [1., 1.]])
np.zeros((3, 2))
array([[0., 0.],
       [0., 0.],
       [0., 0.]])
rng.random((3, 2)) 
array([[0.01652764, 0.81327024],
       [0.91275558, 0.60663578],
       [0.72949656, 0.54362499]])  # may vary
```
<img src="https://numpy.org/doc/stable/_images/np_ones_zeros_matrix.png">

For more information, refer to the [NumPy documentation on array creation routines](https://numpy.org/doc/stable/reference/routines.array-creation.html).

---

### Generating Random Numbers

**Importance:**
Random number generation is crucial for configuring and evaluating numerical and machine learning algorithms. It is used for tasks like initializing weights in neural networks, splitting datasets, and shuffling data.

**Generating Random Integers:**
You can generate random integers using `Generator.integers`. 

**Example:**
```python
rng = np.random.default_rng()
random_integers = rng.integers(5, size=(2, 4))
print(random_integers)
```

Output:
```python
array([[2, 1, 1, 0],
       [0, 0, 0, 4]])
```

For more details, refer to the [NumPy documentation on random number generation](https://numpy.org/doc/stable/reference/random/index.html).

---

### How to Get Unique Items and Counts

**Finding Unique Elements:**
You can use `np.unique` to find unique elements in an array.

**Example:**
```python
a = np.array([11, 11, 12, 13, 14, 15, 16, 17, 12, 13, 11, 14, 18, 19, 20])
unique_values = np.unique(a)
print(unique_values)
```

Output:
```python
array([11, 12, 13, 14, 15, 16, 17, 18, 19, 20])
```

**Getting Indices of Unique Values:**
You can pass the `return_index` argument to get the indices of unique values.

**Example:**
```python
unique_values, indices_list = np.unique(a, return_index=True)
print(indices_list)
```

Output:
```python
array([ 0,  2,  3,  4,  5,  6,  7, 12, 13, 14])
```

**Getting Counts of Unique Values:**
You can pass the `return_counts` argument to get the frequency count of unique values.

**Example:**
```python
unique_values, occurrence_count = np.unique(a, return_counts=True)
print(occurrence_count)
```

Output:
```python
array([3, 2, 2, 2, 1, 1, 1, 1, 1, 1])
```

**Finding Unique Rows in 2D Arrays:**
You can find unique rows in a 2D array by specifying the `axis` argument.

**Example:**
```python
a_2d = np.array([[1, 2, 3, 4],
                 [5, 6, 7, 8],
                 [9, 10, 11, 12],
                 [1, 2, 3, 4]])

unique_rows = np.unique(a_2d, axis=0)
print(unique_rows)
```

Output:
```python
array([[1, 2, 3, 4],
       [5, 6, 7, 8],
       [9, 10, 11, 12]])
```

For more details, refer to the [NumPy documentation on unique](https://numpy.org/doc/stable/reference/generated/numpy.unique.html).

---

### Transposing and Reshaping

**Transposing Arrays:**
Transposing an array swaps its rows and columns. You can use `transpose()` or the `.T` attribute.

<img src="https://numpy.org/doc/stable/_images/np_transposing_reshaping.png">

**Example:**
```python
arr = np.arange(6).reshape((2, 3))
print(arr)
transposed = arr.transpose()
print(transposed)
```

Output:
```python
array([[0, 1, 2],
       [3, 4, 5]])

array([[0, 3],
       [1, 4],
       [2, 5]])
```

**Reshaping Arrays:**
Reshaping allows you to change the shape of an array without altering its data.

**Example:**
```python
data = np.array([1, 2, 3, 4, 5, 6])
reshaped = data.reshape(2, 3)
print(reshaped)
```

Output:
```python
array([[1, 2, 3],
       [4, 5, 6]])
```

Reshaping to different shapes is possible as long as the total number of elements remains the same.

**Example:**
```python
reshaped = data.reshape(3, 2)
print(reshaped)
```

Output:
```python
array([[1, 2],
       [3, 4],
       [5, 6]])
```
<img src="https://numpy.org/doc/stable/_images/np_reshape.png">
For more details, refer to the [NumPy documentation on array manipulation routines](https://numpy.org/doc/stable/reference/routines.array-manipulation.html).

---

### Reversing an Array

**Reversing an Array:**
To reverse an array, you can use the `np.flip()` function.

**Reversing a 1D Array:**
```python
arr = np.array([1, 2, 3, 4, 5, 6, 7, 8])
reversed_arr = np.flip(arr)
print(reversed_arr)
```

Output:
```python
array([8, 7, 6, 5, 4, 3, 2, 1])
```

**Reversing a 2D Array:**
```python
arr_2d = np.array([[1, 2, 3, 4],
                   [5, 6, 7, 8],
                   [9, 10, 11, 12]])
reversed_arr = np.flip(arr_2d)
print(reversed_arr)
```

Output:
```python
array([[12, 11, 10, 9],
       [8, 7, 6, 5],
       [4, 3, 2, 1]])
```

**Reversing Specific Axes:**
You can reverse specific rows or columns by specifying the axis.

**Example:**
```python
reversed_arr_rows = np.flip(arr_2d, axis=0)
print(reversed_arr_rows)
```

Output:
```python
array([[ 9, 10, 11, 12],
       [ 5,  6,  7,  8],
       [ 1,  2,  3,  4]])
```

**Reversing Specific Columns:**
```python
reversed_arr_columns = np.flip(arr_2d, axis=1)
print(reversed_arr_columns)
```

Output:
```python
array([[ 4,  3,  2,  1],
       [ 8,  7,  6,  5],
       [12, 11, 10,  9]])
```

**Reversing Specific Rows or Columns:**
```python
arr_2d[1] = np.flip(arr_2d[1])
print(arr_2d)
```

Output:
```python
array([[ 1,  2,  3,  4],
       [ 8,  7,  6,  5],
       [ 9, 10, 11, 12]])
```

**Reversing Specific Columns:**
```python
arr_2d[:, 1] = np.flip(arr_2d[:, 1])
print(arr_2d)
```

Output:
```python
array([[ 1, 10,  3,  4],
       [ 8,  7,  6,  5],
       [ 9,  2, 11, 12]])
```

For more details, refer to the [NumPy documentation on flipping](https://numpy.org/doc/stable/reference/generated/numpy.flip.html).

---

These comprehensive notes cover various essential topics in NumPy, including broadcasting, array operations, creating and manipulating matrices, generating random numbers, finding unique elements, transposing and reshaping arrays, and reversing arrays. This detailed guide should provide a solid foundation for working with NumPy arrays in Python.
