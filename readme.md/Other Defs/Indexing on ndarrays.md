# Indexing in NumPy's ndarrays

## Overview

Indexing on NumPy ndarrays can be accomplished using the standard Python `x[obj]` syntax, where `x` is the array and `obj` is the selection. There are different kinds of indexing available depending on the nature of `obj`: basic indexing, advanced indexing, and field access.

Most examples provided here will demonstrate how indexing can be used to reference data in an array, but the same principles apply when assigning values to an array. For specific examples and explanations of assignments, see the section on assigning values to indexed arrays.

### Key Points
- `x[(exp1, exp2, ..., expN)]` is equivalent to `x[exp1, exp2, ..., expN]`; the latter is just syntactic sugar for the former.
- NumPy uses C-order indexing, meaning that the last index typically represents the most rapidly changing memory location.

## Types of Indexing

### Basic Indexing
Basic indexing involves:
- Single element indexing
- Slicing and striding

#### Single Element Indexing
Single element indexing works like standard Python sequences:
- It is 0-based.
- Negative indices can be used to index from the end of the array.

**Example:**
```python
import numpy as np
x = np.arange(10)
print(x[2])   # Output: 2
print(x[-2])  # Output: 8
```

For multidimensional arrays, you do not need to separate each dimension’s index into its own set of square brackets:
```python
x.shape = (2, 5)  # x is now 2-dimensional
print(x[1, 3])    # Output: 8
print(x[1, -1])   # Output: 9
```

If a multidimensional array is indexed with fewer indices than its dimensions, a subdimensional array is returned:
```python
print(x[0])  # Output: array([0, 1, 2, 3, 4])
print(x[0][2])  # Output: 2
print(x[0, 2] == x[0][2])  # True, but x[0][2] is less efficient
```

#### Slicing and Striding
Slicing extends Python's basic slicing concept to N dimensions. Basic slicing occurs when `obj` is a slice object (constructed by `start:stop:step` notation inside of brackets), an integer, or a tuple of slice objects and integers. Ellipsis (`...`) and `newaxis` objects can be interspersed with these.

**Example:**
```python
x = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
print(x[1:7:2])  # Output: array([1, 3, 5])
```

Negative indices and steps are also allowed:
```python
print(x[-2:10])       # Output: array([8, 9])
print(x[-3:3:-1])     # Output: array([7, 6, 5, 4])
print(x[5:])          # Output: array([5, 6, 7, 8, 9])
```

When slicing tuples contain fewer objects than the array's dimensions, `:` is assumed for subsequent dimensions:
```python
x = np.array([[[1],[2],[3]], [[4],[5],[6]]])
print(x.shape)  # Output: (2, 3, 1)
print(x[1:2])   # Output: array([[[4], [5], [6]]])
```

### Dimensional Indexing Tools
- **Ellipsis (`...`)**: Expands to the number of `:` objects needed for the selection tuple to index all dimensions.
```python
x = np.array([[1, 2, 3], [4, 5, 6]])
print(x[..., 0])  # Output: array([1, 4])
print(x[:, :, 0])  # Equivalent to above
```

- **`newaxis`**: Expands the dimensions of the resulting selection by one unit-length dimension.
```python
x = np.arange(5)
print(x[:, np.newaxis] + x[np.newaxis, :])
# Output: array([[0, 1, 2, 3, 4],
#                [1, 2, 3, 4, 5],
#                [2, 3, 4, 5, 6],
#                [3, 4, 5, 6, 7],
#                [4, 5, 6, 7, 8]])
```

### Advanced Indexing
Advanced indexing is triggered when `obj` is a non-tuple sequence object, an ndarray (of data type integer or bool), or a tuple with at least one sequence object or ndarray (of data type integer or bool).

**Important Points:**
- Advanced indexing always returns a copy of the data.
- `x[(1, 2, 3),]` triggers advanced indexing, while `x[(1, 2, 3)]` triggers basic selection.

#### Integer Array Indexing
Integer array indexing allows the selection of arbitrary items in the array based on their N-dimensional index:
```python
x = np.arange(10, 1, -1)
print(x[np.array([3, 3, 1, 8])])  # Output: array([7, 7, 9, 2])
```

If index values are out of bounds, an IndexError is thrown:
```python
x = np.array([[1, 2], [3, 4], [5, 6]])
print(x[np.array([1, -1])])  # Output: array([[3, 4], [5, 6]])
# print(x[np.array([3, 4])])  # IndexError
```

When the index consists of as many integer arrays as dimensions of the array being indexed, the indexing is straightforward but different from slicing:
```python
y = np.arange(35).reshape(5, 7)
print(y[np.array([0, 2, 4]), np.array([0, 1, 2])])
# Output: array([ 0, 15, 30])
```

If the index arrays do not have the same shape, there is an attempt to broadcast them to the same shape:
```python
# y[np.array([0, 2, 4]), np.array([0, 1])]  # IndexError
```

Using a scalar value for some indices:
```python
print(y[np.array([0, 2, 4]), 1])  # Output: array([ 1, 15, 29])
```

#### Boolean Array Indexing
Boolean array indexing occurs when `obj` is an array of Boolean type, such as may be returned from comparison operators:
```python
x = np.array([[1., 2.], [np.nan, 3.], [np.nan, np.nan]])
print(x[~np.isnan(x)])  # Output: array([1., 2., 3.])
```

Boolean arrays can also be combined with integer indexing arrays:
```python
x = np.arange(35).reshape(5, 7)
b = x > 20
print(x[b[:, 5]])  # Output: array([[21, 22, 23, 24, 25, 26, 27], [28, 29, 30, 31, 32, 33, 34]])
```

Combining multiple Boolean indexing arrays or a Boolean with an integer indexing array:
```python
x = np.array([[ 0,  1,  2], [ 3,  4,  5], [ 6,  7,  8], [ 9, 10, 11]])
rows = (x.sum(-1) % 2) == 0
columns = [0, 2]
print(x[np.ix_(rows, columns)])  # Output: array([[ 3,  5], [ 9, 11]])
```

Combining advanced and basic indexing:
```python
y = np.arange(35).reshape(5, 7)
print(y[np.array([0, 2, 4]), 1:3])  # Output: array([[ 1,  2], [15, 16], [29, 30]])
```

## Summary
Indexing in NumPy is a powerful tool for data manipulation and retrieval. Understanding the distinctions between basic and advanced indexing, and how to use slicing and Boolean arrays effectively, can greatly enhance your ability to work with multidimensional arrays. By combining different types of indexing, you can achieve complex data selection and manipulation tasks with concise and readable code.
