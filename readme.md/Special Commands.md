## `Left at :` \defgroup <name> (group title)
### Introduction

All commands in the documentation start with a backslash \ or an at-sign (@). If you prefer, you can replace all commands starting with a backslash below with their counterparts that start with an at-sign.

Some commands have one or more arguments. Each argument has a certain range:

- **Sharp Braces (<>)**: The argument is a single word.
- **Round Braces (())**: The argument extends until the end of the line on which the command was found.
- **Curly Braces ({})**: The argument extends until the next paragraph. Paragraphs are delimited by a blank line or by a section indicator. Note that curly braces are also used for command options; here, the braces are mandatory and are just 'normal' characters. The starting curly brace has to directly follow the command, without whitespace.
- **Square Brackets ([])**: The argument is optional unless they are placed between quotes; in that case, they are a mandatory part of the command argument.

### Commands List (Alphabetically Sorted)

#### Structural Indicators

- **\addtogroup <name> [(title)]**
  - **Description**: Defines a group, similar to \defgroup, but using the same <name> more than once will not result in a warning. Instead, it will create one group with merged documentation and the first title found.
  - **Title**: Optional. If provided, it is used as the title of the group.
  - **Usage**: This command can add multiple entities to an existing group using @{ and @}.
  - **Example**:
    ```cpp
    /*! \addtogroup mygrp
     *  Additional documentation for group 'mygrp'
     *  @{
     */

    /*!
     *  A function
     */
    void func1()
    {
    }

    /*! Another function */
    void func2()
    {
    }

    /*! @} */
    ```
  - **References**:
    - Section: Grouping
    - Commands: \defgroup, \ingroup, \weakgroup

- **\callgraph**
  - **Description**: Generates a call graph for a function or method if HAVE_DOT is set to YES, provided the implementation calls other documented functions.
  - **Condition**: The call graph will be generated regardless of the value of CALL_GRAPH.
  - **Note**: The completeness and correctness of the call graph depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \callergraph
    - Commands: \hidecallgraph, \hidecallergraph
    - Option: CALL_GRAPH

- **\hidecallgraph**
  - **Description**: Prevents the generation of a call graph for a function or method.
  - **Condition**: The call graph will not be generated regardless of the value of CALL_GRAPH.
  - **Note**: The completeness and correctness of the call graph depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \callergraph
    - Commands: \callgraph, \hidecallergraph
    - Option: CALL_GRAPH

- **\callergraph**
  - **Description**: Generates a caller graph for a function or method if [HAVE_DOT](https://github.com/shyama7004/OpenCV-Personal-Documentation-MacOS-/blob/main/readme.md/Other%20Defs/HAVE_DOT.md) is set to YES, provided the function is called by other documented functions.
  - **Condition**: The caller graph will be generated regardless of the value of CALLER_GRAPH.
  - **Note**: The completeness and correctness of the caller graph depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \callgraph
    - Commands: \hidecallgraph, \hidecallergraph
    - Option: CALLER_GRAPH

  `Example:`
  ```cpp
  \callgraph {
  function1() {
    // code here
  }
  function2() {
    // code here
  }
  function3() {
    // code here
  }
  }
  ```

- **\hidecallergraph**
  - **Description**: Prevents the generation of a caller graph for a function or method.
  - **Condition**: The caller graph will not be generated regardless of the value of CALLER_GRAPH.
  - **Note**: The completeness and correctness of the caller graph depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \callergraph
    - Commands: \callgraph, \hidecallgraph
    - Option: CALLER_GRAPH

- **\showrefby**
  - **Description**: Generates an overview of the documented functions and methods that call or use a specified function, method, or variable.
  - **Condition**: The overview will be generated regardless of the value of REFERENCED_BY_RELATION.
  - **Note**: The completeness and correctness of the overview depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \showrefs
    - Commands: \hiderefby, \hiderefs
    - Option: REFERENCED_BY_RELATION

- **\hiderefby**
  - **Description**: Prevents the generation of an overview of the documented functions and methods that call or use a specified function, method, or variable.
  - **Condition**: The overview will not be generated regardless of the value of REFERENCED_BY_RELATION.
  - **Note**: The completeness and correctness of the overview depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \showrefs
    - Commands: \showrefby, \hiderefs
    - Option: REFERENCED_BY_RELATION

- **\showrefs**
  - **Description**: Generates an overview of the functions and methods that call a specified function or method.
  - **Condition**: The overview will be generated regardless of the value of REFERENCES_RELATION.
  - **Note**: The completeness and correctness of the overview depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \showrefby
    - Commands: \hiderefby, \hiderefs
    - Option: REFERENCES_RELATION

- **\hiderefs**
  - **Description**: Prevents the generation of an overview of the functions and methods that call a specified function or method.
  - **Condition**: The overview will not be generated regardless of the value of REFERENCES_RELATION.
  - **Note**: The completeness and correctness of the overview depend on the Doxygen code parser, which is not perfect.
  - **References**:
    - Section: \showrefs
    - Commands: \showrefby, \hiderefby
    - Option: REFERENCES_RELATION

- **\showinlinesource**
  - **Description**: Generates the inline source for a function, multi-line macro, enum, or a list-initialized variable.
  - **Condition**: The inline source will be generated regardless of the value of INLINE_SOURCES.
  - **References**:
    - Section: \hideinlinesource
    - Option: INLINE_SOURCES

- **\hideinlinesource**
  - **Description**: Prevents the generation of the inline source for a function, multi-line macro, enum, or a list-initialized variable.
  - **Condition**: The inline source will not be generated regardless of the value of INLINE_SOURCES.
  - **References**:
    - Section: \showinlinesource
    - Option: INLINE_SOURCES

- **\includegraph**
  - **Description**: Generates an include graph for a file.
  - **Condition**: The include graph will be generated regardless of the value of INCLUDE_GRAPH.
  - **References**:
    - Section: \hideincludegraph, \includedbygraph, \hideincludedbygraph
    - Option: INCLUDE_GRAPH
    - `Exmaple :` Pie chart, Histograms, Line Graphs, Bar Graphs

- **\hideincludegraph**
  - **Description**: Prevents the generation of an include graph for a file.
  - **Condition**: The include graph will not be generated regardless of the value of INCLUDE_GRAPH.
  - **References**:
    - Section: \includegraph, \includedbygraph, \hideincludedbygraph
    - Option: INCLUDE_GRAPH

- **\includedbygraph**
  - **Description**: Generates an included-by graph for an include file.
  - **Condition**: The included-by graph will be generated regardless of the value of INCLUDED_BY_GRAPH.
  - **References**:
    - Section: \hideincludedbygraph, \includegraph, \hideincludegraph
    - Option: INCLUDED_BY_GRAPH

- **\hideincludedbygraph**
  - **Description**: Prevents the generation of an included-by graph for an include file.
  - **Condition**: The included-by graph will not be generated regardless of the value of INCLUDED_BY_GRAPH.
  - **References**:
    - Section: \includedbygraph, \includegraph, \hideincludegraph
    - Option: INCLUDED_BY_GRAPH

- **\directorygraph**
  - **Description**: Generates a directory graph for a directory.
  - **Condition**: The directory graph will be generated regardless of the value of DIRECTORY_GRAPH.
  - **References**:
    - Section: \hidedirectorygraph
    - Option: DIRECTORY_GRAPH

- **\hidedirectorygraph**
  - **Description**: Prevents the generation of a directory graph for a directory.
  - **Condition**: The directory graph will not be generated regardless of the value of DIRECTORY_GRAPH.
  - **References**:
    - Section: \directorygraph
    - Option: DIRECTORY_GRAPH

- **\collaborationgraph**
  - **Description**: Generates a collaboration graph for a class.
  - **Condition**: The collaboration graph will be generated regardless of the value of COLLABORATION_GRAPH.
  - **References**:
    - Section: \hidecollaborationgraph
    - Option: COLLABOR
   
- **\hidecollaborationgraph**
  - **Description**: Prevents the generation of a collaboration graph for a class.
  - **Condition**: The collaboration graph will not be generated regardless of the value of `COLLABORATION_GRAPH`.
  - **References**:
    - Section: \collaborationgraph
    - Option: `COLLABORATION_GRAPH`

- **\inheritancegraph ['{option}']**
  - **Description**: Generates an inheritance graph for a class conforming to the specified option.
  - **Condition**: The inheritance graph will be generated regardless of the value of `CLASS_GRAPH`. The possible values for the option are the same as those used with `CLASS_GRAPH`. If no option is specified, `YES` is assumed.
  - **References**:
    - Section: \hideinheritancegraph
    - Option: `CLASS_GRAPH`

- **\hideinheritancegraph**
  - **Description**: Prevents the generation of an inheritance graph for a class.
  - **Condition**: The inheritance graph will not be generated regardless of the value of `CLASS_GRAPH`.
  - **References**:
    - Section: \inheritancegraph
    - Option: `CLASS_GRAPH`

- **\groupgraph**
  - **Description**: Generates a group dependency graph for a group.
  - **Condition**: The group graph will be generated regardless of the value of `GROUP_GRAPHS`.
  - **References**:
    - Section: \hidegroupgraph
    - Option: `GROUP_GRAPHS`

- **\hidegroupgraph**
  - **Description**: Prevents the generation of a group dependency graph for a group.
  - **Condition**: The group graph will not be generated regardless of the value of `GROUP_GRAPHS`.
  - **References**:
    - Section: \groupgraph
    - Option: `GROUP_GRAPHS`

- `\qualifier <label> | "(text)"`
  - **Description**: Adds custom qualifier labels to members and classes, similar to automatically generated labels like "static," "inline," and "final."
  - **Example**: To indicate a function meant only for testing purposes, use `\qualifier test`.

- `\category <name> [<header-file>] [<header-name>]`
  - **Description**: For Objective-C only: Indicates that a comment block contains documentation for a class category with the specified name. The arguments are the same as for the \class command.
  - **References**:
    - Section: \class

- `\class <name> [<header-file>] [<header-name>]`
  - **Description**: Indicates that a comment block contains documentation
    for a class with the specified name. Optionally, a header file and a
    header name can be specified. If the header-file is specified, a link
    to a verbatim copy of the header will be included in the HTML documentation.
    The header-name argument can be used to overwrite the name of the link used
    in the class documentation to something other than header-file. This is
    useful if the include name is not located on the default include path.
    With the header-name argument, you can also specify how the include
    statement should look by adding either quotes or sharp brackets around
    the name. Sharp brackets are used if just the name is given.

    `Note` that the last two arguments can also be specified using the
    `\headerfile` command.
    
  - **Example**:
    ```cpp
    /* A dummy class */
    class Test
    {
    };
    
    /*! \class Test class.h "inc/class.h"
     *  \brief This is a test class.
     *
     * Some details about the Test class.
     */
    ```

- `\concept <name>`
  - **Description**: Indicates that a comment block contains documentation for a C++20 concept with the specified name.
  - **References**: \headerfile

- `\def <name>`
  - **Description**: Indicates that a comment block contains documentation
    for a #define macro.
    
  - **Example**:
    ```cpp
    /*! \file define.h
        \brief testing defines
        
        This is to test the documentation of defines.
    */
    
    /*!
      \def MAX(x,y)
      Computes the maximum of \a x and \a y.
    */
    
    /*! 
       \brief Computes the absolute value of its argument \a x.
       \param x input value.
       \returns absolute value of \a x.
    */
    #define ABS(x) (((x)>0)?(x):-(x))
    #define MAX(x,y) ((x)>(y)?(x):(y))
    #define MIN(x,y) ((x)>(y)?(y):(x))
    ```

- `\defgroup <name> (group title)`
  - **Description**: Indicates that a comment block contains documentation
    for a group of classes, modules, concepts, files, or namespaces.
    This can be used to categorize symbols and document those categories.
    You can also use groups as members of other groups, thus building a
    hierarchy of groups.
    
  - **References**:
    - Page: Grouping
    - Sections: \ingroup, \addtogroup, \weakgroup

```cpp
/**
 * \defgroup my_group My Group Title
 * \ingroup my_group
 */
```

- `\dir [<path fragment>]`
  
  - **Description**: Indicates that a comment block contains documentation
    for a directory. The "path fragment" argument should include the directory
    name and enough of the path to be unique with respect to the other
    directories in the project. The `STRIP_FROM_PATH` option determines what
    is stripped from the full path before it appears in the output.

- `\enum <name>`
  - **Description**: Indicates that a comment block contains documentation for an enumeration with the specified name. If the enum is a member of a class and the documentation block is located outside the class definition, the scope of the class should be specified as well. If a comment block is located directly in front of an enum declaration, the \enum comment may be omitted.
  - **Note**: The type of an anonymous enum cannot be documented, but the values of an anonymous enum can.
  - **Example**:
    ```cpp
    class Enum_Test
    {
      public:
        enum TEnum { Val1, Val2 };
     
        /*! Another enum, with inline docs */
        enum AnotherEnum 
        { 
          V1, /*!< value 1 */
          V2  /*!< value 2 */
        };
    };
     
    /*! \class Enum_Test
     * The class description.
     */
     
    /*! \enum Enum_Test::TEnum
     * A description of the enum type.
     */
     
    /*! \var Enum_Test::TEnum Enum_Test::Val1
     * The description of the first enum value.
     */
    ```

- **\example ['{lineno}'] <file-name>**
  - **Description**: Indicates that a comment block contains documentation for a source code example. The name of the source file is <file-name>. The contents of this file will be included in the documentation, just after the documentation contained in the comment block. You can add the option `{lineno}` to enable line numbers for the example if desired. All examples are placed in a list. The source code is scanned for documented members and classes. If any are found, the names are cross-referenced with the documentation. Source files or directories can be specified using the `EXAMPLE_PATH` tag of Doxygen's configuration file.
  - **Example**:
    ```cpp
    /** A Example_Test class.
     *  More details about this class.
     */
    
    class Example_Test
    {
      public:
        /** An example member function.
         *  More details about this function.
         */
        void example();
    };
    
    void Example_Test::example() {}
    
    /** \example example_test.cpp
     * This is an example of how to use the Example_Test class.
     * More details about this example.
     */
    ```

- **\endinternal**
  - **Description**: Ends a documentation fragment that was started with a \internal command. The text between \internal and \endinternal will only be visible if `INTERNAL_DOCS` is set to YES.

- **\extends <name>**
  - **Description**: Indicates an inheritance relation manually when the programming language does not support this concept natively (e.g., C).
  - **Example**: The file `manual.c` in the example directory shows how to use this command (see also \memberof for the complete file).

- **\file [<name>]**
  - **Description**: Indicates that a comment block contains documentation for a source or header file with the specified name. The file name may include (part of) the path if the file name alone is not unique. If the file name is omitted, the documentation block that contains the \file command will belong to the file it is located in.
  - **Important**: The documentation of global functions, variables, typedefs, and enums will only be included in the output if the file they are in is documented as well or if `EXTRACT_ALL` is set to YES.
  - **Example**:
    ```cpp
    /** \file file.h
     * A brief file description.
     * A more elaborated file description.
     */
    
    /**
     * A global integer value.
     * More details about this value.
     */
    extern int globalValue;
    ```

- **\fileinfo ['{option}']**
  - **Description**: Shows (part of) the file name in which this command
    is placed. The option can be `name`, `extension`, `filename`, `directory`,
    or `full`, with:

        - `name`: The name of the file without extension
    - `extension`: The extension of the file
    - `filename`: The filename (name plus extension)
    - `directory`: The directory of the given file
    - `full`: The full path and filename of the given file
  - **Note**: The command \fileinfo cannot be used as an argument to the \file command.
  - **References**:
    - Section: \lineinfo

- **\lineinfo**
  - **Description**: Shows the line number inside the file at which this command is placed.
  - **References**:
    - Section: \fileinfo

- **\fn (function declaration)**
  - **Description**: Indicates that a comment block contains documentation for a function (either global or as a member of a class). This command is only needed if a comment block is not placed in front (or behind) the function declaration or definition. A full function declaration, including arguments, should be specified after the \fn command on a single line.
  - **Warning**: Do not use this command if it is not absolutely needed, as it will lead to duplication of information and thus to errors.
  - **Example**:
    ```cpp
    class Fn_Test
    {
      public:
        const char *member(char,int) throw(std::out_of_range);
    };
    
    const char *Fn_Test::member(char c,int n) throw(std::out_of_range) {}
    
    /*! \class Fn_Test
     * \brief Fn_Test class.
     *
     * Details about Fn_Test.
     */
    
    /*! \fn const char *Fn_Test::member(char c,int n) 
     *  \brief A member function.
     *  \param c a character.
     *  \param n an integer.
     *  \exception std::out_of_range parameter is out of range.
     *  \return a character pointer.
     */
    ```
  - **References**:
    - Sections: \var, \property, \typedef
