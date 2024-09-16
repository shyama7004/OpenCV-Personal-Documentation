### Chapter 4: Building Simple Targets - Detailed Notes for Beginners

#### 4.1. Executables

**Step-by-Step Guide**

1. **Create a New Project**: Start by creating a new directory for your project.
   
2. **Create `CMakeLists.txt`**: Inside your project directory, create a file named `CMakeLists.txt`.

3. **Minimum Required Version and Project Name**:
    ```cmake
    cmake_minimum_required(VERSION 3.10)
    project(MyFirstProject)
    ```

4. **Add an Executable**:
    - To define an executable target, use the `add_executable` command.
    - Example: Create a `main.cpp` file in your project directory and add the following line to your `CMakeLists.txt`:
    ```cmake
    add_executable(MyFirstExecutable main.cpp)
    ```
    - This tells CMake to compile `main.cpp` and create an executable named `MyFirstExecutable`.

**Example Project**

**Directory Structure**:
```
MyFirstProject/
├── CMakeLists.txt
└── main.cpp
```

**main.cpp**:
```cpp
#include <iostream>

int main() {
    std::cout << "Hello, World!" << std::endl;
    return 0;
}
```

**CMakeLists.txt**:
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyFirstProject)

add_executable(MyFirstExecutable main.cpp)
```

**Build Instructions**:
```sh
mkdir build
cd build
cmake ..
make
./MyFirstExecutable
```

#### 4.2. Defining Libraries

**Step-by-Step Guide**

1. **Create Library Source Files**:
    - Create a new file, e.g., `mylib.cpp` and its header `mylib.h`.

2. **Add Library in `CMakeLists.txt`**:
    ```cmake
    add_library(MyLibrary mylib.cpp)
    ```

3. **Link Library to Executable**:
    - Ensure your executable links to this library.
    ```cmake
    target_link_libraries(MyFirstExecutable PRIVATE MyLibrary)
    ```

**Example Project**

**Directory Structure**:
```
MyFirstProject/
├── CMakeLists.txt
├── main.cpp
└── mylib/
    ├── mylib.cpp
    └── mylib.h
```

**mylib/mylib.cpp**:
```cpp
#include "mylib.h"

void print_hello() {
    std::cout << "Hello from MyLibrary!" << std::endl;
}
```

**mylib/mylib.h**:
```cpp
#ifndef MYLIB_H
#define MYLIB_H

void print_hello();

#endif
```

**main.cpp**:
```cpp
#include <iostream>
#include "mylib.h"

int main() {
    print_hello();
    return 0;
}
```

**CMakeLists.txt**:
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyFirstProject)

add_library(MyLibrary mylib/mylib.cpp)
add_executable(MyFirstExecutable main.cpp)
target_link_libraries(MyFirstExecutable PRIVATE MyLibrary)
```

**Build Instructions**:
```sh
mkdir build
cd build
cmake ..
make
./MyFirstExecutable
```

#### 4.3. Linking Targets

**Step-by-Step Guide**

1. **Create Multiple Libraries and Executables**:
    - Define multiple libraries and executables in your `CMakeLists.txt`.

2. **Link Libraries to Executables**:
    ```cmake
    add_library(LibraryOne libone.cpp)
    add_library(LibraryTwo libtwo.cpp)

    add_executable(MyExecutable main.cpp)
    target_link_libraries(MyExecutable PRIVATE LibraryOne LibraryTwo)
    ```

**Example Project**

**Directory Structure**:
```
MyFirstProject/
├── CMakeLists.txt
├── main.cpp
├── libone/
│   ├── libone.cpp
│   └── libone.h
└── libtwo/
    ├── libtwo.cpp
    └── libtwo.h
```

**libone/libone.cpp**:
```cpp
#include "libone.h"

void print_one() {
    std::cout << "Hello from Library One!" << std::endl;
}
```

**libone/libone.h**:
```cpp
#ifndef LIBONE_H
#define LIBONE_H

void print_one();

#endif
```

**libtwo/libtwo.cpp**:
```cpp
#include "libtwo.h"

void print_two() {
    std::cout << "Hello from Library Two!" << std::endl;
}
```

**libtwo/libtwo.h**:
```cpp
#ifndef LIBTWO_H
#define LIBTWO_H

void print_two();

#endif
```

**main.cpp**:
```cpp
#include <iostream>
#include "libone/libone.h"
#include "libtwo/libtwo.h"

int main() {
    print_one();
    print_two();
    return 0;
}
```

**CMakeLists.txt**:
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyFirstProject)

add_library(LibraryOne libone/libone.cpp)
add_library(LibraryTwo libtwo/libtwo.cpp)

add_executable(MyExecutable main.cpp)
target_link_libraries(MyExecutable PRIVATE LibraryOne LibraryTwo)
```

**Build Instructions**:
```sh
mkdir build
cd build
cmake ..
make
./MyExecutable
```

### Detailed Explanation of Section 4.4: Linking Non-targets

#### Overview
In CMake, `target_link_libraries()` is a versatile command that can link more than just CMake targets. It can also link to non-targets such as system libraries, library files, and even specific linker flags. Understanding how to link these non-targets is essential for managing dependencies effectively in a project.

#### Linking Full Path to a Library File
When specifying a full path to a library file, CMake will directly include this library in the linker command. This ensures that any changes to the library file will trigger a re-link of the target.

**Example:**

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

add_executable(MyExecutable main.cpp)

# Full path to a library file
target_link_libraries(MyExecutable PRIVATE /usr/local/lib/libmylib.a)
```

In this example, `MyExecutable` links to `libmylib.a` located in `/usr/local/lib`.

#### Linking by Plain Library Name
If only the library name is specified, CMake will ask the linker to search for the library in the standard system paths.

**Example:**

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

add_executable(MyExecutable main.cpp)

# Plain library name
target_link_libraries(MyExecutable PRIVATE m)
```

Here, `MyExecutable` links to the math library `libm.so` on Unix systems or `libm.lib` on Windows.

#### Adding Linker Flags
Linker flags can be added directly using `target_link_libraries()` if they start with a hyphen (other than `-l` or `-framework`).

**Example:**

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

add_executable(MyExecutable main.cpp)

# Adding linker flags
target_link_libraries(MyExecutable PRIVATE -Wl,--no-as-needed)
```

This adds the `--no-as-needed` flag to the linker command.

#### Using Keywords: `debug`, `optimized`, and `general`
CMake supports keywords to control when specific libraries or flags should be included based on the build type.

**Example:**

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

add_executable(MyExecutable main.cpp)

# Linking with keywords
target_link_libraries(MyExecutable PRIVATE
    debug /usr/local/lib/libmylib_debug.a
    optimized /usr/local/lib/libmylib.a)
```

In this example:
- `libmylib_debug.a` is linked during debug builds.
- `libmylib.a` is linked during optimized (non-debug) builds.

### Complete Example Project

**Directory Structure:**
```
MyProject/
├── CMakeLists.txt
└── main.cpp
```

**main.cpp:**
```cpp
#include <iostream>
#include <cmath>

int main() {
    double result = std::sqrt(16.0);
    std::cout << "The square root of 16 is " << result << std::endl;
    return 0;
}
```

**CMakeLists.txt:**
```cmake
cmake_minimum_required(VERSION 3.10)
project(MyProject)

add_executable(MyExecutable main.cpp)

# Full path to a library file
target_link_libraries(MyExecutable PRIVATE /usr/local/lib/libmylib.a)

# Plain library name
target_link_libraries(MyExecutable PRIVATE m)

# Adding linker flags
target_link_libraries(MyExecutable PRIVATE -Wl,--no-as-needed)

# Linking with keywords
target_link_libraries(MyExecutable PRIVATE
    debug /usr/local/lib/libmylib_debug.a
    optimized /usr/local/lib/libmylib.a)
```

**Build Instructions:**
1. Create a build directory and navigate into it:
    ```sh
    mkdir build
    cd build
    ```

2. Generate the build system using CMake:
    ```sh
    cmake ..
    ```

3. Compile the project:
    ```sh
    make
    ```

4. Run the executable:
    ```sh
    ./MyExecutable
    ```

