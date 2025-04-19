## Importing Data with `genfromtxt`

### Overview of `genfromtxt`

- **Functionality**: `genfromtxt` in NumPy is used to create arrays from tabular data.
- **Process**:
  1. **First Loop**: Converts each line of the file into a sequence of strings.
  2. **Second Loop**: Converts each string to the appropriate data type.
- **Flexibility**: Handles missing data, unlike `loadtxt`.

### Defining the Input

- **Mandatory Argument**: The source of the data which can be:
  - A string (local/remote file name).
  - A list of strings.
  - A generator returning strings.
  - An open file-like object with a `read` method.
- **File Types**:
  - Text files.
  - Archives (gzip `.gz`, bzip2 `.bz2`).

### Splitting Lines into Columns

#### The `delimiter` Argument

- **Purpose**: Defines how to split lines into columns.
- **Common Delimiters**:
  - Comma `,` (CSV files).
  - Semicolon `;`.
  - Tab `\t`.
  - Any string (default is whitespace).
- **Fixed-Width Files**:
  - Single integer for uniform column width.
  - Sequence of integers for varying column widths.

**Examples**:

```python
data = "1, 2, 3\n4, 5, 6"
np.genfromtxt(StringIO(data), delimiter=",")
# Output: array([[1.,  2.,  3.], [4.,  5.,  6.]])

data = "  1  2  3\n  4  5 67\n890123  4"
np.genfromtxt(StringIO(data), delimiter=3)
# Output: array([[  1.,    2.,    3.], [  4.,    5.,   67.], [890.,  123.,    4.]])
```

#### The `autostrip` Argument

- **Purpose**: Strips leading/trailing whitespace from entries when set to `True`.
- **Example**:

```python
data = "1, abc , 2\n 3, xxx, 4"
np.genfromtxt(StringIO(data), delimiter=",", dtype="|U5", autostrip=True)
# Output: array([['1', 'abc', '2'], ['3', 'xxx', '4']], dtype='<U5')
```

#### The `comments` Argument

- **Purpose**: Defines the start of comments in the data.
- **Default**: `#`
- **Behavior**: Ignores characters after the comment marker.
- **Example**:

```python
data = """#
# Skip me !
# Skip me too !
1, 2
3, 4
5, 6 #This is the third line of the data
7, 8
# And here comes the last line
9, 0
"""
np.genfromtxt(StringIO(data), comments="#", delimiter=",")
# Output: array([[1., 2.], [3., 4.], [5., 6.], [7., 8.], [9., 0.]])
```

### Skipping Lines and Choosing Columns

#### The `skip_header` and `skip_footer` Arguments

- **Purpose**: Skip lines at the beginning (`skip_header`) and end (`skip_footer`) of the file.
- **Example**:

```python
data = "\n".join(str(i) for i in range(10))
np.genfromtxt(StringIO(data), skip_header=3, skip_footer=5)
# Output: array([3.,  4.])
```

#### The `usecols` Argument

- **Purpose**: Select specific columns to import.
- **Accepts**: Single integer, sequence of integers, or sequence of column names.
- **Example**:

```python
data = "1 2 3\n4 5 6"
np.genfromtxt(StringIO(data), usecols=(0, -1))
# Output: array([[1.,  3.], [4.,  6.]])
```

### Choosing the Data Type

#### The `dtype` Argument

- **Purpose**: Control conversion of strings to other types.
- **Acceptable Values**:
  - Single type (e.g., `dtype=float`).
  - Sequence of types (e.g., `dtype=(int, float, float)`).
  - Comma-separated string (e.g., `dtype="i4,f8,|U3"`).
  - Dictionary with 'names' and 'formats'.
  - Sequence of tuples (name, type).
  - Existing `numpy.dtype` object.
  - `None` (types determined from data).

### Setting the Names

#### The `names` Argument

- **Purpose**: Assign names to columns.
- **Options**:
  - Explicit structured `dtype`.
  - Sequence of strings or comma-separated string.
  - `True` to read names from the first line after `skip_header`.
- **Examples**:

```python
data = StringIO("1 2 3\n 4 5 6")
np.genfromtxt(data, names="A, B, C")
# Output: array([(1., 2., 3.), (4., 5., 6.)], dtype=[('A', '<f8'), ('B', '<f8'), ('C', '<f8')])

data = StringIO("So it goes\n#a b c\n1 2 3\n 4 5 6")
np.genfromtxt(data, skip_header=1, names=True)
# Output: array([(1., 2., 3.), (4., 5., 6.)], dtype=[('a', '<f8'), ('b', '<f8'), ('c', '<f8')])
```

#### The `defaultfmt` Argument

- **Purpose**: Define default names if not enough names are provided.
- **Example**:

```python
data = StringIO("1 2 3\n 4 5 6")
np.genfromtxt(data, dtype=(int, float, int), defaultfmt="var_%02i")
# Output: array([(1, 2., 3), (4, 5., 6)], dtype=[('var_00', '<i8'), ('var_01', '<f8'), ('var_02', '<i8')])
```

#### Validating Names

- **Arguments**:
  - `deletechars`: Characters to delete from names (default: `~!@#$%^&*()-=+~\|]}[{';: /?.>,<`).
  - `excludelist`: List of names to exclude (e.g., `return`, `file`, `print`).
  - `case_sensitive`: Case sensitivity (`True`, `False`, `upper`, `lower`).

### Tweaking the Conversion

#### The `converters` Argument

- **Purpose**: Provide custom conversion functions.
- **Usage**: Dictionary with column indices/names as keys and conversion functions as values.
- **Examples**:

```python
convertfunc = lambda x: float(x.strip("%"))/100.
data = "1, 2.3%, 45.\n6, 78.9%, 0"
names = ("i", "p", "n")
np.genfromtxt(StringIO(data), delimiter=",", names=names, converters={1: convertfunc})
# Output: array([(1., 0.023, 45.), (6., 0.789, 0.)], dtype=[('i', '<f8'), ('p', '<f8'), ('n', '<f8')])
```

### Handling Missing and Filling Values

#### The `missing_values` Argument

- **Purpose**: Recognize missing data.
- **Accepts**:
  - Single string/comma-separated string.
  - Sequence of strings.
  - Dictionary with column indices/names or `None` for all columns.

#### The `filling_values` Argument

- **Purpose**: Provide values for missing entries.
- **Defaults**:
  - `bool`: `False`
  - `int`: `-1`
  - `float`: `np.nan`
  - `complex`: `np.nan+0j`
  - `string`: `'???'`
- **Accepts**:
  - Single value for all columns.
  - Sequence of values for corresponding columns.
  - Dictionary with column indices/names or `None` for all columns.

**Example**:

```python
data = "N/A, 2, 3\n4, ,???"
kwargs = dict(delimiter=",", dtype=int, names="a,b,c", missing_values={0:"N/A", 'b':" ", 2:"???"}, filling_values={0:0, 'b':0, 2:-999})
np.genfromtxt(StringIO(data), **kwargs)
# Output: array([(0, 2, 3), (4, 0, -999)], dtype=[('a', '<i8'), ('b', '<i8'), ('c', '<i8')])
```

#### The `usemask` Argument

- **Purpose**: Create a boolean mask to track missing data.
- **Default**: `False`
- **Example**:

```python
data = "1, 2\n3, "
np.genfromtxt(StringIO(data), delimiter=",", usemask=True)
# Output: MaskedArray(data=[[1.0

, 2.0], [3.0, --]], mask=[[False, False], [False, True]], fill_value=1e+20)
```

### Processing the Results

#### The `unpack` Argument

- **Purpose**: Split output array into separate variables (columns).
- **Example**:

```python
data = "1 2 3\n4 5 6"
x, y, z = np.genfromtxt(StringIO(data), unpack=True)
# x = array([ 1.,  4.])
# y = array([ 2.,  5.])
# z = array([ 3.,  6.])
```

#### The `max_rows` Argument

- **Purpose**: Limit the number of lines read.
- **Example**:

```python
data = "1 2\n3 4\n5 6"
np.genfromtxt(StringIO(data), max_rows=2)
# Output: array([[ 1.,  2.], [ 3.,  4.]])
```
