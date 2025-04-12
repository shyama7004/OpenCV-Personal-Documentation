## Chapter 5. Variables

### 5.1 Variable Basics
Variables are a central aspect of CMake's scripting. CMake treats all variables as strings, but they can represent different types based on context (e.g., boolean values or lists).

- **Variable Scope:** Variables have a scope in CMake, which can either be global (applying throughout a CMakeLists.txt) or local, restricted to functions, macros, or subdirectories. The `PARENT_SCOPE` keyword can promote a variable’s scope to its enclosing context.
  
- **Multiple Values:** When setting multiple values, they are separated by semicolons by default. Use quotes to handle spaces properly.

Example:

```cmake
set(myVar a b c)    # myVar = "a;b;c"
set(myVar "a b c")  # myVar = "a b c"
```

- **Recursive Variables:** CMake allows recursive variable evaluation. You can define a variable using another variable’s value, enabling complex dependencies.

```cmake
set(foo ab)               
set(bar ${foo}cd)         # bar = "abcd"
set(big "${${myVar}r}ef") # big = "abcdef"
```

The `$` is used for variable substitution. It replaces the variable with its value. In your example:

- `${
  foo
}
` is replaced with the value of `foo` (`"ab"`), so `bar` becomes `"abcd"`.- `$ {
  myVar
}` is replaced with `"abc"`, making `big = "abcdef"`.

- **Multi-line Strings and Bracket Syntax:** CMake allows multi-line strings using bracket syntax `[[ ]]`. This is especially helpful when defining scripts or avoiding quote escaping.

```cmake
set(shellScript [=[
#!/ bin / bash
[[ -n "${USER}" ]] && echo "Have USER"
]=])
```

The command sets a variable `shellScript` to a multi-line string that contains a Bash script. The script checks if the `USER` variable is non-empty (`-n "${USER}"`) and prints "Have USER" if it is. The `[=[ ... ]=]` syntax allows for multi-line strings without escaping.

A `Bash script` is a text file containing a series of commands that are executed by the Bash shell (a Unix shell). It allows users to automate tasks, manage system processes, and run sequences of commands efficiently. Bash scripts typically have a `.sh` extension and can include control structures like loops and conditionals, making them powerful for scripting tasks in a Unix-like environment.

- **Unsetting Variables:** Variables can be unset with `unset()` or `set()` with no value.

```cmake
unset(myVar)
```

### 5.2 Environment Variables
You can retrieve and set environment variables using the form `$ENV{
  varName
}
`.

    This line means that you can access and modify environment variables in a
        specific syntax :

    -**`$ENV {
  varName
}
`** : This is a way to retrieve the value of an environment variable
          named `varName`
              .The `$ENV` prefix indicates that it is an environment variable.-
    You can also set an environment variable using the same syntax,
    typically in programming languages like Perl.

    For example : -To retrieve the value : `value = $ENV {
  HOME
}` gets the home directory.
- To set a value: `$ENV{MY_VAR} = "some_value"` assigns "some_value" to the environment variable `MY_VAR`.


Example:
```cmake
set(ENV{PATH} "$ENV{PATH}:/opt/myDir")
```
Note that setting an environment variable this way only affects the current CMake instance and does not persist beyond the configuration phase.

### 5.3 Cache Variables
Cache variables are persistent across CMake runs and stored in `CMakeCache.txt`. These variables are typically used to store user or system settings that do not change often.

Example:
```cmake
set(myVar foo CACHE STRING "A cache variable")
```

Cache variables can be of the following types:
- **BOOL:** Boolean values, often displayed as checkboxes in GUI tools.
- **FILEPATH/PATH:** A file or directory path.
- **STRING:** A generic string.

CMake treats all cache variable values as strings, but the type provides a hint for user interfaces. Cache variables can be forced to overwrite previous values using the `FORCE` option.

### 5.4 Manipulating Cache Variables

In CMake, cache variables are used to store the values of variables across multiple runs of the CMake configuration process. Here’s what they mean:

1. **Persistence**: Cache variables allow you to keep settings between CMake runs, so you don’t have to re-enter them each time you configure your project.

2. **User-Editable**: These variables can be modified by the user through the CMake GUI or by editing the `CMakeCache.txt` file directly.

3. **Definition**: Cache variables are typically defined using the `set` command with the `CACHE` option, like this:
   ```cmake
   set(MY_VAR "default_value" CACHE STRING "Description of MY_VAR")
   ```

4. **Visibility**: They can be set to different types (e.g., `STRING`, `BOOL`, etc.) and are visible to all CMake scripts and commands.

5. **Usage**: Cache variables are often used for configuration options, such as specifying library paths or enabling/disabling features in the build process.


Cache variables can be manipulated directly from the command line using the `-D` option or through GUI tools like `cmake-gui` and `ccmake`.

Command-line example:
```sh
cmake -Dfoo:BOOL=ON -Dbar:STRING="Some value"
```

To remove variables:
```sh
cmake -U 'foo*'
```

### 5.5 Debugging Variables and Diagnostics
Use the `message()` command to print the value of variables for debugging purposes. The `message()` command accepts different modes for logging, such as STATUS, WARNING, SEND_ERROR, and FATAL_ERROR.

Example:
```cmake
set(myVar "Hello, CMake!")
message(STATUS "The value of myVar is: ${myVar}")
```

### 5.6 String Handling
CMake provides the `string()` command for common string operations such as finding, replacing, or comparing substrings. For example, to find a substring within a string:

```cmake
string(FIND "hello world" "world" index)
message(STATUS "Index of 'world' is: ${index}")
```

### 5.7 Lists
Lists in CMake are simply strings separated by semicolons. You can manipulate lists using the `list()` command, which allows appending, removing, or reversing elements.

Example:
```cmake
list(APPEND myList a b c)
message(STATUS "myList: ${myList}")
```

### 5.8 Math
The `math(EXPR)` command allows you to perform basic arithmetic operations on variables.

Example:
```cmake
set(x 5)
set(y 3)
math(EXPR result "${x} + ${y}")
message(STATUS "Result: ${result}")
```

### 5.9 Recommended Practices
- **Use Cache Variables for Optional Features:** Instead of relying on environment variables, prefer cache variables for optional build components. This makes them more accessible and manageable in CMake GUI tools.
- **Naming Conventions:** Establish a clear naming convention, especially for cache variables. Group related variables using a common prefix, which helps in tools like `cmake-gui` that group variables automatically.
- **Use `message()` with Modes:** Always use appropriate modes when logging messages with `message()`. For logs intended to remain part of the project, prefer `STATUS` mode for clarity.

## Project

### Beginner-Friendly CMake Project

This project will guide you through setting up a simple CMake project that includes defining and using variables, handling environment and cache variables, and debugging variables.

#### Project Structure

Create a directory structure for your project:

```
CMakeProject/
├── CMakeLists.txt
└── src/
    └── main.cpp
```

#### Step 1: Writing the `CMakeLists.txt` File

Create a `CMakeLists.txt` file in the `CMakeProject` directory.

```cmake
cmake_minimum_required(VERSION 3.10)

#Project name and version
project(MyCMakeProject VERSION 1.0)

#Define variables
set(SRC_DIR "${CMAKE_SOURCE_DIR}/src")
set(MY_MESSAGE "Hello, CMake!")
set(CPP_STANDARD 11)

#Set C++ standard
set(CMAKE_CXX_STANDARD ${CPP_STANDARD})
set(CMAKE_CXX_STANDARD_REQUIRED True)

#Add executable
add_executable(MyApp ${SRC_DIR}/main.cpp)

#Debugging message
message(STATUS "Source directory: ${SRC_DIR}")
message(STATUS "C++ Standard: ${CPP_STANDARD}")
message(STATUS "Custom message: ${MY_MESSAGE}")

#Environment variable
if(NOT DEFINED ENV{MY_ENV_VAR})
    set(ENV{MY_ENV_VAR} "default_value")
endif()
message(STATUS "Environment variable MY_ENV_VAR: $ENV{MY_ENV_VAR}")

#Cache variable
set(MY_CACHE_VAR "DefaultCacheValue" CACHE STRING "A cache variable example")
message(STATUS "Cache variable MY_CACHE_VAR: ${MY_CACHE_VAR}")
```

#### Step 2: Writing the `main.cpp` File

Create a `main.cpp` file in the `src` directory.

```cpp
#include <iostream>

int main() {
  std::cout << "Welcome to My CMake Project!" << std::endl;
  return 0;
}
```

#### Step 3: Building the Project

Open a terminal and navigate to the `CMakeProject` directory. Run the following commands to configure and build the project:

```sh
mkdir build
cd build
cmake -D MY_CACHE_VAR="MyCustomCacheValue" ..
make
```

- `mkdir build`: Creates a build directory for an out-of-source build.
- `cd build`: Navigates into the build directory.
- `cmake -D MY_CACHE_VAR="MyCustomCacheValue" ..`: Configures the project with a custom cache variable value.
- `make`: Builds the project.

#### Step 4: Running the Project

After building, you can run the executable:

```sh
./MyApp
```

You should see the output:

```
Welcome to My CMake Project!
```

#### Explanation

1. **Project Setup:**
    - `cmake_minimum_required(VERSION 3.10)` specifies the minimum CMake version required.
    - `project(MyCMakeProject VERSION 1.0)` sets the project name and version.

2. **Defining Variables:**
    - `set(SRC_DIR "${CMAKE_SOURCE_DIR}/src")` defines the source directory.
    - `set(MY_MESSAGE "Hello, CMake!")` sets a custom message.
    - `set(CPP_STANDARD 11)` defines the C++ standard.

3. **Using Variables:**
    - `set(CMAKE_CXX_STANDARD ${CPP_STANDARD})` sets the C++ standard for the project.
    - `add_executable(MyApp ${SRC_DIR}/main.cpp)` adds an executable target.

4. **Debugging Variables:**
    - `message(STATUS "Source directory: ${SRC_DIR}")` prints the source directory.
    - `message(STATUS "C++ Standard: ${CPP_STANDARD}")` prints the C++ standard.
    - `message(STATUS "Custom message: ${MY_MESSAGE}")` prints the custom message.

5. **Environment Variables:**
    - `if(NOT DEFINED ENV{MY_ENV_VAR})` checks if the environment variable is not defined.
    - `set(ENV{
  MY_ENV_VAR} "default_value")` sets a default value for the environment variable.
    - `message(STATUS "Environment variable MY_ENV_VAR: $ENV{MY_ENV_VAR}")` prints the environment variable.

6. **Cache Variables:**
    - `set(MY_CACHE_VAR "DefaultCacheValue" CACHE STRING "A cache variable example")` sets a cache variable.
    - `message(STATUS "Cache variable MY_CACHE_VAR: ${MY_CACHE_VAR}")` prints the cache variable.
