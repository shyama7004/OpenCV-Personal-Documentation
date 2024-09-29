### Chapter 9: Properties (Detailed Notes)

#### Introduction to Properties
Properties in CMake influence almost all aspects of the build process, from compiling source files to installation locations. They are attached to specific entities such as directories, targets, source files, test cases, and cache variables. Properties differ from variables in that they are entity-specific and predefined by CMake, often having distinct purposes within the build system. These properties provide vital control over various stages of the build and are key to customizing behavior.

---

#### Difference Between Properties and Variables
Both properties and variables store information but have fundamental differences:
- **Variables**: These are not tied to any specific entity and are more flexible. Projects can define their own variables.
- **Properties**: These are typically predefined by CMake and are associated with a specific entity (e.g., a target or directory). The confusion between properties and variables arises because property defaults are often initialized by variables, which have similar names prefixed with `CMAKE_` (e.g., `CMAKE_CXX_FLAGS` is a variable that influences the `COMPILE_FLAGS` property).

---

#### 9.1 General Property Commands
CMake provides several commands to manage properties across different entities. These commands allow setting, getting, and manipulating properties for various entity types such as `GLOBAL`, `TARGET`, `SOURCE`, etc.

##### **`set_property()`**:
- **Purpose**: Sets properties for global entities, directories, targets, sources, and more.
- **Syntax**:
  ```cmake
  set_property(GLOBAL|DIRECTORY|TARGET|SOURCE|INSTALL|TEST|CACHE ...)
  ```
  The first argument specifies the entity type. The remaining arguments define the property name and its values.
  
  - **Options**:
    - **`APPEND`**: Appends values to an existing list property.
    - **`APPEND_STRING`**: Concatenates values to an existing string property.
  
##### **`get_property()`**:
- **Purpose**: Retrieves properties for a specific entity.
- **Syntax**:
  ```cmake
  get_property(resultVar GLOBAL|DIRECTORY|TARGET|SOURCE|...)
  ```

##### **Usage Example**:
```cmake
set_property(TARGET MyTarget PROPERTY COMPILE_FLAGS "-Wall")
get_property(myVar TARGET MyTarget PROPERTY COMPILE_FLAGS)
```

---

#### 9.2 Global Properties
Global properties apply to the entire build process. They influence how tools are launched, define the structure of projects, and more. CMake offers a specific command for querying global properties, beyond just the generic `set_property()` and `get_property()` commands.

##### **`get_cmake_property()`**:
- **Purpose**: Retrieves information about CMake's global state, such as defined variables and commands.
- **Syntax**:
  ```cmake
  get_cmake_property(resultVar property)
  ```
  Example pseudo properties:
  - **VARIABLES**: List of all regular (non-cache) variables.
  - **CACHE_VARIABLES**: List of all cache variables.
  - **COMMANDS**: List of all defined commands, functions, and macros.
  - **COMPONENTS**: List of components defined by `install()` commands.

---

#### 9.3 Directory Properties
Directory properties apply to specific directories and can override global properties within that scope. These properties mainly provide defaults for target properties and can introspect the current state of the build within a directory.

##### **Setting Directory Properties**:
```cmake
set_directory_properties(PROPERTIES prop1 val1)
```

##### **Getting Directory Properties**:
```cmake
get_directory_property(resultVar DIRECTORY dir PROPERTY propName)
```

---

#### 9.4 Target Properties
Target properties are central to defining how targets (e.g., executables, libraries) are built. They manage compiler flags, include directories, binary output locations, and more. CMake provides both general and target-specific commands to work with these properties.

##### **Setting Target Properties**:
```cmake
set_target_properties(MyTarget PROPERTIES COMPILE_FLAGS "-O2")
```

##### **Retrieving Target Properties**:
```cmake
get_target_property(resultVar MyTarget COMPILE_FLAGS)
```

##### **Common Target Properties**:
- **INCLUDE_DIRECTORIES**: Specifies include paths.
- **COMPILE_OPTIONS**: Defines additional compiler flags.
- **INTERFACE_LINK_LIBRARIES**: For managing libraries linked during building.

---

#### 9.5 Source Properties
Source properties allow for fine-grained control over how individual source files are handled by the compiler, such as setting specific compile flags for particular files.

##### **Setting Source Properties**:
```cmake
set_source_files_properties(file1 PROPERTIES COMPILE_FLAGS "-Wall")
```

##### **Retrieving Source Properties**:
```cmake
get_source_file_property(resultVar file1 COMPILE_FLAGS)
```

##### **Note**: Modifying source file properties can cause all sources in a target to be recompiled when using certain generators (e.g., Unix Makefiles). This is important to keep in mind for performance reasons.

---

#### 9.6 Cache Variable Properties
Cache variable properties mostly affect how variables are displayed and handled in CMake GUIs (e.g., CMake GUI or `ccmake`). These properties define the type, help strings, and whether a variable is marked as "advanced."

- **TYPE**: Defines the type of a cache variable (`BOOL`, `FILEPATH`, `STRING`, etc.).
- **HELPSTRING**: Sets the help text shown in the GUI.
- **STRINGS**: Provides a list of valid values for the variable in a combo box.

---

#### 9.7 Other Property Types
CMake also provides properties for specific entities like tests and installed files:

##### **Test Properties**:
```cmake
set_tests_properties(test1 PROPERTIES ENABLED ON)
get_test_property(resultVar test1 ENABLED)
```

##### **Installed File Properties**: These are used to control how files are handled during installation and packaging.

---

#### 9.8 Recommended Practices
- **Use Properties Appropriately**: Properties should be used for specific, targeted tasks within a build. Avoid overuse of file-specific properties as they can slow down the build process.
- **Consistent Use of Prefixes**: Projects defining their own properties should use unique prefixes to avoid conflicts with standard CMake properties.
- **Leverage Target Properties**: Whenever possible, use target-level properties instead of directory-level properties for managing build settings as they offer more control and consistency across the build system.

# Project Overview
We'll build a small C++ program that calculates the sum of two numbers. Here's how we'll structure the project:

- **Directory Structure**:
  ```
  CMakePropertiesExample/
  ├── CMakeLists.txt
  ├── src/
  │   ├── main.cpp
  └── include/
      └── sum.h
  ```

### Step 1: Create the C++ Files

1. **Create `main.cpp`** inside the `src/` folder:
   ```cpp
   // src/main.cpp
   #include <iostream>
   #include "sum.h"
   
   int main() {
       int a = 5, b = 7;
       std::cout << "Sum of " << a << " and " << b << " is " << sum(a, b) << std::endl;
       return 0;
   }
   ```

2. **Create `sum.h`** inside the `include/` folder:
   ```cpp
   // include/sum.h
   #ifndef SUM_H
   #define SUM_H
   
   int sum(int a, int b);
   
   #endif
   ```

3. **Create `sum.cpp`** inside the `src/` folder:
   ```cpp
   // src/sum.cpp
   #include "sum.h"
   
   int sum(int a, int b) {
       return a + b;
   }
   ```

### Step 2: Create `CMakeLists.txt`

The **CMakeLists.txt** file will define the project and how to build it. We'll use properties to set custom flags and include directories.

```cmake
# CMakeLists.txt
cmake_minimum_required(VERSION 3.10)
project(CMakePropertiesExample)

# Set directory properties (optional, e.g., showing variables in the CMake GUI)
set_directory_properties(PROPERTIES TEST_VARIABLE "Hello, CMake!")

# Create a target for the executable
add_executable(CMakePropertiesExample src/main.cpp src/sum.cpp)

# Set a custom compile flag for the target (e.g., enabling all warnings)
set_target_properties(CMakePropertiesExample PROPERTIES
    COMPILE_FLAGS "-Wall"
)

# Include the 'include' directory for headers
target_include_directories(CMakePropertiesExample PRIVATE include)

# Set global property for variables (optional, used for demonstration)
set_property(GLOBAL PROPERTY MY_GLOBAL_PROPERTY "MyGlobalValue")

# Print a message using the set global property
get_property(globalValue GLOBAL PROPERTY MY_GLOBAL_PROPERTY)
message(STATUS "Global Property MY_GLOBAL_PROPERTY is: ${globalValue}")
```

### Step 3: Build the Project

1. **Navigate** to the project directory:
   ```
   cd CMakePropertiesExample
   ```

2. **Create a build directory** to keep the source directory clean:
   ```
   mkdir build && cd build
   ```

3. **Generate the build files** using CMake:
   ```
   cmake ..
   ```

   You should see a message like:
   ```
   -- Global Property MY_GLOBAL_PROPERTY is: MyGlobalValue
   ```

4. **Build the project**:
   ```
   cmake --build .
   ```

   This will create the executable `CMakePropertiesExample`.

### Step 4: Run the Program

Once the build is complete, run the program:

```
./CMakePropertiesExample
```

You should see the output:
```
Sum of 5 and 7 is 12
```

---

### Key Concepts Demonstrated

1. **Setting Target Properties**:
   - We used `set_target_properties()` to add compiler flags (`-Wall`) to the target.
   
2. **Including Directories**:
   - `target_include_directories()` adds the `include/` directory to the project's include path, allowing `sum.h` to be found.

3. **Global and Directory Properties**:
   - We set a global property (`MY_GLOBAL_PROPERTY`) and printed its value using `get_property()`.
   - We also demonstrated how to set directory properties (`TEST_VARIABLE`), though it wasn't actively used in this project.

---

```
Made by shyama7004
