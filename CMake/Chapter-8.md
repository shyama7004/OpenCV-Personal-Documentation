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
  # Function body
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
  # Macro body
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

  cmake_parse_arguments(${prefix} "${noValues}" "${singleValues}" "${multiValues}" ${ARGN})
endfunction()
```

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
    set(${var_name} "local_value" PARENT_SCOPE)
  endfunction()

  set_value(my_var)
  message("my_var = ${my_var}")  # Outputs: "my_var = local_value"
  ```

- **Macros**: Since macros do not introduce a new scope, variables inside macros will affect the caller's scope.

  Example:
  ```cmake
  macro(set_value_macro var_name)
    set(${var_name} "macro_value")
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

#### **Step 1: Define `mylib`**

Add the following lines to your `CMakeLists.txt` to create the `mylib` library:

```cmake
cmake_minimum_required(VERSION 3.10)
project(SimpleTestManager)

# Define the library
add_library(mylib STATIC mylib.cpp)

# Define a function to add test executables and link libraries
function(add_mytest targetName)
  add_executable(${targetName} ${ARGN})  # Creates an executable
  target_link_libraries(${targetName} PRIVATE mylib)  # Links the executable to 'mylib'
  add_test(NAME ${targetName} COMMAND ${targetName})  # Registers the executable as a test
endfunction()

# Add test executables
add_mytest(test1 test1.cpp)
add_mytest(test2 test2.cpp)
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

#### **Step 3: Update `test1.cpp` and `test2.cpp`**

Update your test source files to use the `mylib` function:

**test1.cpp**:
```cpp
#include <iostream>
#include "mylib.h"

int main() {
  std::cout << "Test 1 running\n";
  mylib_function();
  return 0;
}
```

**test2.cpp**:
```cpp
#include <iostream>
#include "mylib.h"

int main() {
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
