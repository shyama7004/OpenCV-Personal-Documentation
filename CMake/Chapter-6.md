### 6.1 The `if()` Command

The `if()` command in CMake allows conditional execution of commands, similar to traditional programming languages.

#### 6.1.1 Basic Expressions

**Syntax**:

```cmake
if(expression1)
#commands...
elseif(expression2)
#commands...
else()
#commands...
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
#commands...
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
#commands...
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
  #some code
  endif() # Don't repeat the condition
  ```

- **Use Foreach for Lists**: Prefer `foreach()` when iterating over lists or ranges, and use `while()` only when the loop's end condition is dynamic.

- **Avoid Complex Nesting**: Keep your loops and conditionals simple to enhance readability. For complex conditions, consider breaking logic into separate helper functions.

**Example**:

```cmake
#Bad Practice
if(VAR1 EQUAL 1 AND VAR2 EQUAL 2 AND VAR3 EQUAL 3)
#complex logic here
endif()

#Better Practice
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

### Beginner Project: Conditional Compilation with CMake

**Objective**: Create a simple CMake project that conditionally compiles different source files based on user-defined options.

#### Step 1: Create Project Structure

Create the following directory structure for your project:

```
ConditionalCompilation/
│
├── CMakeLists.txt
└── src/
    ├── main.cpp
    ├── featureA.cpp
    └── featureB.cpp
```

#### Step 2: Write Source Files

Create the source files with the following content:

- `main.cpp`:
  ```cpp
  #include <iostream>
  #ifdef USE_FEATURE_A
  void featureA();
  #endif
  #ifdef USE_FEATURE_B
  void featureB();
  #endif
  ```

int main() {
std::cout << "Main Function" << std::endl;
#ifdef USE_FEATURE_A
featureA();
#endif
#ifdef USE_FEATURE_B
featureB();
#endif
return 0;
}

````

    - `featureA.cpp`:
  ```cpp
#include <iostream>
    void
    featureA() {
  std::cout << "Feature A" << std::endl;
}
````

    - `featureB.cpp`:

```cpp
#include <iostream>
  void
  featureB() {
std::cout << "Feature B" << std::endl;
}
```

#### Step 3: Write CMakeLists.txt

Create the `CMakeLists.txt` file in the root directory of your project with the following content:

```cmake
cmake_minimum_required(VERSION 3.10)
project(ConditionalCompilation)

#Define options to enable / disable features
option(USE_FEATURE_A "Enable Feature A" ON)
option(USE_FEATURE_B "Enable Feature B" OFF)

#Add the main executable
add_executable(main src/main.cpp)

#Conditionally compile featureA.cpp if USE_FEATURE_A is ON
if(USE_FEATURE_A)
    target_compile_definitions(main PRIVATE USE_FEATURE_A)
    target_sources(main PRIVATE src/featureA.cpp)
endif()

#Conditionally compile featureB.cpp if USE_FEATURE_B is ON
if(USE_FEATURE_B)
    target_compile_definitions(main PRIVATE USE_FEATURE_B)
    target_sources(main PRIVATE src/featureB.cpp)
endif()
```

### Step 4: Build and Run the Project

1. **Create a Build Directory**:
   It's a good practice to create a separate build directory to keep your source tree clean. Run the following commands in your terminal:

   ```sh
   mkdir build
   cd build
   ```

2. **Configure the Project**:
   Run CMake to configure your project. By default, this will enable `USE_FEATURE_A` and disable `USE_FEATURE_B` as specified in your `CMakeLists.txt` file:

   ```sh
   cmake ..
   ```

   If you want to enable or disable specific features, you can pass options to the CMake command. For example, to enable both features:

   ```sh
   cmake -DUSE_FEATURE_A=ON -DUSE_FEATURE_B=ON ..
   ```

3. **Build the Project**:
   Compile the project using the build tool generated by CMake. Typically, this will be `make`:

   ```sh
   make
   ```

4. **Run the Executable**:
   After building, you can run the executable. The name of the executable will be `main` as specified in your `CMakeLists.txt`:

   ```sh
   ./main
   ```

### Example Commands

Here is a summary of the commands you would run in sequence:

```sh
mkdir build
cd build
cmake ..
make
./main
```

If you want to enable both features `A` and `B`, use:

```sh
cmake -DUSE_FEATURE_A=ON -DUSE_FEATURE_B=ON ..
make
./main
```

### Expected Output

Based on your configuration and the contents of `featureA.cpp` and `featureB.cpp`, you should see the following output:

1. If only `USE_FEATURE_A` is enabled:

   ```
   Main Function
   Feature A
   ```

2. If only `USE_FEATURE_B` is enabled:

   ```
   Main Function
   Feature B
   ```

3. If both `USE_FEATURE_A` and `USE_FEATURE_B` are enabled:
   ```
   Main Function
   Feature A
   Feature B
   ```

### Detailed Explanation

1. **Project Structure**:

   - `CMakeLists.txt` is the main configuration file for CMake, which controls how the project is built.
   - `src/` directory contains the source files for the project.

2. **Source Files**:

   - `main.cpp` contains the main entry point of the application. It conditionally calls `featureA()` and `featureB()` based on whether the respective feature macros are defined.
   - `featureA.cpp` and `featureB.cpp` define simple functions that print messages to the console.

3. **CMake Configuration**:
   - `cmake_minimum_required(VERSION 3.10)`: Specifies the minimum version of CMake required.
   - `project(ConditionalCompilation)`: Defines the project name.
   - `option(USE_FEATURE_A "Enable Feature A" ON)`: Defines an option to enable or disable Feature A. By default, it is ON.
   - `option(USE_FEATURE_B "Enable Feature B" OFF)`: Defines an option to enable or disable Feature B. By default, it is OFF.
   - `add_executable(main src/main.cpp)`: Adds an executable target named `main` with `src/main.cpp` as its source file.
   - `if(USE_FEATURE_A)`: Checks if Feature A is enabled.
     - `target_compile_definitions(main PRIVATE USE_FEATURE_A)`: Adds the `USE_FEATURE_A` macro definition to the `main` target.
     - `target_sources(main PRIVATE src/featureA.cpp)`: Adds `src/featureA.cpp` as a source file for the `main` target.
   - `if(USE_FEATURE_B)`: Checks if Feature B is enabled.
     - `target_compile_definitions(main PRIVATE USE_FEATURE_B)`: Adds the `USE_FEATURE_B` macro definition to the `main` target.
     - `target_sources(main PRIVATE src/featureB.cpp)`: Adds `src/featureB.cpp` as a source file for the `main` target.

This project demonstrates how to use conditional compilation and options in CMake to build different versions of an executable based on user-defined settings. It provides a hands-on introduction to basic flow control mechanisms in CMake.
