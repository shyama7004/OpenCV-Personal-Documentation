### CMake Chapter 8: Functions and Macros 

---

### **1. Introduction to Functions and Macros in CMake**

Functions and macros in CMake are powerful tools to encapsulate reusable code. They are similar but differ in their scope behavior:

- **Functions**: Introduce a new scope for variables (similar to functions in programming languages like C/C++).
- **Macros**: Do not introduce a new scope and behave more like text substitution macros in C/C++.

Using functions and macros allows developers to write cleaner, more maintainable CMake scripts by avoiding repetition. Both can take arguments and manipulate them.

---

### **2. Syntax and Usage**

#### **Defining Functions**
Functions in CMake allow you to encapsulate repetitive logic and create reusable blocks of code. Variables inside functions are scoped locally.

```cmake
function(function_name [arg1 arg2 ...])
#Function body
endfunction()
```

Example:
```cmake
function(print_message)
  message("This is a function")
endfunction()

print_message()
```

#### **Defining Macros**
Macros in CMake are similar to functions but without local scoping. Variables modified inside a macro affect the calling scope.

```cmake
macro(macro_name [arg1 arg2 ...])
#Macro body
endmacro()
```

Example:
```cmake
macro(print_message_macro)
  message("This is a macro")
endmacro()

print_message_macro()
```

**Key Difference**: Functions introduce local variable scope, while macros operate within the same scope as the caller.

<details>
  <summary>More about function v/s macros </summary>

  Yes, you can think of functions and macros in these terms, but it's important to understand the differences and nuances more precisely.

### Functions
- **Local Scope:** Functions indeed provide local scope. Variables declared within a function are local to that function and cannot be accessed outside of it.
- **Runtime:** Functions are evaluated at runtime. When a function is called, control transfers to the function code, which executes and then returns control back to the calling location.
- **Type Safety:** Functions provide type checking at compile time (for statically typed languages like C++), which helps catch errors.
- **Reusability:** Functions promote code reusability and modularity, making the code easier to understand and maintain.

### Macros
- **No Local Scope:** Macros, on the other hand, do not provide a local scope in the traditional sense. Macros are essentially text replacements performed by the preprocessor before compilation.
- **Preprocessing:** Macros are expanded by the preprocessor before the actual compilation begins. This means they are substituted directly into the code wherever they are used, which can lead to potential issues if not used carefully.
- **No Type Checking:** Since macros are just textual replacements, they do not offer type checking, which can lead to subtle bugs.
- **Performance:** Macros can be faster than functions because they avoid the overhead of a function call. However, this can make the code harder to debug and maintain.
- **Code Bloat:** Excessive use of macros can lead to code bloat because each macro expansion increases the code size.

### Example

#### Function Example
```cpp
int add(int a, int b) {
  return a + b;
}

int main() {
  int result = add(5, 3);
  return 0;
}
```

In this example, the function `add` provides a local scope for its parameters `a` and `b`. The function is called from `main`, and the result is returned.

#### Macro Example
```cpp
#define ADD(a, b) ((a) + (b))

int main() {
  int result = ADD(5, 3);
  return 0;
}
```

In this example, `ADD` is a macro. Before the code is compiled, the preprocessor replaces `ADD(5, 3)` with `((5) + (3))`.

### Summary
- **Functions** provide local scope and type safety, are evaluated at runtime, and are better for maintainable and reusable code.
- **Macros** do not provide local scope, are expanded at preprocessing time, do not provide type safety, and can lead to code bloat and harder-to-debug code.

Understanding these differences can help you decide when to use functions and when to use macros, though modern practice often favors functions for their safety and clarity.

</details>


---

### **3. Argument Handling in Functions and Macros**

Both functions and macros accept arguments. However, they differ in how arguments are treated:

- **Functions**: Treat arguments as variables. These arguments are scoped within the function and behave like normal CMake variables.

  Example:
  ```cmake
  function(greet name)
    message("Hello, ${name}!")
  endfunction()

  greet("World")
  ```

  Output: `Hello, World!`

- **Macros**: Arguments are treated as simple text substitutions.

  Example:
  ```cmake
  macro(shout message)
    message(STATUS "${message}!!!")
  endmacro()

  shout("Hello")
  ```

  Output: `-- Hello!!!`


  
#### **Keyword-Based Argument Handling**
To handle arguments more flexibly, CMake provides `cmake_parse_arguments()`, allowing you to define keyword arguments and named lists.

Example:

```cmake
include(CMakeParseArguments)

function(my_function)
  set(prefix ARG)
  set(noValues ENABLE_FEATURE)
  set(singleValues TARGET)
  set(multiValues SOURCES)

  cmake_parse_arguments(${
  prefix} "${noValues}" "${singleValues}" "${multiValues}" ${ARGN})
endfunction()
```

<details>
  <summary>Line by Line Explanation</summary>
    
```cmake
include(CMakeParseArguments)
```
- This line includes the `CMakeParseArguments` module, which provides the `cmake_parse_arguments` function used to parse arguments passed to custom functions and macros in CMake.

```cmake
function(my_function)
```
- This defines a custom function in CMake named `my_function`. The function will contain the commands between `function` and `endfunction`.

```cmake
  set(prefix ARG)
```
- This line sets the variable `prefix` to the string `ARG`. This `prefix` will be used as a prefix for the parsed arguments when using `cmake_parse_arguments`.

```cmake
  set(noValues ENABLE_FEATURE)
```
- This line defines a list of arguments that do not have values associated with them (boolean flags). In this case, `ENABLE_FEATURE` is such an argument. When `cmake_parse_arguments` encounters `ENABLE_FEATURE` in the argument list, it will be treated as a flag.

```cmake
  set(singleValues TARGET)
```
- This line defines a list of arguments that have a single value associated with them. In this case, `TARGET` is such an argument. When `cmake_parse_arguments` encounters `TARGET` in the argument list, it expects it to be followed by a single value.

```cmake
  set(multiValues SOURCES)
```
- This line defines a list of arguments that can have multiple values associated with them. In this case, `SOURCES` is such an argument. When `cmake_parse_arguments` encounters `SOURCES` in the argument list, it will collect all subsequent values until it encounters another recognized argument or the end of the argument list.

```cmake
  cmake_parse_arguments(${prefix} "${noValues}" "${singleValues}" "${multiValues}" ${ARGN})
```
- This line calls the `cmake_parse_arguments` function to parse the arguments passed to `my_function`. Here's a breakdown:
  - `${prefix}`: The prefix for the parsed arguments. In this case, it's `ARG`.
  - `"${noValues}"`:
The list of arguments that do not have associated values(flags)
        .- `"${singleValues}"`: The list of arguments that have a single
                                    associated value.- `"${multiValues}"`
    : The list of arguments that have multiple associated values.- `$ {
  ARGN
}
`: This special variable contains all the arguments passed to `my_function` that were not explicitly specified as parameters.

The `cmake_parse_arguments` function will parse the arguments passed to `my_function` and populate the variables `ARG_ENABLE_FEATURE`, `ARG_TARGET`, and `ARG_SOURCES` (and their respective `UNPARSED_ARGUMENTS` counterparts, if any) with the corresponding values.

```cmake
endfunction()
```
- This ends the definition of the `my_function`.

### Example Usage

Suppose you call `my_function` like this in your CMakeLists.txt:

```cmake
my_function(ENABLE_FEATURE TARGET MyApp SOURCES main.cpp utils.cpp)
```

- `ENABLE_FEATURE` is a flag, so `ARG_ENABLE_FEATURE` will be set to `TRUE`.
- `TARGET` is followed by `MyApp`, so `ARG_TARGET` will be set to `MyApp`.
- `SOURCES` is followed by `main.cpp` and `utils.cpp`, so `ARG_SOURCES` will be set to `main.cpp;utils.cpp` (a semicolon-separated list).

The function `cmake_parse_arguments` helps in managing complex function argument parsing in a structured and maintainable way.

  </details>

You can call this function with a keyword-based syntax:

```cmake
my_function(TARGET myApp SOURCES main.cpp utils.cpp ENABLE_FEATURE)
```

---

### **4. Scope Handling**

- **Functions**: Variables defined inside functions are local to that function unless explicitly passed outside using `PARENT_SCOPE`. This prevents unintended changes to variables outside the function scope.
  
  Example:
  ```cmake
  function(set_value var_name)
    set(${
  var_name} "local_value" PARENT_SCOPE)
  endfunction()

  set_value(my_var)
  message("my_var = ${my_var}")  # Outputs: "my_var = local_value"
  ```

- **Macros**: Since macros do not introduce a new scope, variables inside macros will affect the caller's scope.

  Example:
  ```cmake
  macro(set_value_macro var_name)
    set(${
  var_name} "macro_value")
  endmacro()

  set_value_macro(my_var)
  message("my_var = ${my_var}")  # Outputs: "my_var = macro_value"
  ```

---

### **5. Overriding Commands**

Both functions and macros can be used to override existing CMake commands, though caution is advised. Overriding can lead to confusion and difficult-to-debug issues.

Example:
```cmake
function(message)
  _message("[Overridden] ${ARGV}")
endfunction()

message("This is a test")  # Outputs: "[Overridden] This is a test"
```

---

### **6. Best Practices**

- **Use Functions Over Macros**: Functions should be preferred over macros because they avoid scope-related issues, making the CMake script more predictable and easier to maintain.
  
- **Handle Arguments with `cmake_parse_arguments()`**: When defining complex functions, use `cmake_parse_arguments()` to handle keyword-based arguments, improving clarity.

- **Avoid Overriding Built-in Commands**: Overriding commands can lead to unintended behavior, especially in large projects. Always document and use such overrides carefully.

---

### **Example Project for Beginners: Managing Tests with Functions**

This project demonstrates how to use functions to simplify test management in CMake. We'll define a function to add test executables, link libraries, and register them as tests.

The error indicates that CMake is trying to link against a library named `mylib`, but it cannot find this library. Here are the steps to fix the issue:

### **1. Ensure `mylib` is Created**

First, verify that you have created and built the `mylib` library. If not, you'll need to define and build it in your `CMakeLists.txt` file.

### **2. Add `mylib` to the Project**

Assuming `mylib` is a static or shared library you've created, here is an example of how to define and link it in your `CMakeLists.txt`:

<details>
<summary>More about Static v/s Shared Library</summary>

In the context of the sentence "Assuming mylib is a static or shared library you've created," the word "static" refers to a type of library in CMake and more broadly in software development.

### Static Library
A **static library** is a collection of object files that are linked into the program during the linking phase of compilation. It is a compiled library that is included directly into the executable file. Here are some key points about static libraries:

- **File Extension:** Typically, static libraries have the file extension `.lib` on Windows and `.a` on Unix-like systems (including Linux and macOS).
- **Linking:** The code from the static library is copied into the executable at compile time. This means the resulting executable file contains all the code it needs, including the code from the static library.
- **Performance:** Because the code is included directly into the executable, there is no runtime overhead of locating and loading the library. This can sometimes result in slightly faster execution.
- **Portability:** The executable is self-contained, so it can run on any compatible system without needing to ensure that the library is present.
- **Size:** The executable can be larger because it includes all the necessary code from the static libraries.

### Shared Library
In contrast, a **shared library** (also known as a dynamic library) is a collection of object files that are not included in the executable but are loaded at runtime. Key points about shared libraries:

- **File Extension:** Shared libraries typically have the file extension `.dll` on Windows and `.so` on Unix-like systems (including Linux and macOS).
- **Linking:** Only a reference to the shared library is included in the executable. The actual code is loaded into memory at runtime.
- **Performance:** There is a small runtime overhead to locate and load the shared library when the executable is run.
- **Portability:** The executable depends on the presence of the shared library on the system where it is run.
- **Size:** The executable is usually smaller because it does not include the code from the shared library. Additionally, multiple programs can share a single copy of the shared library in memory.

### Example CMakeLists.txt

Here's an example of how you might define and link `mylib` as either a static or shared library in your `CMakeLists.txt`:

```cmake
#Define a static library
add_library(mylib STATIC mylib.cpp)

#Or define a shared library
add_library(mylib SHARED mylib.cpp)

#Set the properties for the shared library(optional)
set_target_properties(mylib PROPERTIES VERSION 1.0 SOVERSION 1)

#Add an executable that links to the library
add_executable(myapp main.cpp)

#Link the executable to the library
target_link_libraries(myapp PRIVATE mylib)
```

In this example:
- `add_library(mylib STATIC mylib.cpp)` creates a static library from `mylib.cpp`.
- `add_library(mylib SHARED mylib.cpp)` creates a shared library from `mylib.cpp`.
- `target_link_libraries(myapp PRIVATE mylib)` links the `mylib` library to the `myapp` executable.

You would choose between `STATIC` and `SHARED` based on your specific needs for portability, performance, and deployment.

</details>

#### **Step 1: Define `mylib`**

Add the following lines to your `CMakeLists.txt` to create the `mylib` library:

```cmake
cmake_minimum_required(VERSION 3.10)

project(funmac)

#Enable testing
enable_testing()

#Define the library mylib
add_library(mylib STATIC /Users/sankarsanbisoyi/Desktop/Cmake/funmac/mylib/mylib.cpp)

#Define the function myFunction
function(myFunction targetName)
#Create an executable for the given target name with the provided sources(ARGN)
    add_executable(${targetName} ${ARGN})

#Link the mylib library to the target executable
    target_link_libraries(${targetName} PRIVATE mylib)

#Add a test for the target executable
    add_test(NAME ${targetName} COMMAND ${targetName})
endfunction(myFunction)

#Call the myFunction function for test1 and test2
myFunction(test1 /Users/sankarsanbisoyi/Desktop/Cmake/funmac/test1/test1.cpp)
myFunction(test2 /Users/sankarsanbisoyi/Desktop/Cmake/funmac/test2/test2.cpp)
```

#### **Step 2: Create `mylib.cpp`**

Create a simple source file for `mylib`:

**mylib.cpp**:
```cpp
#include <iostream>

void mylib_function() {
  std::cout << "mylib function called" << std::endl;
}
```

**mylib.h** (optional, if you need a header file for the library):
```cpp
#ifndef MYLIB_H
#define MYLIB_H

void mylib_function();

#endif
```

    ####**Step 3
    : Update `test1.cpp` and `test2
          .cpp`**

              Update your test source files to use the `mylib` function :

    **test1.cpp ** :
```cpp
#include "mylib.h"
#include <iostream>

    int
    main() {
  std::cout << "Test 1 running\n";
  mylib_function();
  return 0;
}
```

    **test2.cpp ** :
```cpp
#include "mylib.h"
#include <iostream>

    int
    main() {
  std::cout << "Test 2 running\n";
  mylib_function();
  return 0;
}
```

### **3. Build and Run**

1. **Create a build directory**:
   ```bash
   mkdir build
   cd build
   ```

2. **Configure the project**:
   ```bash
   cmake ..
   ```

3. **Build the project**:
   ```bash
   make
   ```

4. **Run the tests**:
   ```bash
   ctest
   ```

### **Expected Output**

If everything is set up correctly, the output should look like this:

```
Test 1 running
mylib function called
Test 2 running
mylib function called
```

### **Summary**

- Ensure `mylib` is defined and built correctly.
- Link `mylib` to the test executables using `target_link_libraries`.
- Update the test source files to include and use the `mylib` functions.
- **Functions** in CMake are used to encapsulate repetitive code with a local scope, whereas **macros** substitute text without scoping.
- **Argument Handling**: Functions treat arguments as variables, while macros treat them as text. You can use `cmake_parse_arguments()` for more structured argument handling.
- **Scope**: Functions introduce a new scope for variables, while macros operate in the calling scope.
- In our project example, we demonstrated using functions to simplify test case management. Functions were used to create test executables, link libraries, and register them for testing with CTest.
