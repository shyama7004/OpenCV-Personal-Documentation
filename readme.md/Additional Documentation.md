### Custom Pages in Doxygen

Doxygen allows you to create custom pages for your documentation that are not part of the API of your library or program. These pages can be used to add any additional information that might be useful to the users of your documentation.

#### Creating Custom Pages

To create custom pages, you can use files with the following extensions: `.dox`, `.txt`, or `.md`. 

- **.dox or .txt files:** Treated as C/C++ source files by Doxygen.
- **.md files:** Treated as Markdown files by Doxygen.

#### Using .dox or .txt Files

You can use a single Doxygen comment to define your custom page. For example, to create a main page for your library manual:

**manual/index.dox**
```cpp
/** \mainpage My Library Manual
- Building
- Basics
- Examples
*/
```
- The `\mainpage` command is used to designate this page as the main page.
- For other custom pages, use the `\page` command.

#### Including Custom Pages in Documentation

By default, Doxygen does not include custom files. To include them, you need to specify the custom files in the `INPUT` attribute of your Doxyfile:

**Doxyfile**
```plaintext
INPUT = manual/index.dox
```

#### Adding Detailed Instructions

You can create additional custom pages with detailed instructions. For example, to add instructions on how to build your project:

**manual/building/index.dox**
```cpp
/** \page Building

<h2>Linux</h2>
<p>
  A simple way to build this project is with cmake, clone the repository, cd into the root of the project and run:
</p>

<pre>
  <code>
    mkdir my_build
    cmake -S . -B my_build
    cd my_build
    cmake --build .
  </code>
</pre>

*/
```

You can also use Markdown notation in a `.md` file:

**manual/building/index.md**
```markdown
# Building

## Linux

A simple way to build this project is with cmake, clone the repository,
cd into the root of the project and run:

    mkdir my_build
    cmake -S . -B my_build
    cd my_build
    cmake --build .
```

#### Scaling Up Documentation

As your manual grows, you can include entire directories instead of individual files:

**Doxyfile**
```plaintext
INPUT = manual/
RECURSIVE = YES
```
- This ensures all subdirectories under `manual/` are included.

#### Generating a Side Panel Treeview

To improve navigation in your documentation, enable the tree view in the side panel:

**Doxyfile**
```plaintext
GENERATE_TREEVIEW = YES
```
- Use the `\ref` command to create links between topics, which will help populate the tree view.

By following these steps, you can create a well-organized, scalable manual to enrich your documentation and make it easier for users to navigate and understand your project.
