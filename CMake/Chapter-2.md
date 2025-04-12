# Setting Up a CMake Project

This guide provides a comprehensive walkthrough of configuring a CMake project, detailing both basic build strategies and an in-depth example of setting up a C++ project with CMake and the LLDB debugger in Visual Studio Code on macOS.

## Overview

CMake is an advanced build system generator designed to manage the build process in a compiler-independent fashion. Its simple configuration files, known as `CMakeLists.txt`, allow you to generate native build scripts (e.g., Makefiles, Visual Studio solutions) tailored to your target environment. Using CMake brings several benefits:

- **Portability:** Write a single configuration file to generate build scripts for various operating systems and compilers.
- **Flexibility:** Manage complex build configurations and dependencies with relative ease.
- **Maintainability:** Keep your source tree organized, particularly when using out-of-source builds.

---

## Build Strategies

### In-source Builds

An **in-source build** occurs when build files and source files reside within the same directory. Although simple in setup, this strategy has notable drawbacks:

- **Directory Clutter:** Build outputs mix with source files, making the project directory messy.
- **Version Control Issues:** Build artifacts can inadvertently be committed unless explicitly excluded.
- **Clean-up Complexity:** Removing build outputs might risk accidentally deleting important source files.

**Example of an In-source Build (Not Recommended):**
```sh
# Navigate to the source directory
cd /path/to/source
# Run CMake to configure and generate the build files in place
cmake .
# Build the project
make
```

### Out-of-source Builds

An **out-of-source build** involves creating a separate build directory. This method is highly recommended because:

- **Cleaner Organization:** Keeps the source directory free from generated build files.
- **Multiple Configurations:** Allows you to maintain distinct build directories (e.g., Debug and Release) for various configurations.
- **Safe Clean-ups:** You can remove the entire build directory without affecting the source code.

**Example of an Out-of-source Build:**
```sh
# Create and navigate to a build directory outside the source tree
mkdir build
cd build
# Run CMake, pointing to the source directory
cmake ../source
# Build the project
make
```

---

## Generating Project Files with CMake

CMake supports various native build systems and can generate project files using a generator of your choice. Common generators include:

- **Visual Studio:** For Windows development.
- **Xcode:** For macOS development.
- **Unix Makefiles:** A default option on Linux and many Unix-like systems.
- **Ninja:** A fast build system focused on speed and simplicity.

**Example: Generating Unix Makefiles**
```sh
mkdir build
cd build
cmake -G "Unix Makefiles" ../source
```

Once the project files are created, you can either invoke the build tool directly or delegate the task back to CMake:

**Example: Building the Project via CMake**
```sh
cmake --build . --config Release --target MyApp
```
For multi-configuration projects (e.g., Visual Studio, Xcode), remember to specify the configuration with the `--config` flag.

---

## Best Practices for CMake Projects

When working with CMake, keep these practices in mind:

- **Prefer Out-of-source Builds:** Always configure your projects in a separate directory to maintain a clean source tree.
- **Organize Build Configurations:** Use different directories (e.g., `debug_build`, `release_build`) for different build types.
- **Cross-platform Testing:** Try different generators to ensure your project is robust and does not rely on generator-specific behavior.

**Example: Separate Debug and Release Builds**
```sh
# Debug build configuration
mkdir debug_build
cd debug_build
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Debug ../source

# Release build configuration
mkdir release_build
cd release_build
cmake -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release ../source
```

---

# Step-by-Step Guide: Configuring a C++ Project with CMake and LLDB in VS Code on macOS

This section walks you through setting up a simple C++ project in Visual Studio Code using CMake, with LLDB as the debugger.

## Step 1: Create the `.vscode` Directory

1. **Navigate to Your Project Folder:**
   ```sh
   cd ~/projects/my_cpp_project
   ```
2. **Create the `.vscode` Configuration Directory:**
   ```sh
   mkdir .vscode
   ```

## Step 2: Configure `settings.json`

1. **Create and Open `settings.json`:**
   ```sh
   touch .vscode/settings.json
   code .vscode/settings.json
   ```
2. **Add the Following Configuration:**
   ```json
   {
       "cmake.sourceDirectory": "${workspaceFolder}",
       "cmake.configureOnOpen": true
   }
   ```
   This configuration tells VS Code to treat your workspace folder as the CMake source directory and to automatically run CMake when the workspace opens.

## Step 3: Configure `c_cpp_properties.json`

1. **Create and Open `c_cpp_properties.json`:**
   ```sh
   touch .vscode/c_cpp_properties.json
   code .vscode/c_cpp_properties.json
   ```
2. **Insert the Following Configuration:**
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
   This file configures IntelliSense for macOS, setting up include paths, compiler paths, and target C/C++ standards.

## Step 4: Configure `launch.json`

1. **Create and Open `launch.json`:**
   ```sh
   touch .vscode/launch.json
   code .vscode/launch.json
   ```
2. **Add the Debug Configuration:**
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
   This configuration sets up VS Code to launch the LLDB debugger for your application.

## Step 5: Configure `tasks.json`

1. **Create and Open `tasks.json`:**
   ```sh
   touch .vscode/tasks.json
   code .vscode/tasks.json
   ```
2. **Define the Build Task:**
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
   This task file defines a shell command that triggers a CMake build. The task is also set as the default build task.

## Step 6: Restart VS Code

To ensure all changes take effect, close and reopen Visual Studio Code.

---

# Creating and Building a Simple C++ Program

## Step 7: Create a Simple C++ Program

1. **Set Up the Source Directory:**
   ```sh
   mkdir src
   cd src
   ```
2. **Create `main.cpp`:**
   ```sh
   touch main.cpp
   code main.cpp
   ```
3. **Insert the Following Code:**
   ```cpp
   #include <iostream>

   int main() {
       std::cout << "Hello, World!" << std::endl;
       return 0;
   }
   ```
4. **Save and Close the File.**

## Step 8: Create the `CMakeLists.txt` File

1. **Return to the Project Root and Create the File:**
   ```sh
   touch CMakeLists.txt
   code CMakeLists.txt
   ```
2. **Add This CMake Configuration:**
   ```cmake
   cmake_minimum_required(VERSION 3.16)
   project(HelloWorld)

   set(CMAKE_CXX_STANDARD 11)

   add_executable(HelloWorld src/main.cpp)
   ```
3. **Save and Close the File.**

## Step 9: Build the Project

1. **Create the Build Directory:**
   ```sh
   mkdir build
   cd build
   ```
2. **Configure the Project with CMake:**
   ```sh
   cmake ..
   ```
3. **Build the Project:**
   ```sh
   cmake --build .
   ```

After the build completes, you should have an executable named `HelloWorld` in your build directory.

## Step 10: Run the Program

Test your build by running the executable:
```sh
./HelloWorld
```
You should see the following output:
```
Hello, World!
```

---

# Troubleshooting: Common Build Issues

If you encounter issues where the `HelloWorld` executable is not present in the build directory, consider the following troubleshooting steps:

### 1. Verify `CMakeLists.txt`
- Ensure that the `CMakeLists.txt` contains the correct paths and the `add_executable` command is properly configured:
  ```cmake
  add_executable(HelloWorld src/main.cpp)
  ```

### 2. Clean the Build Directory and Rebuild
- Remove the existing build directory and re-run the CMake configuration:
  ```sh
  cd ~/projects/my_cpp_project
  rm -rf build
  mkdir build
  cd build
  cmake ..
  cmake --build .
  ```

### 3. Review Build Output
- Carefully check the terminal output for any configuration errors or warnings that might indicate the source of the problem.

---

# Step 11: Debug with LLDB in VS Code

With the configuration complete, you are ready to start debugging:

1. **Launch Visual Studio Code.**
2. **Press `F5` to start debugging.**
   - VS Code will launch LLDB based on the settings in `launch.json`.
3. **Set Breakpoints and Step Through Your Code.**
   - Use the built-in debugging tools in VS Code to inspect variables, step through code, and analyze program behavior.

---

## Conclusion

This guide has walked you through:
- Setting up a clean, organized CMake project.
- Differentiating between in-source and out-of-source builds.
- Configuring Visual Studio Code for a C++ project with debugging capabilities using LLDB on macOS.
- Troubleshooting common build issues.

By following these detailed instructions, you can maintain an organized development environment and streamline your build and debugging processes across multiple configurations and platforms.
