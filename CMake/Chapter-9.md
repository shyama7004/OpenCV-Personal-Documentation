
### Chapter 9: Properties in CMake

#### Overview
In CMake, properties are essential for controlling the build process and defining behaviors for different build entities. These properties can be attached to various elements like targets, source files, directories, and even cache variables, unlike variables, which have broader scope. This chapter will guide you through the core concepts, commands, and best practices regarding properties in CMake.

---

### 9.1 General Property Commands

#### set_property
The `set_property` command allows you to define or modify properties for different CMake entities, such as targets, directories, and source files. Its syntax is flexible, allowing properties to be appended or replaced.

```cmake
set_property(
  TARGET | SOURCE | DIRECTORY | INSTALL | TEST | CACHE 
  [APPEND | APPEND_STRING]
  PROPERTY <propName> [value1 [value2 ...]]
)
```

- **TARGET**: Apply properties to one or more build targets.
- **SOURCE**: Set properties on specific source files.
- **DIRECTORY**: Define properties for the current directory or a specified one.
- **INSTALL**: Configure properties for installed files.
- **TEST**: Manage properties related to testing.
- **CACHE**: Modify properties of cache variables.

The `APPEND` or `APPEND_STRING` options allow you to add values to a property without overwriting the existing ones.

#### get_property
The `get_property` command retrieves the current value of a property for an entity. This is useful for checking if a property has been set or for dynamically reacting to its value.

```cmake
get_property(<resultVar> 
  TARGET | SOURCE | DIRECTORY | INSTALL | TEST | CACHE 
  PROPERTY <propName>
)
```

---

### 9.2 Global Properties

Global properties affect the entire project. They control higher-level settings and can be accessed and modified using specific commands. Some common global properties include:

- **VARIABLES**: Lists all non-cache variables.
- **CACHE_VARIABLES**: Lists all cache variables.
- **COMMANDS**: Lists all available CMake commands.
- **COMPONENTS**: Provides details on components used in `install()` commands.

```cmake
get_cmake_property(<resultVar> VARIABLES)
```

This command retrieves the value of the `VARIABLES` global property into `<resultVar>`.

---

### 9.3 Directory Properties

Directory properties allow you to manage properties at the directory level. They can define default behaviors for targets within a specific directory and its subdirectories.

#### set_directory_properties

```cmake
set_directory_properties(PROPERTIES <prop1> <val1> [<prop2> <val2> ...])
```

This command sets multiple properties for the current directory.

#### get_directory_property

```cmake
get_directory_property(<resultVar> [DIRECTORY <dir>] PROPERTY <propName>)
```

Retrieve the value of a property for a given directory, which can be either the current directory or another specified directory.

---

### 9.4 Target Properties

Target properties are crucial as they influence how individual build targets are compiled and linked. These properties can control settings such as compiler flags, output directories, and more.

#### set_target_properties

```cmake
set_target_properties(<target1> [<target2> ...]
  PROPERTIES <propName1> <value1> [<propName2> <value2> ...]
)
```

Set multiple properties on one or more targets, such as compiler flags or version information.

#### get_target_property

```cmake
get_target_property(<resultVar> <target> <propName>)
```

Retrieve the value of a property for a specified target.

---

### 9.5 Source Properties

Source properties allow you to manage specific behaviors for individual source files, such as applying special compiler flags only to certain files.

#### set_source_files_properties

```cmake
set_source_files_properties(<file1> [<file2> ...]
  PROPERTIES <propName1> <value1> [<propName2> <value2> ...]
)
```

#### get_source_file_property

```cmake
get_source_file_property(<resultVar> <sourceFile> PROPERTY <propName>)
```

This command retrieves the property of a specific source file.

---

### 9.6 Cache Variable Properties

Cache variables store values persistently across CMake runs and can have properties attached to them, such as type, advanced status, or help strings.

Common properties for cache variables:
- **TYPE**: The data type of the cache variable (e.g., BOOL, FILEPATH, STRING).
- **ADVANCED**: Marks the variable as advanced.
- **HELPSTRING**: A description of the variable for users in CMake GUIs.
- **STRINGS**: Lists valid options for STRING-type variables.

---

### 9.7 Other Property Types

In addition to the main types (global, directory, target, source, and cache), CMake allows you to set properties on tests and installed files.

#### set_tests_properties

```cmake
set_tests_properties(<test1> [<test2> ...]
  PROPERTIES <propName1> <value1> [<propName2> <value2> ...]
)
```

#### get_test_property

```cmake
get_test_property(<resultVar> <test> PROPERTY <propName>)
```

---

### 9.8 Recommended Practices

1. **Use `target_...()` commands when available**: It's generally better to use higher-level commands like `target_compile_options()` rather than manipulating properties directly.
2. **Minimize directory property usage**: Directory properties can have unexpected side effects, especially in large projects. Where possible, set properties on targets directly.
3. **Avoid global property overuse**: Global properties should be used sparingly, as they affect the entire project and can lead to hard-to-diagnose issues.
4. **Clear property usage**: When modifying properties, ensure you understand the implications, particularly in larger and multi-target projects.

---

### Example: Managing Properties in a Simple CMake Project

This example demonstrates how to control target and source properties in a CMake project.

#### Project Structure
```
CustomLibrary/
├── CMakeLists.txt
├── src/
│   ├── main.cpp
│   └── library.cpp
├── include/
│   └── library.h
```

#### CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.15)
project(CustomLibrary)

# Include directories
include_directories(include)

# Add library
add_library(custom_lib src/library.cpp)
set_target_properties(custom_lib PROPERTIES
  VERSION 1.0
  SOVERSION 1
)

# Set source file properties
set_source_files_properties(src/library.cpp PROPERTIES
  COMPILE_FLAGS "-Wall -Wextra"
)

# Add executable
add_executable(main_exec src/main.cpp)
target_link_libraries(main_exec PRIVATE custom_lib)

# Set target properties
set_target_properties(main_exec PROPERTIES
  RUNTIME_OUTPUT_DIRECTORY "${CMAKE_BINARY_DIR}/bin"
)
```

By organizing your project this way, you can manage properties effectively, ensuring flexibility and control over the build process.

