Let's create a simple C++ project using CMake and explain the use of variables throughout the project setup. The project will have two source files and a utility function. We’ll use CMake to organize the build system efficiently.

### **Project Structure**

We'll create a project that prints a simple message and includes a utility to print another line. Here's the project structure:

```
MyCMakeProject/
│
├── CMakeLists.txt
├── main.cpp
├── utils.cpp
├── utils.h
└── build/ (CMake will create this during the build process)
```

### **1. Step-by-Step CMake Setup**

#### **Step 1: Creating the CMakeLists.txt File**

Here’s the content of the `CMakeLists.txt` file:

```cmake
# Minimum CMake version
cmake_minimum_required(VERSION 3.10)

# Project name and language
project(MyCMakeProject VERSION 1.0 LANGUAGES CXX)

# Set source files in a variable
set(SOURCE_FILES main.cpp utils.cpp)

# Define an executable using the variable
add_executable(MyApp ${SOURCE_FILES})

# Optional: Add include directories
target_include_directories(MyApp PRIVATE ${CMAKE_SOURCE_DIR})
```

- **`cmake_minimum_required(VERSION 3.10)`**: Ensures that we are using CMake version 3.10 or newer.
- **`project(MyCMakeProject VERSION 1.0 LANGUAGES CXX)`**: Declares the project name, version, and programming language (C++).
- **`set(SOURCE_FILES main.cpp utils.cpp)`**: This defines a variable `SOURCE_FILES` that holds the list of source files. We will pass this variable to `add_executable()`.
- **`${CMAKE_SOURCE_DIR}`**: This variable refers to the root directory of the project. It’s useful when you want to include files like headers from the main directory.

#### **Step 2: Creating `main.cpp`**

Here's the content of `main.cpp`:

```cpp
#include <iostream>
#include "utils.h"

int main() {
    std::cout << "Hello from MyCMakeProject!" << std::endl;
    print_util_message();
    return 0;
}
```

This file prints a message and calls a function from `utils.cpp`.

#### **Step 3: Creating `utils.cpp`**

```cpp
#include "utils.h"
#include <iostream>

void print_util_message() {
    std::cout << "This is a message from the utils function!" << std::endl;
}
```

#### **Step 4: Creating `utils.h`**

```cpp
#ifndef UTILS_H
#define UTILS_H

void print_util_message();

#endif
```

### **2. Using Variables in CMake**

- **Setting Variables**: In the `CMakeLists.txt`, we used the `set()` command to define the `SOURCE_FILES` variable:
  ```cmake
  set(SOURCE_FILES main.cpp utils.cpp)
  ```
  This variable holds the source files and is passed to `add_executable()` to create the executable `MyApp`.

- **Using Cache Variables**: Suppose we want to allow the user to specify where the project should be installed. We can use a cache variable for this:
  
  ```cmake
  set(INSTALL_DIR "/usr/local/bin" CACHE PATH "Installation directory")
  ```

  You can also modify this on the command line during the build process:

  ```bash
  cmake -DINSTALL_DIR=/custom/install/path ..
  ```

### **3. Building the Project**

1. **Navigate to the project directory**:
   ```bash
   cd MyCMakeProject
   ```

2. **Create the `build/` directory**:
   ```bash
   mkdir build && cd build
   ```

3. **Run CMake to configure the project**:
   ```bash
   cmake ..
   ```

4. **Build the project**:
   ```bash
   cmake --build .
   ```

Once built, the executable `MyApp` will be created in the `build/` directory. You can run it:

```bash
./MyApp
```

### **4. Adding More CMake Variables**

Let’s introduce a variable to control whether we build with a debug message:

```cmake
option(ENABLE_DEBUG "Enable debug mode" ON)

if(ENABLE_DEBUG)
    add_definitions(-DDEBUG)
endif()
```

- **`option(ENABLE_DEBUG "Enable debug mode" ON)`**: Defines a cache variable called `ENABLE_DEBUG` with the default value `ON`.
- **`add_definitions(-DDEBUG)`**: If `ENABLE_DEBUG` is `ON`, it adds a preprocessor definition to the compilation process.

### **5. Modifying the Code for Debug Mode**

Modify `main.cpp` to print a debug message when `ENABLE_DEBUG` is `ON`:

```cpp
#include <iostream>
#include "utils.h"

int main() {
    #ifdef DEBUG
    std::cout << "Debug mode is enabled!" << std::endl;
    #endif
    
    std::cout << "Hello from MyCMakeProject!" << std::endl;
    print_util_message();
    return 0;
}
```

Now you can enable or disable debug mode from the command line:

```bash
cmake -DENABLE_DEBUG=OFF ..
cmake --build .
./MyApp
```

---

### **Summary of Key Concepts**:

1. **Variables**: Use `set()` to define variables and reuse them in the project.
2. **Cache Variables**: Define user-configurable options that persist across builds using cache variables.
3. **Conditional Logic**: Use `option()` and `if()` for toggling features like debug mode.
4. **Organizing Source Files**: Define variables to hold source files and pass them to CMake commands like `add_executable()`.

This simple project demonstrates how to use CMake variables effectively to manage builds and add flexibility to the project. Let me know if you have any questions or need further clarification!
