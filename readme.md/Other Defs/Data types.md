# Data Types

## See Also

- **Data type objects**
- **Array types and conversions between types**

NumPy supports a much greater variety of numerical types than Python does. This section shows which are available, and how to modify an array’s data-type.

NumPy numerical types are instances of `numpy.dtype` (data-type) objects, each having unique characteristics. Once you have imported NumPy using `import numpy as np`, you can create arrays with a specified `dtype` using the scalar types in the NumPy top-level API, e.g., `numpy.bool`, `numpy.float32`, etc.

These scalar types can be used as arguments to the `dtype` keyword that many NumPy functions or methods accept. For example:

```python
z = np.arange(3, dtype=np.uint8)
z
# Output: array([0, 1, 2], dtype=uint8)
```

Array types can also be referred to by character codes, for example:

```python
np.array([1, 2, 3], dtype='f')
# Output: array([1.,  2.,  3.], dtype=float32)

np.array([1, 2, 3], dtype='d')
# Output: array([1.,  2.,  3.], dtype=float64)
```

See [Specifying and constructing data types](https://numpy.org/doc/stable/user/basics.types.html#specifying-and-constructing-data-types) for more information about specifying and constructing data type objects, including how to specify parameters like the byte order.

To convert the type of an array, use the `.astype()` method. For example:

```python
z.astype(np.float64)
# Output: array([0.,  1.,  2.])
```

Note that, above, we could have used the Python `float` object as a `dtype` instead of `numpy.float64`. NumPy knows that `int` refers to `numpy.int_`, `bool` means `numpy.bool`, `float` is `numpy.float64`, and `complex` is `numpy.complex128`. The other data-types do not have Python equivalents.

To determine the type of an array, look at the `dtype` attribute:

```python
z.dtype
# Output: dtype('uint8')
```

`dtype` objects also contain information about the type, such as its bit-width and its byte-order. The data type can also be used indirectly to query properties of the type, such as whether it is an integer:

```python
d = np.dtype('int64')
d
# Output: dtype('int64')

np.issubdtype(d, np.integer)
# Output: True

np.issubdtype(d, np.floating)
# Output: False
```

## Numerical Data Types

There are 5 basic numerical types representing booleans (`bool`), integers (`int`), unsigned integers (`uint`), floating point (`float`), and complex. A basic numerical type name combined with a numeric bitsize defines a concrete type. The bitsize is the number of bits needed to represent a single value in memory. For example, `numpy.float64` is a 64-bit floating point data type. Some types, such as `numpy.int_` and `numpy.intp`, have differing bitsizes dependent on the platforms (e.g., 32-bit vs. 64-bit CPU architectures). This should be taken into account when interfacing with low-level code (such as C or Fortran) where the raw memory is addressed.

## Data Types for Strings and Bytes

In addition to numerical types, NumPy also supports storing Unicode strings via the `numpy.str_` dtype (U character code), null-terminated byte sequences via `numpy.bytes_` (S character code), and arbitrary byte sequences via `numpy.void` (V character code).

All of the above are fixed-width data types. They are parameterized by a width, in either bytes or Unicode points, that a single data element in the array must fit inside. This means that storing an array of byte sequences or strings using this dtype requires knowing or calculating the sizes of the longest text or byte sequence in advance.

As an example, we can create an array storing the words "hello" and "world!":

```python
np.array(["hello", "world!"])
# Output: array(['hello', 'world!'], dtype='<U6')
```

Here the data type is detected as a Unicode string that is a maximum of 6 code points long, enough to store both entries without truncation. If we specify a shorter or longer data type, the string is either truncated or zero-padded to fit in the specified width:

```python
np.array(["hello", "world!"], dtype="U5")
# Output: array(['hello', 'world'], dtype='<U5')

np.array(["hello", "world!"], dtype="U7")
# Output: array(['hello', 'world!'], dtype='<U7')
```

We can see the zero-padding more clearly if we use the bytes data type and ask NumPy to print out the bytes in the array buffer:

```python
np.array(["hello", "world"], dtype="S7").tobytes()
# Output: b'hello\x00\x00world\x00\x00'
```

Each entry is padded with two extra null bytes. Note, however, that NumPy cannot tell the difference between intentionally stored trailing nulls and padding nulls:

```python
x = [b"hello\0\0", b"world"]
a = np.array(x, dtype="S7")
print(a[0])
# Output: b'hello'

a[0] == x[0]
# Output: False
```

If you need to store and round-trip any trailing null bytes, you will need to use an unstructured void data type:

```python
a = np.array(x, dtype="V7")
a
# Output: array([b'\x68\x65\x6C\x6C\x6F\x00\x00', b'\x77\x6F\x72\x6C\x64\x00\x00'], dtype='|V7')

a[0] == np.void(x[0])
# Output: True
```

Advanced types, not listed above, are explored in section [Structured arrays](https://numpy.org/doc/stable/user/basics.rec.html).

## Relationship Between NumPy Data Types and C Data Types

NumPy provides both bit-sized type names and names based on the names of C types. Since the definition of C types is platform dependent, explicitly bit-sized types should be preferred to avoid platform-dependent behavior in programs using NumPy.

To ease integration with C code, where it is more natural to refer to platform-dependent C types, NumPy also provides type aliases that correspond to the C types for the platform. Some dtypes have a trailing underscore to avoid confusion with built-in Python type names, such as `numpy.bool_`.

| Canonical Python API name | Python API “C-like” name | Actual C type                | Description                                              |
|---------------------------|--------------------------|------------------------------|----------------------------------------------------------|
| `numpy.bool` or `numpy.bool_` | N/A                      | `bool` (defined in stdbool.h) | Boolean (True or False) stored as a byte.                |
| `numpy.int8`              | `numpy.byte`             | `signed char`                | Platform-defined integer type with 8 bits.               |
| `numpy.uint8`             | `numpy.ubyte`            | `unsigned char`              | Platform-defined integer type with 8 bits without sign.  |
| `numpy.int16`             | `numpy.short`            | `short`                      | Platform-defined integer type with 16 bits.              |
| `numpy.uint16`            | `numpy.ushort`           | `unsigned short`             | Platform-defined integer type with 16 bits without sign. |
| `numpy.int32`             | `numpy.intc`             | `int`                        | Platform-defined integer type with 32 bits.              |
| `numpy.uint32`            | `numpy.uintc`            | `unsigned int`               | Platform-defined integer type with 32 bits without sign. |
| `numpy.intp`              | N/A                      | `ssize_t/Py_ssize_t`         | Platform-defined integer of size `size_t`; used e.g., for sizes. |
| `numpy.uintp`             | N/A                      | `size_t`                     | Platform-defined integer type capable of storing the maximum allocation size. |
| N/A                       | `'p'`                    | `intptr_t`                   | Guaranteed to hold pointers. Character code only (Python and C). |
| N/A                       | `'P'`                    | `uintptr_t`                  | Guaranteed to hold pointers. Character code only (Python and C). |
| `numpy.int32` or `numpy.int64` | `numpy.long`            | `long`                       | Platform-defined integer type with at least 32 bits.     |
| `numpy.uint32` or `numpy.uint64` | `numpy.ulong`           | `unsigned long`              | Platform-defined integer type with at least 32 bits without sign. |
| N/A                       | `numpy.longlong`         | `long long`                  | Platform-defined integer type with at least 64 bits.     |
| N/A                       | `numpy.ulonglong`        | `unsigned long long`         | Platform-defined integer type with at least 64 bits without sign. |
| `numpy.float16`           | `numpy.half`             | N/A                          | Half precision float: sign bit, 5 bits exponent, 10 bits mantissa. |
| `numpy.float

32`           | `numpy.single`           | `float`                      | Platform-defined single precision float: typically sign bit, 8 bits exponent, 23 bits mantissa. |
| `numpy.float64`           | `numpy.double`           | `double`                     | Platform-defined double precision float: typically sign bit, 11 bits exponent, 52 bits mantissa. |
| `numpy.float96` or `numpy.float128` | `numpy.longdouble`      | `long double`                | Platform-defined extended-precision float.               |
| `numpy.complex64`         | `numpy.csingle`          | `float complex`              | Complex number, represented by two single-precision floats (real and imaginary components). |
| `numpy.complex128`        | `numpy.cdouble`          | `double complex`             | Complex number, represented by two double-precision floats (real and imaginary components). |
| `numpy.complex192` or `numpy.complex256` | `numpy.clongdouble`     | `long double complex`        | Complex number, represented by two extended-precision floats (real and imaginary components). |

Since many of these have platform-dependent definitions, a set of fixed-size aliases are provided (See [Sized aliases](https://numpy.org/doc/stable/reference/arrays.scalars.html#built-in-scalar-types)).

## Array Scalars

NumPy generally returns elements of arrays as array scalars (a scalar with an associated dtype). Array scalars differ from Python scalars, but for the most part they can be used interchangeably (the primary exception is for versions of Python older than v2.x, where integer array scalars cannot act as indices for lists and tuples). There are some exceptions, such as when code requires very specific attributes of a scalar or when it checks specifically whether a value is a Python scalar. Generally, problems are easily fixed by explicitly converting array scalars to Python scalars, using the corresponding Python type function (e.g., `int`, `float`, `complex`, `str`).

The primary advantage of using array scalars is that they preserve the array type (Python may not have a matching scalar type available, e.g., `int16`). Therefore, the use of array scalars ensures identical behavior between arrays and scalars, irrespective of whether the value is inside an array or not. NumPy scalars also have many of the same methods arrays do.

## Overflow Errors

The fixed size of NumPy numeric types may cause overflow errors when a value requires more memory than available in the data type. For example, `numpy.power` evaluates `100 ** 9` correctly for 64-bit integers, but gives `-1486618624` (incorrect) for a 32-bit integer.

```python
np.power(100, 9, dtype=np.int64)
# Output: 1000000000000000000

np.power(100, 9, dtype=np.int32)
# Output: -1486618624
```

The behavior of NumPy and Python integer types differs significantly for integer overflows and may confuse users expecting NumPy integers to behave similarly to Python’s `int`. Unlike NumPy, the size of Python’s `int` is flexible. This means Python integers may expand to accommodate any integer and will not overflow.

NumPy provides `numpy.iinfo` and `numpy.finfo` to verify the minimum or maximum values of NumPy integer and floating point values respectively:

```python
np.iinfo(int) # Bounds of the default integer on this system.
# Output: iinfo(min=-9223372036854775808, max=9223372036854775807, dtype=int64)

np.iinfo(np.int32) # Bounds of a 32-bit integer
# Output: iinfo(min=-2147483648, max=2147483647, dtype=int32)

np.iinfo(np.int64) # Bounds of a 64-bit integer
# Output: iinfo(min=-9223372036854775808, max=9223372036854775807, dtype=int64)
```

If 64-bit integers are still too small, the result may be cast to a floating point number. Floating point numbers offer a larger, but inexact, range of possible values.

```python
np.power(100, 100, dtype=np.int64) # Incorrect even with 64-bit int
# Output: 0

np.power(100, 100, dtype=np.float64)
# Output: 1e+200
```

## Extended Precision

Python’s floating-point numbers are usually 64-bit floating-point numbers, nearly equivalent to `numpy.float64`. In some unusual situations, it may be useful to use floating-point numbers with more precision. Whether this is possible in NumPy depends on the hardware and on the development environment: specifically, x86 machines provide hardware floating-point with 80-bit precision, and while most C compilers provide this as their `long double` type, MSVC (standard for Windows builds) makes `long double` identical to `double` (64 bits). NumPy makes the compiler’s `long double` available as `numpy.longdouble` (and `np.clongdouble` for the complex numbers). You can find out what your NumPy provides with `np.finfo(np.longdouble)`.

NumPy does not provide a dtype with more precision than C’s `long double`; in particular, the 128-bit IEEE quad precision data type (FORTRAN’s `REAL*16`) is not available.

For efficient memory alignment, `numpy.longdouble` is usually stored padded with zero bits, either to 96 or 128 bits. Which is more efficient depends on hardware and development environment; typically on 32-bit systems they are padded to 96 bits, while on 64-bit systems they are typically padded to 128 bits. `np.longdouble` is padded to the system default; `np.float96` and `np.float128` are provided for users who want specific padding. In spite of the names, `np.float96` and `np.float128` provide only as much precision as `np.longdouble`, that is, 80 bits on most x86 machines and 64 bits in standard Windows builds.

Be warned that even if `numpy.longdouble` offers more precision than Python `float`, it is easy to lose that extra precision, since Python often forces values to pass through `float`. For example, the `%` formatting operator requires its arguments to be converted to standard Python types, and it is therefore impossible to preserve extended precision even if many decimal places are requested. It can be useful to test your code with the value `1 + np.finfo(np.longdouble).eps`.

```markdown
Here is the continuation of the documentation on NumPy data types:

```markdown
## Structured Arrays

NumPy’s `ndarray` allows one to specify structured data types. Structured data types are meant for interpreting arrays of bytes as numbers with specific sizes and interpretation. Structured data types are a sub-class of `numpy.void`, which can contain fields, analogous to C structures.

A structured array can be created using the `dtype` keyword. The dtype can be specified using a dictionary with field names and corresponding data types. For example:

```python
dt = np.dtype([('field1', np.int32), ('field2', np.float32)])
x = np.array([(1, 2.0), (3, 4.0)], dtype=dt)
print(x['field1'])
# Output: [1 3]

print(x['field2'])
# Output: [2. 4.]
```

The `dtype` can also be specified using a string. For example, the above dtype can be written as:

```python
dt = np.dtype("i4, f4")
```

Structured data types can be nested and can include padding bytes which can be useful for interfacing with C code. For example:

```python
dt = np.dtype([('a', np.int32), ('b', np.int32, 3), ('c', np.float32)])
print(dt)
# Output: dtype([('a', '<i4'), ('b', '<i4', (3,)), ('c', '<f4')])

x = np.array([(1, [2, 3, 4], 5.0)], dtype=dt)
print(x['a'])
# Output: [1]

print(x['b'])
# Output: [[2 3 4]]

print(x['c'])
# Output: [5.]
```

## Record Arrays

Record arrays are structured arrays with field access by attribute rather than by index. They can be accessed using the `np.recarray` class. For example:

```python
x = np.array([(1, 2.0), (3, 4.0)], dtype=dt).view(np.recarray)
print(x.field1)
# Output: [1 3]

print(x.field2)
# Output: [2. 4.]
```

Record arrays are convenient but may be slower than normal structured arrays due to attribute access overhead.

## Custom Data Types

It is also possible to define custom data types using sub-classing. For example:

```python
class CustomDtype(np.generic):
    def __new__(cls, input_array, info=None):
        obj = np.asarray(input_array).view(cls)
        obj.info = info
        return obj

x = np.array([1, 2, 3], dtype=CustomDtype)
print(x)
# Output: [1 2 3]

print(type(x[0]))
# Output: <class '__main__.CustomDtype'>
```

## Data Type Conversion

NumPy provides functions to convert between data types. The most common function is `astype()`, which is used to cast an array to a different data type. For example:

```python
x = np.array([1.1, 2.2, 3.3])
y = x.astype(np.int32)
print(y)
# Output: [1 2 3]
```

It is also possible to specify the casting behavior using the `casting` keyword. The options are 'no', 'equiv', 'safe', 'same_kind', and 'unsafe'. For example:

```python
x = np.array([1.1, 2.2, 3.3])
y = x.astype(np.int32, casting='safe')
print(y)
# Output: [1 2 3]
```

If the `casting` keyword is set to 'no', an error is raised if a cast would change any of the data:

```python
try:
    y = x.astype(np.int32, casting='no')
except TypeError as e:
    print(e)
# Output: Cannot cast array data from dtype('float64') to dtype('int32') according to the rule 'no'
```

## Conclusion

Understanding NumPy data types is crucial for efficient numerical computations. Proper use of data types ensures that operations are performed with the desired precision and efficiency. This document covered the basics of NumPy data types, including numerical types, string and byte types, structured arrays, record arrays, custom data types, and data type conversion. For more advanced topics, refer to the official NumPy documentation.
