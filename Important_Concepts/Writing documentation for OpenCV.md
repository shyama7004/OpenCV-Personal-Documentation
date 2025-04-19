# OpenCV with Doxygen

## Overview of Doxygen

### Introduction

`Doxygen` is a powerful tool designed for generating documentation from annotated source code. It offers several features that make it an excellent choice for creating detailed and accurate documentation for your projects. Some of its key features include:

- **Source Parsing:** Doxygen can parse your program's source code to produce documentation that accurately reflects the code's structure and contents.
- **Error Checking:** It checks the documentation for errors, ensuring that your comments are correctly formatted and comprehensive.
- **Rich Media Support:** You can insert images and formulas into your documentation, making it more informative and visually appealing.
- **Flexible Formatting:** Doxygen supports markdown syntax and plain HTML, allowing for precise text formatting.
- **Multiple Output Formats:** It can generate documentation in various formats, including HTML, PDF, and LaTeX.

The existing documentation for the OpenCV library has been converted to the Doxygen format, ensuring consistency and accessibility.

## Installation

To install Doxygen, please refer to the official [download](https://doxygen.nl/download.html) and [installation](https://doxygen.nl/manual/install.html) pages. Many Linux distributions also provide Doxygen packages, which can be installed using the distribution's package manager.

## Generating Documentation

To generate the OpenCV documentation using Doxygen, follow these detailed steps:

### Step-by-Step Instructions

1. **Get the OpenCV Sources:**

   - Download the OpenCV source code (version 3.0 or later). This is the foundational codebase you will be documenting.
   - Optionally, download the `opencv_contrib` sources, which include additional modules that extend the functionality of OpenCV.

2. **Create a Build Directory:**

   - Navigate to the directory containing the OpenCV sources. It's good practice to create a separate build directory to keep the build files separate from the source files.
     ```bash
     mkdir build
     cd build
     ```

3. **Run CMake:**

   - Use CMake to configure the build system and generate the necessary makefiles. This step also prepares the project to build the documentation.
   - If you're only using the OpenCV sources, run:
     ```bash
     cmake -DBUILD_DOCS=ON ../opencv
     ```
   - If you're using both OpenCV and `opencv_contrib` sources, run:
     ```bash
     cmake -DBUILD_DOCS=ON -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules ../opencv
     ```

4. **Installing Doxygen:**
   ```
      brew install doxygen
   ```
5. **Run Make to Generate Documentation:**

   - Use the `make` command to build the Doxygen documentation.
     ```bash
     make doxygen
     ```

6. **Open the Generated Documentation:**

   - The documentation will be generated in HTML format and can be found in the `doc/doxygen/html` directory. Open the `index.html` file in your web browser to view the documentation.

7. **Test Your Python Code with Pylint:**
   - Run the following command to check your Python code:
     ```bash
     make check_pylint
     ```

### Installing Pylint

If Pylint is not already installed, you can install it using pip:

```bash
pip install pylint
```

### Executing the Steps in Your Terminal

1. Open a terminal and navigate to the directory with the OpenCV sources.
2. Create the build directory and navigate into it:
   ```bash
   mkdir build
   cd build
   ```
3. Run CMake to configure the project:
   ```bash
   cmake -DBUILD_DOCS=ON ..(Your file location upto opencv)/opencv
   ```
   or, if you have the `opencv_contrib` modules:
   ```bash
   cmake -DBUILD_DOCS=ON -DOPENCV_EXTRA_MODULES_PATH=../opencv_contrib/modules ../opencv
   ```
4. Generate the documentation:
   ```bash
   make doxygen
   ```
5. Open the generated documentation:
   ```bash
   open doc/doxygen/html/index.html
   ```
6. Test your Python code:
   ```bash
   make check_pylint
   ```

## Quick Start

### Note (C++):

These instructions are tailored for documenting the OpenCV library. Different projects might have different layouts and documentation conventions.

### Documentation Locations

The OpenCV documentation is gathered from various sources, organized into different sections:

- **Source Code Entities:** Classes, functions, or enumerations should be documented in the corresponding header files, right before their definitions. Examples of this are provided in the following sections.
- **Pages:** For large pieces of text, images, and code examples not directly connected to any source code entity. Pages should be in separate files located in predefined places. This tutorial is an example of such a page.
- **Images:** Used to illustrate described concepts. Typically located in the same places as pages, images can be inserted anywhere in the documentation.
- **Code Examples:** Show how to use the library in real applications. Each example is a self-contained file representing one simple application. Parts of these files can be included in documentation and tutorials to demonstrate function calls and object interactions.
- **BibTeX References:** Used to create a common bibliography. All science books, articles, and proceedings that served as the basis for the library's functionality should be included in this reference list.

### Common Documentation Locations in the OpenCV Repository:

```
<opencv>
├── doc - Doxygen config files, root page (root.markdown.in), BibTeX file (opencv.bib)
│   ├── tutorials - Tutorials hierarchy (pages and images)
│   └── py_tutorials - Python tutorials hierarchy (pages and images)
├── modules
│   └── <modulename>
│      ├── doc - Documentation pages and images for the module
│      └── include - Code documentation in header files
└── samples - Place for all code examples
 ├── cpp
 │ └── tutorial_code - Place for tutorial code examples
 └── ...
```

### Automatic Code Parser:

The automatic code parser looks for all header files (`.h`, `.hpp`, except for `.inl.hpp`, `.impl.hpp`, `_detail.hpp`) in the include folder and its subfolders. Some module-specific instructions (group definitions) and documentation should be placed in the `include/opencv2/<module-name>.hpp` file. C++ template implementations and specializations can be placed in separate files (`.impl.hpp`) ignored by Doxygen.

Files in the `src` subfolder are not parsed, as documentation is mainly intended for library users, not developers. However, it's possible to generate full documentation by customizing the list of processed files in the CMake script (`doc/CMakeLists.txt`) and Doxygen options in its configuration file (`doc/Doxyfile.in`).

### Layout for New Modules in the `opencv_contrib` Repository:

```
<opencv_contrib>
└── modules
 └── <modulename>
 ├── doc - Documentation pages and images, BibTeX file (<modulename>.bib)
 ├── include - Code documentation in header files
 ├── samples - Code examples for documentation and tutorials
 └── tutorials - Tutorial pages and images
```

### Adding Documentation for Functions, Classes, and Other Entities:

Insert special comments before the definition of each entity. For example:

```cpp
/** @brief Calculates the exponent of every array element.

The function exp calculates the exponent of every element of the input array:
\f[ \texttt{dst} [I] = e^{ src(I) } \f]

The maximum relative error is about 7e-6 for single-precision input and less than 1e-10 for
double-precision input. Currently, the function converts denormalized values to zeros on output.
Special values (NaN, Inf) are not handled.

@param src input array.
@param dst output array of the same size and type as src.

@sa log , cartToPolar , polarToCart , phase , pow , sqrt , magnitude
*/
CV_EXPORTS_W void exp(InputArray src, OutputArray dst);
```

### Key Elements in the Example Above:

- **Special C-Comment Syntax:** `/** ... */` denotes it is a Doxygen comment.
- **@brief Command:** Brief description of the function.
- **Empty Line:** Denotes the end of the paragraph.
- **TeX Formula:** Between `\f[ ... \f]` commands.
- **@param Command:** Describes the function's parameters.
- **@sa Command:** "See also" paragraph containing references to related classes, methods, pages, or URLs.

### One-Line Short Comments:

For one-line comments, you can use a different syntax:

```cpp
//! type of line
enum LineTypes {
    FILLED  = -1,
    LINE_4  = 4, //!< 4-connected line
    LINE_8  = 8, //!< 8-connected line
    LINE_AA = 16 //!< antialiased line
};
```

### Additional Syntax Details:

- **Command Prefix:** Doxygen commands start with `@` or `\`:
  - `@brief ...`
  - `\brief ...`
- **Comment Syntax:** Various forms include:
  - C-style: `/** ... */` or `/*! ... */`
  - C++-style: `//! ...` or `/// ...`
  - Lines starting with `*`:
    ```cpp
    /**
     * ...
     * ...
     */
    ```
  - After documented entity:
    - `//!< ...`
    - `/**< ... */`
- **Paragraph End:** Insert an empty line or a new paragraph command:

  ```cpp


  @brief This is the first paragraph.
  @brief This is the second paragraph.
  ```

- **TeX Support:** Math formulas should be between `\f[ ... \f]` or `\f$ ... \f$` commands, with more control provided by the built-in MathJax support.

### Doxygen Tag Reference:

#### List of Main Tags:

| Command          | Description                                          |
| ---------------- | ---------------------------------------------------- |
| `@addtogroup`    | Adds documentation to a group                        |
| `@anchor`        | Defines an anchor that can be referenced             |
| `@brief`         | Brief description of an item                         |
| `@bug`           | Describes a bug related to the documented item       |
| `@code`          | Starts a code block                                  |
| `@endcode`       | Ends a code block                                    |
| `@details`       | Detailed description of an item                      |
| `@deprecated`    | Marks the item as deprecated                         |
| `@example`       | Includes an example source code file                 |
| `@exception`     | Describes exceptions thrown by a function            |
| `@file`          | Documents a file                                     |
| `@ingroup`       | Adds the item to a group                             |
| `@link`          | Creates a link to another item                       |
| `@note`          | Adds a note                                          |
| `@param`         | Documents a parameter                                |
| `@return`        | Describes the return value of a function             |
| `@see`           | Links to another item for further reference          |
| `@since`         | Documents when the item was added                    |
| `@throw`         | Describes exceptions thrown by a function            |
| `@todo`          | Adds a to-do item                                    |
| `@warning`       | Adds a warning message                               |
| `@author`        | Author of the item                                   |
| `@date`          | Date when the item was documented                    |
| `@version`       | Version of the item                                  |
| `@defgroup`      | Defines a group                                      |
| `@section`       | Starts a new section                                 |
| `@subsection`    | Starts a new subsection                              |
| `@param[in]`     | Describes an input parameter                         |
| `@param[out]`    | Describes an output parameter                        |
| `@param[in,out]` | Describes a parameter used for both input and output |
| `@f$ ... \f$`    | Inline formula                                       |
| `@f[ ... \f]`    | Display formula                                      |
| `@image`         | Embeds an image                                      |
| `@include`       | Includes a file                                      |
| `@internal`      | Marks the item as internal, not for public use       |
| `@interface`     | Documents an interface                               |
| `@var`           | Documents a variable                                 |

For comprehensive details on all available Doxygen commands, please refer to the official [Doxygen documentation](https://www.doxygen.nl/manual/commands.html).
