### 1. **Setting Up a Minimal Project**

Every CMake project starts with a file named **`CMakeLists.txt`**, placed at the root of your source directory. Think of this as the project file that defines all the instructions for building, testing, and packaging your code.

Here’s a simple **CMakeLists.txt** structure:

```cmake
cmake_minimum_required(VERSION 3.2)
project(MyApp)
add_executable(myExe main.cpp)
```

**Key Commands:**
- **`cmake_minimum_required(VERSION X.Y.Z)`**: This command specifies the minimum CMake version required to build the project. It’s essential to prevent compatibility issues if someone uses an older version of CMake.
- **`project()`**: This command sets up your project name and can also specify the programming languages used, such as C++ or C. It should appear right after the `cmake_minimum_required()` command.
- **`add_executable(targetName source1 source2)`**: This creates an executable. In this example, it turns the source file **`main.cpp`** into an executable named **`myExe`**.

---

### 2. **Managing CMake Versions**

CMake evolves over time, introducing new features and behavior changes. By setting **`cmake_minimum_required()`**, you can ensure that your project works with the specified CMake version.

- **Best practice**: For new projects, it’s a good idea to use a relatively recent version, such as **CMake 3.2** or higher. This ensures you have access to modern features, making your project more adaptable.

---

### 3. **Defining the Project with `project()`**

The **`project()`** command does much more than just give your project a name:
- **Languages**: You can specify programming languages like C, C++, or Fortran. If not specified, CMake assumes you're using C and C++.
- **Versioning**: If you provide a version in **`project(MyApp VERSION 1.0)`**, CMake uses this version throughout your project to set variables and generate package metadata.

### Example:
```cmake
project(MyApp VERSION 1.0 LANGUAGES CXX)
```

---

### 4. **Building an Executable**

To build an executable from your source files:
1. **Add the executable**:
   ```cmake
   add_executable(myApp main.cpp)
   ```
   This command instructs CMake to compile **`main.cpp`** and generate an executable named **`myApp`**.
   
2. **Multiple Source Files**: If your project has multiple source files, list them all:
   ```cmake
   add_executable(myApp main.cpp file1.cpp file2.cpp)
   ```

---

### 5. **Commenting in CMake**

Commenting in CMake is similar to how you comment in most scripting languages:
- Any line that begins with **`#`** is treated as a comment.
  
Example:
```cmake
# This is a comment
add_executable(myApp main.cpp)  # Another comment here
```

---

### 6. **Recommended Practices**

- **Always start your project with `cmake_minimum_required()`**. This ensures your project is future-proof and compatible with modern CMake features.
- **Use a recent CMake version** to avoid compatibility problems with newer platforms and tools.
- **Version your project** early. This helps manage releases and makes it easier for others to track changes.

