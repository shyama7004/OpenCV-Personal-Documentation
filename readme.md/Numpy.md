### NumPy: The Absolute Basics for Beginners

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

- Generate a list of coordinates:
  ```python
  list_of_coordinates = list(zip(b[0], b[1]))

  for coord in list_of_coordinates:
      print(coord)
  # Output:
  # (np.int64(0), np.int64(0))
  # (np.int64(0), np.int64(1))
  # (np.int64(0), np.int64(2))
  # (np.int64(0), np.int64(3))
  ```

- Print elements less than 5:
  ```python
  print(a[b])
  # Output: [1 2 3 4]
  ```

- Handle elements not found:
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
```python
data = np.array([1, 2])
ones = np.ones(2, dtype=int)
```

- Addition:
  ```python
  data + ones
  # Output: array([2, 3])
  ```

- Subtraction:
  ```python
  data - ones
  # Output: array([0, 1])
  ```

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

