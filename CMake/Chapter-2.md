### Setting Up a CMake Project

#### Overview
CMake is a powerful build system generator that helps manage the build process of software projects in a compiler-independent manner. It uses simple configuration files called `CMakeLists.txt` to generate standard build files for a variety of environments. Setting up a project involves creating these configuration files and running CMake to produce the necessary build scripts.

#### In-source Builds
**In-source builds** store both source and build files in the same directory. This is generally discouraged because:
- It clutters the directory with build outputs mixed with source files.
- It complicates version control, as build outputs need to be ignored or manually excluded during commits.
- It can be difficult to clean build outputs and start with a clean source tree.

**Example:**
```sh
# In-source build (not recommended)
cd /path/to/source
cmake .
make
```

#### Out-of-source Builds
**Out-of-source builds** keep source and build directories separate. This approach is preferred as it:
- Keeps directories clean and organized.
- Allows multiple build configurations from the same source directory.
- Avoids the risk of build outputs overwriting source files.

**Example:**
```sh
# Out-of-source build
mkdir build
cd build
cmake ../source
make
```

#### Generating Project Files
CMake generates project files based on a chosen generator. Common generators include:
- Visual Studio
- Xcode
- Unix Makefiles
- Ninja

**Example:**
```sh
# Generate Unix Makefiles
mkdir build
cd build
cmake -G "Unix Makefiles" ../source
```

#### Running the Build Tool
Once CMake generates the project files, you can use the selected build tool to compile the project. CMake can also invoke the build tool for you.

**Example:**
```sh
# Invoke the build tool via CMake
cmake --build . --config Release --target MyApp
```

This command works for various project types, including those typically used through an IDE like Xcode or Visual Studio. For multi-configuration generators, specify the configuration with `--config`.

#### Recommended Practices
- Always use out-of-source builds to keep the source directory clean.
- Create separate build directories for different configurations, such as Debug and Release.
- Consider using different project generators to catch unintended dependencies and verify compiler settings.

**Example:**
```sh
# Debug build
mkdir debug_build
cd debug_build
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug ../source

# Release build
mkdir release_build
cd release_build
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release ../source
```

### Step-by-Step Guide: Setting Up C++ Project with CMake and LLDB in VS Code on macOS

#### Step 1: Create the `.vscode` Directory
1. Open your terminal and navigate to your project folder:
   ```sh
   cd ~/projects/my_cpp_project
   ```
2. Create the `.vscode` directory:
   ```sh
   mkdir .vscode
   ```

#### Step 2: Configure `settings.json`
1. Create and open the `settings.json` file:
   ```sh
   touch .vscode/settings.json
   code .vscode/settings.json
   ```
2. Add the following:
   ```json
   {
       "cmake.sourceDirectory": "${workspaceFolder}",
       "cmake.configureOnOpen": true
   }
   ```
3. Save and close the file.

#### Step 3: Configure `c_cpp_properties.json`
1. Create and open the `c_cpp_properties.json` file:
   ```sh
   touch .vscode/c_cpp_properties.json
   code .vscode/c_cpp_properties.json
   ```
2. Add this configuration:
   ```json
   {
       "configurations": [
           {
               "name": "Mac",
               "includePath": ["${workspaceFolder}/**"],
               "defines": [],
               "macFrameworkPath": ["/System/Library/Frameworks", "/Library/Frameworks"],
               "compilerPath": "/usr/bin/clang",
               "cStandard": "c11",
               "cppStandard": "c++11",
               "intelliSenseMode": "macos-clang-arm64"
           }
       ],
       "version": 4
   }
   ```
3. Save and close the file.

#### Step 4: Configure `launch.json`
1. Create and open the `launch.json` file:
   ```sh
   touch .vscode/launch.json
   code .vscode/launch.json
   ```
2. Add the following configuration:
   ```json
   {
       "version": "0.2.0",
       "configurations": [
           {
               "name": "(lldb) Launch",
               "type": "cppdbg",
               "request": "launch",
               "program": "${workspaceFolder}/build/HelloWorld",
               "stopAtEntry": false,
               "cwd": "${workspaceFolder}",
               "MIMode": "lldb",
               "preLaunchTask": "CMake: build",
               "miDebuggerPath": "/usr/bin/lldb"
           }
       ]
   }
   ```
3. Save and close the file.

#### Step 5: Configure `tasks.json`
1. Create and open the `tasks.json` file:
   ```sh
   touch .vscode/tasks.json
   code .vscode/tasks.json
   ```
2. Add this configuration:
   ```json
   {
       "version": "2.0.0",
       "tasks": [
           {
               "label": "CMake: build",
               "type": "shell",
               "command": "cmake --build build",
               "group": { "kind": "build", "isDefault": true },
               "problemMatcher": ["$gcc"]
           }
       ]
   }
   ```
3. Save and close the file.

#### Step 6: Restart VS Code
- Close and reopen VS Code to apply the changes.

Now that your C++ project is set up in VS Code with CMake and LLDB, the next steps are as follows:

### Step 7: Create a Simple C++ Program

1. In your project folder, create a `src` directory and navigate into it:
   ```sh
   mkdir src
   cd src
   ```
2. Create a simple "Hello World" C++ file:
   ```sh
   touch main.cpp
   code main.cpp
   ```
3. Add the following code to `main.cpp`:
   ```cpp
   #include <iostream>

   int main() {
       std::cout << "Hello, World!" << std::endl;
       return 0;
   }
   ```
4. Save and close the file.

### Step 8: Create a `CMakeLists.txt` File

1. Go back to your project root directory and create a `CMakeLists.txt` file:
   ```sh
   touch CMakeLists.txt
   code CMakeLists.txt
   ```
2. Add the following configuration to your `CMakeLists.txt` file:
   ```cmake
   cmake_minimum_required(VERSION 3.16)
   project(HelloWorld)

   set(CMAKE_CXX_STANDARD 11)

   add_executable(HelloWorld src/main.cpp)
   ```
3. Save and close the file.

### Step 9: Build the Project

1. Back in the terminal, from the root of your project directory, create a `build` folder:
   ```sh
   mkdir build
   cd build
   ```
2. Run CMake to configure your project:
   ```sh
   cmake ..
   ```
3. Build the project using CMake:
   ```sh
   cmake --build .
   ```

### Step 10: Run the Program

1. After the build is complete, run the `HelloWorld` program:
   ```sh
   ./HelloWorld
   ```

<details>
<summary>If you Fail Click Here</summary>
  It looks like the build directory does not contain your `HelloWorld` executable yet. This could happen for a couple of reasons. Let's troubleshoot:

### 1. Ensure `CMakeLists.txt` is Correct

First, check if your `CMakeLists.txt` file includes the following line to build the `HelloWorld` executable:

```cmake
add_executable(HelloWorld src/main.cpp)
```

Make sure that the path to the `main.cpp` file is correct (`src/main.cpp` in this case). If it’s located somewhere else, adjust the path accordingly.

### 2. Re-run CMake in a Clean Build Directory

Sometimes, CMake can fail to regenerate properly. To fix this, delete the contents of the `build` directory and try reconfiguring the project:

1. **Clean the build directory**:
   Navigate to your project root (outside of the `build` directory):

   ```bash
   cd ~/projects/my_cpp_project
   rm -rf build
   ```

2. **Re-create and configure the build**:
   Create a new `build` directory and re-run CMake:

   ```bash
   mkdir build
   cd build
   cmake ..
   ```

3. **Build the project**:
   Once the configuration is done, build the project again:

   ```bash
   cmake --build .
   ```

### 3. Verify Build Output

After the build, check if the executable is in the `build` directory:

```bash
ls
```

You should see `HelloWorld` or an equivalent executable. If you do, run it with:

```bash
./HelloWorld
```

### If Issues Persist

- **Look at the build output**: If CMake produces errors or warnings during configuration or building, share those messages, and I can help troubleshoot further.
- **Check CMake version**: Ensure you're using an appropriate version of CMake (3.16 or higher) that supports the necessary features for your project. You can check the version using:

   ```bash
   cmake --version
   ```

</details>


You should see the output:
```
Hello, World!
```

### Step 11: Debug with LLDB

1. Open VS Code.
2. Press `F5` to start debugging. This will launch the LLDB debugger with the `launch.json` configuration.
3. You can now set breakpoints, step through code, and debug as needed.

