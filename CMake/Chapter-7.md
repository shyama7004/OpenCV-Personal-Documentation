### Chapter 7: Using Subdirectories

#### 7.1. `add_subdirectory()`

The `add_subdirectory()` command allows you to include another directory in your build, provided it has its own `CMakeLists.txt` file. This command is typically used to structure complex projects. The syntax is:

```cmake
add_subdirectory(sourceDir [binaryDir] [EXCLUDE_FROM_ALL])
```

- **sourceDir**: The directory containing the `CMakeLists.txt` file.
- **binaryDir** (Optional): The directory in the build tree where the output will be placed.
- **EXCLUDE_FROM_ALL** (Optional): Prevents the targets in this subdirectory from being included in the default build process.

If `binaryDir` is omitted, CMake automatically creates a corresponding directory in the build tree, named after `sourceDir`.

#### 7.1.1. Source and Binary Directory Variables

CMake defines several variables to handle directories:

- **CMAKE_SOURCE_DIR**: The top-level source directory.
- **CMAKE_BINARY_DIR**: The top-level build directory.
- **CMAKE_CURRENT_SOURCE_DIR**: The source directory currently being processed.
- **CMAKE_CURRENT_BINARY_DIR**: The corresponding binary directory for the current source directory.

#### 7.1.2. Scope

When using `add_subdirectory()`, a new scope is created for the included directory. This isolates variables defined in subdirectories unless explicitly returned to the parent scope using the `PARENT_SCOPE` option. This containment avoids unwanted interference between different parts of the project.

#### 7.2. `include()`

The `include()` command brings external CMake code into the current scope without creating a new one, unlike `add_subdirectory()`. Its syntax is:

```cmake
include(filename)
```

This command is useful for including reusable CMake code (e.g., macros or functions) that may be shared across different parts of your project.

#### 7.3. Ending Processing Early

The `return()` command can be used to halt the processing of the current file and return control to the caller, especially in conditional inclusion scenarios. For example:

```cmake
if(DEFINED cool_stuff_include_guard)
    return()
endif()

set(cool_stuff_include_guard 1)
#Additional processing...
```

Starting with CMake 3.10, `include_guard()` provides a more concise and reliable way to ensure files are only included once:

```cmake
include_guard()
```

You can specify its scope with `DIRECTORY` or `GLOBAL` to control where previous inclusions are checked.

#### 7.4. Recommended Practices

Deciding between `add_subdirectory()` and `include()` depends on the structure and requirements of your project:

- Use `add_subdirectory()` to maintain modular and isolated subdirectories with their own variable scopes.
- Use `include()` when you need to share common CMake logic without introducing a new scope.

### Example: Multi-Module Project

Let's demonstrate a simple multi-module project with a main application and two libraries.

**Directory Structure:**

```
MyProject/
├── CMakeLists.txt
├── app/
│   └── CMakeLists.txt
│   └── main.cpp
├── libA/
│   └── CMakeLists.txt
│   └── libA.cpp
│   └── libA.h
└── libB/
    └── CMakeLists.txt
    └── libB.cpp
    └── libB.h
```

**Top-level `CMakeLists.txt`:**

```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

#Add subdirectories
add_subdirectory(app)
add_subdirectory(libA)
add_subdirectory(libB)
```

**`app/CMakeLists.txt`:**

```cmake
add_executable(MyApp main.cpp)
target_link_libraries(MyApp PRIVATE libA libB)
```

**`libA/CMakeLists.txt`:**

```cmake
add_library(libA libA.cpp libA.h)
```

**`libB/CMakeLists.txt`:**

```cmake
add_library(libB libB.cpp libB.h)
```

**`app/main.cpp`:**

```cpp
#include "libA.h" //include the full path
#include "libB.h" //include the full path
#include <iostream>

int main() {
  std::cout << "Hello from MyApp" << std::endl;
  libAFunction();
  libBFunction();
  return 0;
}
```

        **`libA /
    libA.cpp`: **

```cpp
#include "libA.h"
#include <iostream>

               void
               libAFunction() {
  std::cout << "Hello from libA" << std::endl;
}
```

        **`libA /
    libA.h`: **

```cpp
#pragma once

             void
             libAFunction();
```

        **`libB /
    libB.cpp`: **

```cpp
#include "libB.h"
#include <iostream>

               void
               libBFunction() {
  std::cout << "Hello from libB" << std::endl;
}
```

        **`libB /
    libB.h`: **

```cpp
#pragma once

             void
             libBFunction();
```

`Note` : Use this to print

```cpp
./app/MyApp
```

In this example, `add_subdirectory()` is used to include the `app`, `libA`, and `libB` directories. Each directory has its own `CMakeLists.txt` file, keeping the project modular and organized.
