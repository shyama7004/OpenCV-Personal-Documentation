## **Chapter 6: Flow Control** 

### 1. **Understanding the `if()` Command**

The **`if()`** command in CMake works similarly to `if` statements in other programming languages. It helps you conditionally run specific commands based on an expression or condition.

#### Syntax:
```cmake
if(expression)
   # Commands to execute if expression is true
elseif(expression)
   # Commands to execute if the elseif expression is true
else()
   # Commands to execute if none of the above are true
endif()
```

---

### 2. **Basic Expressions in `if()`**

CMake considers the following values as **true**:
- 1, ON, YES, TRUE, Y, or any non-zero number.

And the following values as **false**:
- 0, OFF, NO, FALSE, N, IGNORE, NOTFOUND, an empty string, or a string that ends in `-NOTFOUND`.

**Example:**

```cmake
set(VAR TRUE)
if(${VAR})  # This evaluates to true
   message("Variable is true!")
endif()
```

---

### 3. **Using Logic Operators**

CMake supports the usual logical operators, like **AND**, **OR**, and **NOT**, to form more complex conditions.

**Example:**

```cmake
if(NOT VAR1 AND VAR2 OR VAR3)
   message("The condition is true!")
endif()
```

---

### 4. **Comparison Tests**

CMake offers multiple comparison operators for numbers, strings, and versions.

#### Numeric Comparison:
```cmake
if(2 GREATER 1)  # True
if(${MY_NUMBER} EQUAL 42)  # True if MY_NUMBER is 42
```

#### String Comparison:
```cmake
if("Hello" STREQUAL "Hello")  # True
```

#### Version Comparison:
```cmake
if(CMAKE_VERSION VERSION_GREATER "3.10")  # True if CMake version is greater than 3.10
```

---

### 5. **File System Tests**

You can also use `if()` to check the existence of files and directories.

**Common file system tests**:
- **`EXISTS pathToFileOrDir`**: Checks if a file or directory exists.
- **`IS_DIRECTORY pathToDir`**: Checks if the path is a directory.

**Example:**
```cmake
if(EXISTS "${CMAKE_SOURCE_DIR}/myfile.txt")
   message("File exists!")
endif()
```

---

### 6. **Loops in CMake**

#### **`foreach()` Loop**

The **`foreach()`** loop allows you to iterate over a list of items in CMake.

**Syntax:**
```cmake
foreach(item IN ITEMS a b c)
   message("Item: ${item}")
endforeach()
```

**Example:**
```cmake
set(MY_LIST a b c)
foreach(item IN LISTS MY_LIST)
   message("Item: ${item}")
endforeach()
```

This will print:
```
Item: a
Item: b
Item: c
```

#### **`while()` Loop**

The **`while()`** loop repeats a block of commands as long as a condition is true.

**Syntax:**
```cmake
set(COUNTER 0)
while(${COUNTER} LESS 5)
   message("Counter: ${COUNTER}")
   math(EXPR COUNTER "${COUNTER} + 1")
endwhile()
```

This will print:
```
Counter: 0
Counter: 1
Counter: 2
Counter: 3
Counter: 4
```

---

### 7. **Own Project Example Using Flow Control**

Let’s create a simple CMake project with flow control to demonstrate these features.

#### **Project Structure:**
```
FlowControlProject/
│
├── CMakeLists.txt
└── main.cpp
```

#### **Step 1: Create `main.cpp`**

This is a simple C++ program that checks the platform and runs a loop.

```cpp
#include <iostream>

int main() {
    std::cout << "Hello from FlowControlProject!" << std::endl;

#ifdef DEBUG
    std::cout << "Debug mode is enabled!" << std::endl;
#endif

    for (int i = 0; i < 5; ++i) {
        std::cout << "Counter: " << i << std::endl;
    }

    return 0;
}
```

#### **Step 2: Create `CMakeLists.txt`**

```cmake
# Minimum required CMake version
cmake_minimum_required(VERSION 3.10)

# Project name
project(FlowControlProject)

# Add executable
add_executable(MyApp main.cpp)

# Option for enabling debug mode
option(ENABLE_DEBUG "Enable debug mode" ON)

# Conditionally add a preprocessor definition for debug mode
if(ENABLE_DEBUG)
    target_compile_definitions(MyApp PRIVATE DEBUG)
endif()

# Platform-specific settings
if(WIN32)
    message("Compiling on Windows")
elseif(UNIX)
    message("Compiling on Unix/Linux")
endif()
```

#### **Step 3: Building the Project**

1. **Create a build directory**:
   ```bash
   mkdir build && cd build
   ```

2. **Run CMake**:
   ```bash
   cmake ..
   ```

3. **Build the project**:
   ```bash
   cmake --build .
   ```

4. **Run the executable**:
   ```bash
   ./MyApp
   ```

If the option `ENABLE_DEBUG` is set to `ON`, it will print:
```
Debug mode is enabled!
```

---

### **Conclusion**

Using flow control in CMake allows you to customize your project build based on conditions such as platform, environment, or specific variables. The **`if()`** command helps with conditional logic, while loops like **`foreach()`** and **`while()`** help in iterating over items. This flexibility helps in creating complex build systems that adapt to various requirements.
