# NumPy quickstart — NumPy v2.0 Manual

## Prerequisites

You’ll need to know a bit of Python. For a refresher, see the Python tutorial.

To work the examples, you’ll need matplotlib installed in addition to NumPy.

## Learner profile

This is a quick overview of arrays in NumPy. It demonstrates how n-dimensional ( ) arrays are represented and can be manipulated. In particular, if you don’t know how to apply common functions to n-dimensional arrays (without using for-loops), or if you want to understand axis and shape properties for n-dimensional arrays, this article might be of help.

## Learning Objectives

After reading, you should be able to:

- Understand the difference between one-, two- and n-dimensional arrays in NumPy;
- Understand how to apply some linear algebra operations to n-dimensional arrays without using for-loops;
- Understand axis and shape properties for n-dimensional arrays.

## The basics

NumPy’s main object is the homogeneous multidimensional array. It is a table of elements (usually numbers), all of the same type, indexed by a tuple of non-negative integers. In NumPy dimensions are called axes.

For example, the array for the coordinates of a point in 3D space, `[1, 2, 1]`, has one axis. That axis has 3 elements in it, so we say it has a length of 3. In the example pictured below, the array has 2 axes. The first axis has a length of 2, the second axis has a length of 3.

```
[[1., 0., 0.],
 [0., 1., 2.]]
```

NumPy’s array class is called `ndarray`. It is also known by the alias `array`. Note that `numpy.array` is not the same as the Standard Python Library class `array.array`, which only handles one-dimensional arrays and offers less functionality. The more important attributes of an `ndarray` object are:

- `ndarray.ndim`  
  the number of axes (dimensions) of the array.
- `ndarray.shape`  
  the dimensions of the array. This is a tuple of integers indicating the size of the array in each dimension. For a matrix with n rows and m columns, shape will be `(n,m)`. The length of the shape tuple is therefore the number of axes, `ndim`.
- `ndarray.size`  
  the total number of elements of the array. This is equal to the product of the elements of shape.
- `ndarray.dtype`  
  an object describing the type of the elements in the array. One can create or specify dtype’s using standard Python types. Additionally, NumPy provides types of its own. `numpy.int32`, `numpy.int16`, and `numpy.float64` are some examples.
- `ndarray.itemsize`  
  the size in bytes of each element of the array. For example, an array of elements of type `float64` has itemsize 8 (=64/8), while one of type `complex32` has itemsize 4 (=32/8). It is equivalent to `ndarray.dtype.itemsize`.
- `ndarray.data`  
  the buffer containing the actual elements of the array. Normally, we won’t need to use this attribute because we will access the elements in an array using indexing facilities.

## An example

```python
>>> import numpy as np
>>> a = np.arange(15).reshape(3, 5)
>>> a
array([[ 0,  1,  2,  3,  4],
       [ 5,  6,  7,  8,  9],
       [10, 11, 12, 13, 14]])
>>> a.shape
(3, 5)
>>> a.ndim
2
>>> a.dtype.name
'int64'
>>> a.itemsize
8
>>> a.size
15
>>> type(a)
<class 'numpy.ndarray'>
>>> b = np.array([6, 7, 8])
>>> b
array([6, 7, 8])
>>> type(b)
<class 'numpy.ndarray'>
```

## Array creation

There are several ways to create arrays.

For example, you can create an array from a regular Python list or tuple using the `array` function. The type of the resulting array is deduced from the type of the elements in the sequences.

```python
>>> import numpy as np
>>> a = np.array([2, 3, 4])
>>> a
array([2, 3, 4])
>>> a.dtype
dtype('int64')
>>> b = np.array([1.2, 3.5, 5.1])
>>> b.dtype
dtype('float64')
```

A frequent error consists in calling `array` with multiple arguments, rather than providing a single sequence as an argument.

```python
>>> a = np.array(1, 2, 3, 4)    # WRONG
Traceback (most recent call last):
  ...
TypeError: array() takes from 1 to 2 positional arguments but 4 were given
>>> a = np.array([1, 2, 3, 4])  # RIGHT
```

`array` transforms sequences of sequences into two-dimensional arrays, sequences of sequences of sequences into three-dimensional arrays, and so on.

```python
>>> b = np.array([(1.5, 2, 3), (4, 5, 6)])
>>> b
array([[1.5, 2. , 3. ],
       [4. , 5. , 6. ]])
```

The type of the array can also be explicitly specified at creation time:

```python
>>> c = np.array([[1, 2], [3, 4]], dtype=complex)
>>> c
array([[1.+0.j, 2.+0.j],
       [3.+0.j, 4.+0.j]])
```

Often, the elements of an array are originally unknown, but its size is known. Hence, NumPy offers several functions to create arrays with initial placeholder content. These minimize the necessity of growing arrays, an expensive operation.

The function `zeros` creates an array full of zeros, the function `ones` creates an array full of ones, and the function `empty` creates an array whose initial content is random and depends on the state of the memory. By default, the `dtype` of the created array is `float64`, but it can be specified via the keyword argument `dtype`.

```python
>>> np.zeros((3, 4))
array([[0., 0., 0., 0.],
       [0., 0., 0., 0.],
       [0., 0., 0., 0.]])
>>> np.ones((2, 3, 4), dtype=np.int16)
array([[[1, 1, 1, 1],
        [1, 1, 1, 1],
        [1, 1, 1, 1]],

       [[1, 1, 1, 1],
        [1, 1, 1, 1],
        [1, 1, 1, 1]]], dtype=int16)
>>> np.empty((2, 3)) 
array([[3.73603959e-262, 6.02658058e-154, 6.55490914e-260],  # may vary
       [5.30498948e-313, 3.14673309e-307, 1.00000000e+000]])
```

To create sequences of numbers, NumPy provides the `arange` function which is analogous to the Python built-in `range`, but returns an array.

```python
>>> np.arange(10, 30, 5)
array([10, 15, 20, 25])
>>> np.arange(0, 2, 0.3)  # it accepts float arguments
array([0. , 0.3, 0.6, 0.9, 1.2, 1.5, 1.8])
```

When `arange` is used with floating point arguments, it is generally not possible to predict the number of elements obtained, due to the finite floating point precision. For this reason, it is usually better to use the function `linspace` that receives as an argument the number of elements that we want, instead of the step:

```python
>>> from numpy import pi
>>> np.linspace(0, 2, 9)                   # 9 numbers from 0 to 2
array([0.  , 0.25, 0.5 , 0.75, 1.  , 1.25, 1.5 , 1.75, 2.  ])
>>> x = np.linspace(0, 2 * pi, 100)        # useful to evaluate function at lots
>>> f = np.sin(x)
```

`array`, `zeros`, `zeros_like`, `ones`, `ones_like`, `empty`, `empty_like`, `arange`, `linspace`, `random.Generator.random`, `random.Generator.normal`, `fromfunction`, `fromfile`

## Printing arrays

When you print an array, NumPy displays it in a similar way to nested lists, but with the following layout:

- the last axis is printed from left to right,
- the second-to-last is printed from top to bottom,
- the rest are also printed from top to bottom, with each slice separated from

 the next by an empty line.

```python
>>> a = np.arange(6)
>>> print(a)
[0 1 2 3 4 5]
>>> b = np.arange(12).reshape(4, 3)
>>> print(b)
[[ 0  1  2]
 [ 3  4  5]
 [ 6  7  8]
 [ 9 10 11]]
>>> c = np.arange(24).reshape(2, 3, 4)
>>> print(c)
[[[ 0  1  2  3]
  [ 4  5  6  7]
  [ 8  9 10 11]]

 [[12 13 14 15]
  [16 17 18 19]
  [20 21 22 23]]]
```

If an array is too large to be printed, NumPy automatically skips the central part of the array and only prints the corners:

```python
>>> print(np.arange(10000))
[   0    1    2 ... 9997 9998 9999]
>>> print(np.arange(10000).reshape(100, 100))
[[   0    1    2 ...   97   98   99]
 [ 100  101  102 ...  197  198  199]
 [ 200  201  202 ...  297  298  299]
 ...
 [9700 9701 9702 ... 9797 9798 9799]
 [9800 9801 9802 ... 9897 9898 9899]
 [9900 9901 9902 ... 9997 9998 9999]]
```

To disable this behaviour and force NumPy to print the entire array, you can change the printing options using `set_printoptions`.

```python
>>> np.set_printoptions(threshold=sys.maxsize)
```

## Basic operations

Arithmetic operators on arrays apply elementwise. A new array is created and filled with the result.

```python
>>> a = np.array([20, 30, 40, 50])
>>> b = np.arange(4)
>>> b
array([0, 1, 2, 3])
>>> c = a - b
>>> c
array([20, 29, 38, 47])
>>> b**2
array([0, 1, 4, 9])
>>> 10 * np.sin(a)
array([ 9.12945251, -9.88031624,  7.4511316 ,  2.62374854])
>>> a < 35
array([ True,  True, False, False])
```

Unlike in many matrix languages, the product operator `*` operates elementwise in NumPy arrays. The matrix product can be performed using the `@` operator (in python >=3.5) or the `dot` function or method:

```python
>>> A = np.array([[1, 1],
...               [0, 1]])
>>> B = np.array([[2, 0],
...               [3, 4]])
>>> A * B                       # elementwise product
array([[2, 0],
       [0, 4]])
>>> A @ B                       # matrix product
array([[5, 4],
       [3, 4]])
>>> A.dot(B)                    # another matrix product
array([[5, 4],
       [3, 4]])
```

Some operations, such as `+=` and `*=`, act in place to modify an existing array rather than create a new one.

```python
>>> rg = np.random.default_rng(1)
>>> a = np.ones((2, 3), dtype=int)
>>> b = rg.random((2, 3))
>>> a *= 3
>>> a
array([[3, 3, 3],
       [3, 3, 3]])
>>> b += a
>>> b
array([[3.51182162, 3.9504637 , 3.14415961],
       [3.94864945, 3.31183145, 3.42332645]])
>>> a += b                  # b is not automatically converted to integer type
Traceback (most recent call last):
  ...
TypeError: Cannot cast ufunc 'add' output from dtype('float64') to dtype('int64') with casting rule 'same_kind'
>>> a += b.astype(int)      # b is converted to integer type
>>> a
array([[6, 6, 6],
       [6, 6, 6]])
```

When operating with arrays of different types, the type of the resulting array corresponds to the more general or precise one (a behavior known as upcasting).

```python
>>> a = np.ones(3, dtype=np.int32)
>>> b = np.linspace(0, pi, 3)
>>> b.dtype.name
'float64'
>>> c = a + b
>>> c
array([1.        , 2.57079633, 4.14159265])
>>> c.dtype.name
'float64'
>>> d = np.exp(c * 1j)
>>> d
array([ 0.54030231+0.84147098j, -0.84147098+0.54030231j,
       -0.54030231-0.84147098j])
>>> d.dtype.name
'complex128'
```

## Unary operations

Many unary operations, such as computing the sum of all the elements in the array, are implemented as methods of the `ndarray` class.

```python
>>> a = rg.random((2, 3))
>>> a
array([[0.17000745, 0.43169294, 0.90910832],
       [0.26861514, 0.81606441, 0.91890553]])
>>> a.sum()
3.514393785734676
>>> a.min()
0.17000745143699082
>>> a.max()
0.918905527661021
```

By default, these operations apply to the array as though it were a list of numbers, regardless of its shape. However, by specifying the `axis` parameter you can apply an operation along the specified axis of an array:

```python
>>> b = np.arange(12).reshape(3, 4)
>>> b
array([[ 0,  1,  2,  3],
       [ 4,  5,  6,  7],
       [ 8,  9, 10, 11]])
>>> b.sum(axis=0)                            # sum of each column
array([12, 15, 18, 21])
>>> b.min(axis=1)                            # min of each row
array([0, 4, 8])
>>> b.cumsum(axis=1)                         # cumulative sum along each row
array([[ 0,  1,  3,  6],
       [ 4,  9, 15, 22],
       [ 8, 17, 27, 38]])
```

`ndarray.sum`, `ndarray.min`, `ndarray.max`, `ndarray.argmax`, `ndarray.cumsum`, `ndarray.mean`, `ndarray.std`

## Universal functions

NumPy provides familiar mathematical functions such as sin, cos, and exp. In NumPy, these are called “universal functions”(ufunc). Within NumPy, these functions operate elementwise on an array, producing an array as output.

```python
>>> B = np.arange(3)
>>> B
array([0, 1, 2])
>>> np.exp(B)
array([1.        , 2.71828183, 7.3890561 ])
>>> np.sqrt(B)
array([0.        , 1.        , 1.41421356])
>>> C = np.array([2., -1., 4.])
>>> np.add(B, C)
array([2., 0., 6.])
```

`ndarray.sum`, `ndarray.min`, `ndarray.max`, `ndarray.cumsum`, `ufunc.outer`, `ufunc.reduce`

## Indexing, slicing and iterating

One-dimensional arrays can be indexed, sliced and iterated over, much like lists and other Python sequences.

```python
>>> a = np.arange(10)**3
>>> a
array([  0,   1,   8,  27,  64, 125, 216, 343, 512, 729])
>>> a[2]
8
>>> a[2:5]
array([ 8, 27, 64])
>>> a[:6:2] = -1000                    # equivalent to a[0:6:2] = -1000
>>> a
array([-1000,     1,  -1000,    27,  -1000,   125,   216,   343,   512,   729])
>>> a[::-1]
array([  729,   512,   343,   216,   125,  -1000,    27,  -1000,     1,  -1000])
>>> for i in a:
...     print(i**(1 / 3.))
...
nan
1.0
nan
3.0
nan
5.0
6.0
7.0
8.0
9.

0
```

Multidimensional arrays can have one index per axis. These indices are given in a tuple separated by commas:

```python
>>> def f(x, y):
...     return 10 * x + y
...
>>> b = np.fromfunction(f, (5, 4), dtype=int)
>>> b
array([[ 0,  1,  2,  3],
       [10, 11, 12, 13],
       [20, 21, 22, 23],
       [30, 31, 32, 33],
       [40, 41, 42, 43]])
>>> b[2, 3]
23
>>> b[0:5, 1]                       # each row in the second column of b
array([ 1, 11, 21, 31, 41])
>>> b[:, 1]                         # equivalent to the previous example
array([ 1, 11, 21, 31, 41])
>>> b[1:3, :]
array([[10, 11, 12, 13],
       [20, 21, 22, 23]])
```

When fewer indices are provided than the number of axes, the missing indices are considered complete slices:

```python
>>> b[-1]
array([40, 41, 42, 43])
```

The expression within brackets in `b[i]` is treated as an i-th order polynomial on the index `i` with the variable replaced by `np.arange(len(b.shape))`:

```python
>>> b[i]
array([40, 41, 42, 43])
>>> i = np.array([1, 1])
>>> b[i]
array([10, 11])
```

It is possible to iterate over the elements of the array in two different ways:

1. By iterating over each element, one at a time:

```python
>>> for row in b:
...     print(row)
...
[ 0  1  2  3]
[10 11 12 13]
[20 21 22 23]
[30 31 32 33]
[40 41 42 43]
```

2. By iterating over the flattened version of the array, which is the array interpreted as a 1D array:

```python
>>> for element in b.flat:
...     print(element)
...
0
1
2
3
10
11
12
13
20
21
22
23
30
31
32
33
40
41
42
43
```

## Shape manipulation

An array has a shape given by the number of elements along each axis:

```python
>>> a = np.floor(10 * rg.random((3, 4)))
>>> a
array([[8., 8., 3., 1.],
       [5., 6., 4., 7.],
       [8., 1., 1., 0.]])
>>> a.shape
(3, 4)
```

The shape of an array can be changed with various commands. Note that the following three commands all return a modified array, but do not change the original array:

```python
>>> a.ravel()  # returns the array, flattened
array([8., 8., 3., 1., 5., 6., 4., 7., 8., 1., 1., 0.])
>>> a.reshape(6, 2)  # returns the array with a modified shape
array([[8., 8.],
       [3., 1.],
       [5., 6.],
       [4., 7.],
       [8., 1.],
       [1., 0.]])
>>> a.T  # returns the array, transposed
array([[8., 5., 8.],
       [8., 6., 1.],
       [3., 4., 1.],
       [1., 7., 0.]])
>>> a.T.shape
(4, 3)
>>> a.shape
(3, 4)
```

The `reshape` function is another way to modify the shape of an array:

```python
>>> a
array([[8., 8., 3., 1.],
       [5., 6., 4., 7.],
       [8., 1., 1., 0.]])
>>> a.reshape(2, 6)
array([[8., 8., 3., 1., 5., 6.],
       [4., 7., 8., 1., 1., 0.]])
```

The `resize` method modifies the array itself:

```python
>>> a.resize(2, 6)
>>> a
array([[8., 8., 3., 1., 5., 6.],
       [4., 7., 8., 1., 1., 0.]])
```

If a dimension is given as `-1` in a reshaping operation, the other dimensions are automatically calculated:

```python
>>> a.reshape(3, -1)
array([[8., 8., 3., 1.],
       [5., 6., 4., 7.],
       [8., 1., 1., 0.]])
```

`ndarray.shape`, `ndarray.reshape`, `ndarray.resize`, `ndarray.ravel`, `ndarray.T`

## Stacking together different arrays

Several arrays can be stacked together along different axes:

```python
>>> a = np.floor(10 * rg.random((2, 2)))
>>> a
array([[7., 9.],
       [1., 6.]])
>>> b = np.floor(10 * rg.random((2, 2)))
>>> b
array([[4., 8.],
       [7., 7.]])
>>> np.vstack((a, b))
array([[7., 9.],
       [1., 6.],
       [4., 8.],
       [7., 7.]])
>>> np.hstack((a, b))
array([[7., 9., 4., 8.],
       [1., 6., 7., 7.]])
```

The function `column_stack` stacks 1D arrays as columns into a 2D array. It is equivalent to `hstack` only for 2D arrays:

```python
>>> from numpy import newaxis
>>> np.column_stack((a, b))  # with 2D arrays
array([[7., 9., 4., 8.],
       [1., 6., 7., 7.]])
>>> a = np.array([4., 2.])
>>> b = np.array([3., 8.])
>>> np.column_stack((a, b))  # returns a 2D array
array([[4., 3.],
       [2., 8.]])
>>> np.hstack((a, b))  # the result is different
array([4., 2., 3., 8.])
>>> a[:, newaxis]  # this allows to have a 2D columns array
array([[4.],
       [2.]])
>>> np.column_stack((a[:, newaxis], b[:, newaxis]))
array([[4., 3.],
       [2., 8.]])
>>> np.hstack((a[:, newaxis], b[:, newaxis]))  # the result is the same
array([[4., 3.],
       [2., 8.]])
```

On the other hand, the function `row_stack` is equivalent to `vstack` for any input arrays. In general, for arrays with more than two dimensions, `hstack` stacks along their second axes, `vstack` stacks along their first axes, and `concatenate` allows for the stacking along arbitrary axes specified with the `axis` keyword.

In complex cases, `r_` and `c_` are useful for creating arrays by stacking numbers along one axis. They allow the use of array range notation (`a:b:c` syntax):

```python
>>> np.r_[1:4, 0, 4]
array([1, 2, 3, 0, 4])
>>> np.c_[np.array([1, 2, 3]), np.array([4, 5, 6])]
array([[1, 4],
       [2, 5],
       [3, 6]])
```

## Splitting one array into several smaller ones

Using `hsplit`, you can split an array along its horizontal axis by specifying either the number of equally shaped arrays to return, or the columns after which the division is to occur:

```python
>>> a = np.floor(10 * rg.random((2, 12)))
>>> a
array([[4., 7., 9., 5., 5., 0., 1., 5., 4., 1., 6., 4.],
       [7., 0., 3., 8., 0., 6., 3., 0., 1., 3., 5., 3.]])
>>> np.hsplit(a, 3)  # Split `a` into 3
[array([[4., 7., 9., 5.],
        [7., 0., 3., 8.]]), array([[5., 0., 1., 5.],
        [0., 6., 3., 0.]]), array([[4., 1., 6., 4.],


        [1., 3., 5., 3.]])]
>>> np.hsplit(a, (3, 4))  # Split `a` after the third and the fourth column
[array([[4., 7., 9.],
        [7., 0., 3.]]), array([[5.],
        [8.]]), array([[5., 0., 1., 5., 4., 1., 6., 4.],
        [0., 6., 3., 0., 1., 3., 5., 3.]])]
```

`vsplit` splits along the vertical axis, and `array_split` allows specifying the number of splits for uneven splits.

## Copies and views

When operating and manipulating arrays, their data is sometimes copied into a new array and sometimes not. This is often a source of confusion for beginners. There are three cases:

1. **No Copy at All**:

   Simple assignments do not copy array objects or their data.

```python
>>> a = np.arange(12)
>>> b = a  # `a` and `b` are two names for the same ndarray object
>>> b is a
True
>>> b.shape = 3, 4  # changes the shape of `a`
>>> a.shape
(3, 4)
```

2. **View or Shallow Copy**:

   Different array objects can share the same data. The view method creates a new array object that looks at the same data.

```python
>>> c = a.view()
>>> c is a
False
>>> c.base is a  # `c` is a view of the data owned by `a`
True
>>> c.flags.owndata
False
>>> c.shape = 2, 6  # changes the shape of `c`, not `a`
>>> a.shape
(3, 4)
>>> c[0, 4] = 1234  # modifies `a`'s data
>>> a
array([[   0,    1,    2,    3],
       [1234,    5,    6,    7],
       [   8,    9,   10,   11]])
```

Slicing an array returns a view of it.

```python
>>> s = a[:, 1:3]
>>> s[:] = 10  # `s` is a view of `a`
>>> a
array([[  0,  10,  10,   3],
       [1234,   10,   10,   7],
       [  8,   10,   10,  11]])
```

3. **Deep Copy**:

   The `copy` method makes a complete copy of the array and its data.

```python
>>> d = a.copy()  # a new array object with new data is created
>>> d is a
False
>>> d.base is a
False  # `d` does not share data with `a`
>>> d[0, 0] = 9999
>>> a
array([[   0,   10,   10,    3],
       [1234,   10,   10,    7],
       [   8,   10,   10,   11]])
```

`array.flags`

## Functions and Methods Overview


### Conversions
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `ndarray.astype`    | Converts the data type of an ndarray to another type.                         |
| `atleast_1d`        | Converts input to an array with at least one dimension.                       |
| `atleast_2d`        | Converts input to an array with at least two dimensions.                      |
| `atleast_3d`        | Converts input to an array with at least three dimensions.                    |
| `mat`               | Interprets the input as a matrix.                                             |

### Manipulations
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `array_split`       | Splits an array into multiple sub-arrays.                                     |
| `column_stack`      | Stacks 1D arrays as columns into a 2D array.                                  |
| `concatenate`       | Joins a sequence of arrays along an existing axis.                            |
| `diagonal`          | Returns specified diagonals.                                                  |
| `dsplit`            | Splits an array into multiple sub-arrays along the 3rd axis.                  |
| `dstack`            | Stacks arrays along the third axis.                                           |
| `hsplit`            | Splits an array into multiple sub-arrays horizontally (column-wise).          |
| `hstack`            | Stacks arrays horizontally (column-wise).                                     |
| `ndarray.item`      | Copy an element of an array to a standard Python scalar and return it.        |
| `newaxis`           | Used to increase the dimensions of the existing array by one more dimension.  |
| `ravel`             | Returns a contiguous flattened array.                                         |
| `repeat`            | Repeats elements of an array.                                                 |
| `reshape`           | Gives a new shape to an array without changing its data.                      |
| `resize`            | Changes the shape and size of an array.                                       |
| `squeeze`           | Removes single-dimensional entries from the shape of an array.                |
| `swapaxes`          | Interchanges two axes of an array.                                            |
| `take`              | Takes elements from an array along an axis.                                   |
| `transpose`         | Permutes the dimensions of an array.                                          |
| `vsplit`            | Splits an array into multiple sub-arrays vertically (row-wise).               |
| `vstack`            | Stacks arrays vertically (row-wise).                                          |

### Questions
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `all`               | Returns True if all elements evaluate to True.                                |
| `any`               | Returns True if any element evaluates to True.                                |
| `nonzero`           | Returns the indices of non-zero elements.                                     |
| `where`             | Returns elements chosen from `x` or `y` depending on condition.               |

### Ordering
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `argmax`            | Returns the indices of the maximum values along an axis.                      |
| `argmin`            | Returns the indices of the minimum values along an axis.                      |
| `argsort`           | Returns the indices that would sort an array.                                 |
| `max`               | Returns the maximum of an array or maximum along an axis.                     |
| `min`               | Returns the minimum of an array or minimum along an axis.                     |
| `ptp`               | Returns the range (maximum - minimum) of values along an axis.                |
| `searchsorted`      | Finds indices where elements should be inserted to maintain order.            |
| `sort`              | Returns a sorted copy of an array.                                            |

### Operations
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `choose`            | Constructs an array from an index array and a set of arrays to choose from.   |
| `compress`          | Returns selected slices of an array along a given axis.                       |
| `cumprod`           | Returns the cumulative product of elements along a given axis.                |
| `cumsum`            | Returns the cumulative sum of elements along a given axis.                    |
| `inner`             | Returns the inner product of two arrays.                                      |
| `ndarray.fill`      | Fills an array with a scalar value.                                           |
| `imag`              | Returns the imaginary part of the complex data type elements in an array.     |
| `prod`              | Returns the product of array elements over a given axis.                      |
| `put`               | Replaces specified elements of an array with given values.                    |
| `putmask`           | Changes elements of an array based on a condition.                            |
| `real`              | Returns the real part of the complex data type elements in an array.          |
| `sum`               | Returns the sum of array elements over a given axis.                          |

### Basic Statistics
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `cov`               | Estimates a covariance matrix.                                                |
| `mean`              | Returns the arithmetic mean of array elements.                                |
| `std`               | Returns the standard deviation of array elements.                             |
| `var`               | Returns the variance of array elements.                                       |

### Basic Linear Algebra
| **Function**        | **Description**                                                               |
|---------------------|-------------------------------------------------------------------------------|
| `cross`             | Returns the cross product of two vectors.                                     |
| `dot`               | Returns the dot product of two arrays.                                        |
| `outer`             | Returns the outer product of two vectors.                                     |
| `linalg.svd`        | Performs singular value decomposition.                                        |
| `vdot`              | Returns the dot product of two vectors.                                       |


### Array Creation
| **Function**             | **Description**                                                                      |
|--------------------------|--------------------------------------------------------------------------------------|
| `np.arange`              | Create an array with evenly spaced values within a given range                       |
| `np.array`               | Create an array from a list or tuple                                                 |
| `np.asanyarray`          | Convert the input to an array, but pass through arrays                               |
| `np.asarray`             | Convert the input to an array, but pass through arrays                               |
| `np.copy`                | Return an array copy of the input                                                    |
| `np.empty`               | Create a new array of given shape and type, without initializing entries             |
| `np.empty_like`          | Create a new array with the same shape and type as a given array, without initializing |
| `np.eye`                 | Create a 2D array with ones on the diagonal and zeros elsewhere                      |
| `np.identity`            | Create a square array with ones on the main diagonal                                 |
| `np.zeros`               | Create a new array of given shape and type, filled with zeros                        |
| `np.zeros_like`          | Create a new array with the same shape and type as a given array, filled with zeros  |
| `np.ones`                | Create a new array of given shape and type, filled with ones                         |
| `np.ones_like`           | Create a new array with the same shape and type as a given array, filled with ones   |
| `np.full`                | Create a new array of given shape and type, filled with a given value                |
| `np.full_like`           | Create a new array with the same shape and type as a given array, filled with a value|
| `np.linspace`            | Create an array with evenly spaced values within a given interval                    |
| `np.logspace`            | Create an array with evenly spaced values on a log scale                             |
| `np.geomspace`           | Create an array with evenly spaced values on a geometric progression                 |
| `np.meshgrid`            | Create coordinate matrices from coordinate vectors                                   |
| `np.mgrid`               | Create a dense multi-dimensional "meshgrid"                                          |
| `np.ogrid`               | Create an open multi-dimensional "meshgrid"                                          |
| `np.indices`             | Create an array representing the indices of a grid                                   |
| `np.fromfunction`        | Create an array by executing a function over each coordinate                         |
| `np.fromfile`            | Create an array from data in a binary file                                           |
| `np.frombuffer`          | Create an array from data in a buffer                                                |
| `np.fromstring`          | Create an array from data in a string                                                |
| `np.loadtxt`             | Load data from a text file                                                           |
| `np.genfromtxt`          | Load data from a text file, with missing values handled                              |
| `np.savetxt`             | Save an array to a text file                                                         |
| `np.load`                | Load arrays or pickled objects from .npy or .npz files                               |
| `np.save`                | Save an array to a binary file in .npy format                                        |
| `np.savez`               | Save multiple arrays to a compressed .npz file                                       |
| `np.savez_compressed`    | Save multiple arrays to a compressed .npz file                                       |


## Less Basic

### Broadcasting rules

Broadcasting is a powerful mechanism that allows numpy to work with arrays of different shapes when performing arithmetic operations. 

The term broadcasting describes how numpy treats arrays with different shapes during arithmetic operations. Subject to certain constraints, the smaller array is “broadcast” across the larger array so that they have compatible shapes. 

The rules for broadcasting are:

1. If the arrays do not have the same rank, prepend the shape of the lower-rank array with ones until both shapes have the same length.
2. The sizes of the arrays along each dimension must be either the same or one of them must be 1.

### More functions

NumPy provides many useful functions for performing computations on arrays; one of the most useful is `sum`:

```python
>>> a = np.array([0, 1, 2, 3])
>>> a
array([0, 1, 2, 3])
>>> a.sum()
6
>>> a.sum(axis=0)  # sum of each column
6
```

By default, `sum` computes the sum of all the elements in the array. When the keyword argument `axis` is provided, an operation is performed along the given axis.

Other useful functions include `min`, `max`, `cumsum`, and `mean`:

```python
>>> b = np.array([[0, 1, 2], [3, 4, 5], [6, 7, 8]])
>>> b
array([[0, 1, 2],
       [3, 4, 5],
       [6, 7, 8]])
>>> b.sum(axis=0)  # sum of each column
array([ 9, 12, 15])
>>> b.min(axis=1)  # min of each row
array([0, 3, 6])
>>> b.cumsum(axis=1)  # cumulative sum along each row
array([[ 0,  1,  3],
       [ 3,  7, 12],
       [ 6, 13, 21]])
```

### Universal functions

NumPy provides familiar mathematical functions such as sin, cos, and exp. In NumPy, these are called “universal functions” (ufunc). Within NumPy, these functions operate elementwise on an array, producing an array as output.

```python
>>> B = np.arange(3)
>>> B
array([0, 1, 2])
>>> np.exp(B)
array([ 1.        ,  2.71828183,  7.3890561 ])
>>> np.sqrt(B)
array([0.        , 1.        , 1.41421356])
```

### Indexing with arrays of indices

Indexing arrays with other arrays of integer indices is a powerful feature provided by NumPy. Here is an example:

```python
>>> a = np.arange(12)**2  # the first 12 square numbers
>>> i = np.array([1, 1, 3, 8, 5])  # an array of indices
>>> a[i]  # the elements at these positions
array([ 1,  1,  9, 64, 25])
>>> j = np.array([[3, 4], [9, 7]])  # a 2d array of indices
>>> a[j]  # the same shape as j
array([[ 9, 16],
       [81, 49]])
```

### Indexing with boolean arrays

When we index arrays with other arrays of boolean values (True/False) of the same shape, we get an array of elements corresponding to the True values.

```python
>>> a = np.arange(12).reshape(3, 4)
>>> b = a > 4  # boolean array
>>> b
array([[False, False, False, False],
       [ True,  True,  True,  True],
       [ True,  True,  True,  True]])
>>> a[b]  # 1d array with the selected elements
array([ 5,  6,  7,  8,  9, 10, 11])
```

This feature is useful for filtering data and setting certain elements to new values:

```python
>>> a[b] = 0  # All elements of 'a' higher than 4 become 0
>>> a
array([[0, 1, 2, 3],
       [4, 0, 0, 0],
       [0, 0, 0, 0]])
```

### ix_() function

The `ix_` function can be used to combine different vectors so as to obtain the result for each pair of vectors:

```python
>>> a = np.array([2, 3, 4, 5])
>>> b = np.array([8, 5, 4])
>>> c = np.array([5, 4, 6, 8, 3])
>>> ax, bx, cx = np.ix_(a, b, c)
>>> ax
array([[[2]],

       [[3]],

       [[4]],

       [[5]]])
>>> bx
array([[[8],
        [5],
        [4]]])
>>> cx
array([[[5, 4, 6, 8, 3]]])
>>> ax.shape, bx.shape, cx.shape
((4, 1, 1), (1, 3, 1), (1, 1, 5))
>>> result = ax + bx * cx
>>> result
array([[[42, 34, 50, 66, 26],
        [27, 22, 34, 46, 17],
        [22, 18, 26, 34, 14]],

       [[43, 35, 51, 67, 27],
        [28, 23, 35, 47, 18],
        [23, 19, 27, 35, 15]],

       [[44, 36, 52, 68, 28],
        [29, 24, 36, 48, 19],
        [24, 20, 28, 36, 16]],

       [[45, 37, 53, 69, 29],
        [30, 25, 37, 49, 20],
        [25, 21, 29, 37, 17]]])
>>> result[3, 2, 4]
17
>>> a[3] + b[2] * c[4]
17
```

### Linear algebra

The `dot` function performs matrix multiplication:

```python
>>> A = np.array([[1, 1],
...               [0, 1]])
>>> B = np.array([[2, 0],
...               [3, 4]])
>>> np.dot(A, B)
array([[5, 4],
       [3, 4]])
```

The `dot` function works on higher-dimensional arrays as well:

```python
>>> A = np.arange(12).reshape(3, 4)
>>> B = np.arange(12, 24).reshape(4, 3)
>>> np.dot(A, B)
array([[ 84,  90,  96],
       [240, 262, 284],
       [396, 434, 472]])
```

The `@` operator can be used as a shorthand for the `dot` function:

```python
>>> A @ B
array([[ 84,  90,  96],
       [240, 262, 284],
       [396, 434, 472]])
```

Other useful functions in the numpy.linalg module include `inv`, `eig`, `svd`, `solve`, and `det`:

```python
>>> from numpy.linalg import inv, qr
>>> A = np.array([[1., 2.], [3., 4.]])
>>> inv(A)
array([[-2. ,  1. ],
       [ 1.5, -0.5]])
>>> q, r = qr(A)
>>> q
array([[-0.31622777, -0.9486833 ],
       [-0.9486833 ,  0.31622777]])
>>> r
array([[-3.16227766, -4.42718872],
       [ 0.        , -0.63245553]])
```

### Random

The `numpy.random` module provides functions for generating random numbers. Some examples include `rand`, `randn`, and `randint`:

```python
>>> np.random.rand(3, 2)
array([[0.14022471, 0.96360618],
       [0.37601032, 0.25528411],
       [0.49313049, 0.94909878]])
>>> np.random.randn(3, 2)
array([[ 0.2676809 , -1.17454617],
       [ 1.40004307, -1.20218426],
       [ 0.14979907, -0.10778965]])
>>> np.random.randint(1, 10, size=(3, 2))
array([[5, 9],
       [5, 1],
       [1, 9]])
```

The `seed` function sets the random seed for reproducibility:

```python
>>> np.random.seed(0)
>>> np.random.rand(3, 2)
array([[0.5488135 , 0.71518937],
      

 [0.60276338, 0.54488318],
       [0.4236548 , 0.64589411]])
```

### Tricks and Tips

- **Large Arrays**: NumPy arrays can be very large. If an operation seems to take too long, check if you can use in-place operations with `+=`, `*=` and `**=`.
- **Vectorize Functions**: If you have written a function that operates on scalars, you can convert it to a function that operates on arrays by using `numpy.vectorize`.
- **Read Documentation**: Always refer to the [NumPy Documentation](https://numpy.org/doc/stable/) for comprehensive details on functions and their parameters.


