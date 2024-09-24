### 6.1 The `if()` Command

The `if()` command in CMake allows conditional execution of commands, similar to traditional programming languages.

#### 6.1.1 Basic Expressions

**Syntax**:
```cmake
if(expression1)
    # commands ...
elseif(expression2)
    # commands ...
else()
    # commands ...
endif()
```

- **True and False Constants**:
  - **True**: `1`, `ON`, `YES`, `TRUE`, `Y`, any non-zero number.
  - **False**: `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, empty string, or a string ending with `-NOTFOUND`.

- **Variables and Strings**: If the expression does not match any of the true/false constants, it is treated as a variable name or string.

**Example**:
```cmake
set(VAR "ON")
if(${VAR})
    message("VAR is ON")
endif()
```

- If `VAR` contains a string that matches the true constants (e.g., `ON`), the block executes.

#### 6.1.2 Logic Operators

CMake provides standard logic operators to combine expressions:

- **AND**: Both conditions must be true.
- **OR**: At least one condition must be true.
- **NOT**: Inverts the condition.

**Example**:
```cmake
set(VALUE 5)
if(VALUE GREATER 3 AND VALUE LESS 10)
    message("VALUE is between 3 and 10")
endif()
```

- This will output the message since 5 is greater than 3 and less than 10.

#### 6.1.3 Comparison Tests

CMake supports numerical and string comparison tests:
- **Numerical Comparisons**: `EQUAL`, `LESS`, `GREATER`, `LESS_EQUAL`, `GREATER_EQUAL`.
- **String Comparisons**: `STREQUAL`, `STRLESS`, `STRGREATER`.

**Example**:
```cmake
set(NUM 8)
if(NUM EQUAL 8)
    message("NUM is 8")
endif()

set(STR1 "hello")
set(STR2 "world")
if(STR1 STRLESS STR2)
    message("${STR1} comes before ${STR2}")
endif()
```

- The first condition checks if `NUM` is equal to 8, and the second checks if `STR1` is lexicographically less than `STR2`.

#### 6.1.4 File System Tests

Check the existence of files or directories:

**Example**:
```cmake
if(EXISTS "CMakeLists.txt")
    message("CMakeLists.txt exists")
endif()

if(IS_DIRECTORY "src")
    message("src is a directory")
endif()
```

- `EXISTS` checks if a file or directory exists, while `IS_DIRECTORY` checks if the given path is a directory.

#### 6.1.5 Existence Tests

These tests check if a variable or command exists in the current context.

- **Variable Existence**:
  ```cmake
  if(DEFINED MY_VAR)
      message("MY_VAR is defined")
  endif()
  ```

- **Command Existence**:
  ```cmake
  if(COMMAND my_custom_command)
      message("my_custom_command is available")
  endif()
  ```

---

### 6.2 Looping

CMake provides two looping mechanisms: `foreach()` and `while()`.

#### 6.2.1 `foreach()`

Used to iterate over a list of items or a numerical range.

**Syntax**:
```cmake
foreach(var RANGE start stop [step])
    # commands ...
endforeach()
```

- The `foreach()` command can iterate over ranges or explicit lists.

**Range Example**:
```cmake
foreach(i RANGE 1 5)
    message("i is ${i}")
endforeach()
```

**List Example**:
```cmake
foreach(item IN LISTS listVar)
    message("Item: ${item}")
endforeach()
```

- You can loop through ranges (`RANGE`) or lists (`IN LISTS`).

#### 6.2.2 `while()`

Executes a block of code repeatedly as long as the condition is true.

**Syntax**:
```cmake
while(condition)
    # commands ...
endwhile()
```

**Example**:
```cmake
set(counter 0)
while(counter LESS 3)
    message("Counter is ${counter}")
    math(EXPR counter "${counter}+1")
endwhile()
```

- This loop prints the value of `counter` from 0 to 2.

#### 6.2.3 Interrupting Loops

- **break()**: Exit the loop early.
- **continue()**: Skip to the next iteration.

**Example**:
```cmake
foreach(letter IN ITEMS a b c d)
    if(letter STREQUAL "c")
        continue() # Skip "c"
    endif()
    message("Processing ${letter}")
endforeach()
```

- This loop will skip the item `"c"` and process the others.

**Nested Loops with Break**:
```cmake
foreach(x IN ITEMS 1 2 3)
    foreach(y IN ITEMS a b c)
        if(x EQUAL 2 AND y STREQUAL "b")
            break() # Exit inner loop
        endif()
        message("${x}-${y}")
    endforeach()
endforeach()
```

---

### 6.3 Recommended Practices

Here are some recommended practices for using flow control in CMake:

- **Use Readable Constructs**: Avoid repeating conditions or expressions in control structures:
  ```cmake
  if(CONDITION)
      # some code
  endif() # Don't repeat the condition
  ```

- **Use Foreach for Lists**: Prefer `foreach()` when iterating over lists or ranges, and use `while()` only when the loop's end condition is dynamic.

- **Avoid Complex Nesting**: Keep your loops and conditionals simple to enhance readability. For complex conditions, consider breaking logic into separate helper functions.

**Example**:
```cmake
# Bad Practice
if(VAR1 EQUAL 1 AND VAR2 EQUAL 2 AND VAR3 EQUAL 3)
    # complex logic here
endif()

# Better Practice
function(check_conditions var1 var2 var3)
    if(var1 EQUAL 1 AND var2 EQUAL 2 AND var3 EQUAL 3)
        return(1)
    else()
        return(0)
    endif()
endfunction()

check_conditions(${VAR1} ${VAR2} ${VAR3})
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
