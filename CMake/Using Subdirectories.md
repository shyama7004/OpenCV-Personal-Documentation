### Chapter 7: Using Subdirectories

#### 7.1. `add_subdirectory()`
The `add_subdirectory()` command allows you to include another directory in your build. This directory must have its own `CMakeLists.txt` file, which will be processed when `add_subdirectory()` is called. The command is used as follows:

```cmake
add_subdirectory(sourceDir [binaryDir] [EXCLUDE_FROM_ALL])
```

- **sourceDir**: The directory containing the `CMakeLists.txt` file to include.
- **binaryDir**: Optional. The directory in the build tree where the build output should be placed.
- **EXCLUDE_FROM_ALL**: Optional. Prevents the targets in the subdirectory from being included in the default build.

If `binaryDir` is omitted, CMake creates a corresponding directory in the build tree with the same name as `sourceDir` [oai_citation:10,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1) [oai_citation:9,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1).

#### 7.1.1. Source and Binary Directory Variables
CMake defines several directory variables that are useful when working with subdirectories:

- **CMAKE_SOURCE_DIR**: The top-level source directory.
- **CMAKE_BINARY_DIR**: The top-level build directory.
- **CMAKE_CURRENT_SOURCE_DIR**: The current source directory.
- **CMAKE_CURRENT_BINARY_DIR**: The current binary directory [oai_citation:8,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1).

#### 7.1.2. Scope
The `add_subdirectory()` command creates a new scope for the directory it includes. This means that variables set in the subdirectory do not affect the parent directory unless explicitly passed back using the `PARENT_SCOPE` option. This helps to keep build logic localized and prevents unintended interactions between different parts of the project [oai_citation:7,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1).

#### 7.2. `include()`
The `include()` command is another way to bring content into the build. Unlike `add_subdirectory()`, `include()` does not create a new scope. It simply includes the specified file and processes its contents as if they were part of the current directory.

```cmake
include(filename)
```

This command is useful for including reusable CMake code that can be shared across multiple directories [oai_citation:6,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1) [oai_citation:5,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1).

#### 7.3. Ending Processing Early
The `return()` command can be used to stop processing the current file and return control back to the caller. This can be useful in situations where you want to include a file only once or conditionally process parts of a file.

```cmake
if(DEFINED cool_stuff_include_guard)
    return()
endif()

set(cool_stuff_include_guard 1)
# ...
```

CMake 3.10 introduced the `include_guard()` command, which provides a more concise and robust way to achieve the same effect:

```cmake
include_guard()
```

The `include_guard()` command can also accept `DIRECTORY` or `GLOBAL` as arguments to specify the scope within which to check for previous inclusion [oai_citation:4,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1) [oai_citation:3,Professional CMake_ A Practical Guide ( PDFDrive ).pdf](file-service://file-deHgssbHq9uRc7ee75siOdK1).

#### 7.4. Recommended Practices
Choosing between `add_subdirectory()` and `include()` depends on the specific needs of your project:

- Use `add_subdirectory()` when you want to keep directories self-contained and create a new scope for variables.
- Use `include()` when you need to share common CMake code across multiple directories without creating new scopes.

**Example Project: Multi-Module Project**

Let's create a simple multi-module project with a main application and two libraries.

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

**Top-level CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

# Add subdirectories
add_subdirectory(app)
add_subdirectory(libA)
add_subdirectory(libB)
```

**app/CMakeLists.txt:**
```cmake
add_executable(MyApp main.cpp)
target_link_libraries(MyApp PRIVATE libA libB)
```

**libA/CMakeLists.txt:**
```cmake
add_library(libA libA.cpp libA.h)
```

**libB/CMakeLists.txt:**
```cmake
add_library(libB libB.cpp libB.h)
```

**app/main.cpp:**
```cpp
#include <iostream>
#include "libA.h"
#include "libB.h"

int main() {
    std::cout << "Hello from MyApp" << std::endl;
    libAFunction();
    libBFunction();
    return 0;
}
```

**libA/libA.cpp:**
```cpp
#include <iostream>
#include "libA.h"

void libAFunction() {
    std::cout << "Hello from libA" << std::endl;
}
```

**libA/libA.h:**
```cpp
#pragma once

void libAFunction();
```

**libB/libB.cpp:**
```cpp
#include <iostream>
#include "libB.h"

void libBFunction() {
    std::cout << "Hello from libB" << std::endl;
}
```

**libB/libB.h:**
```cpp
#pragma once

void libBFunction();
```


In this example, `add_subdirectory()` is used to include the `app`, `libA`, and `libB` directories. Each directory has its own `CMakeLists.txt` file to define the build rules for its components.
