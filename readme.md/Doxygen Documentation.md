<h1><div align ="center ">Documenting the code(Day-3)</div></h1>

## Special comment blocks


A special comment block is a C or C++ style comment block with some additional markings, so Doxygen knows it is a piece of structured text that needs to end up in the generated documentation. The next section presents the various styles supported by Doxygen.

For Python, VHDL, and Fortran code there are different commenting conventions, which can be found in sections Comment blocks in Python, Comment blocks in VHDL, and Comment blocks in Fortran respectively.

## Comment blocks for C-like languages (C/C++/C#/Objective-C/PHP/Java)

- **Types of Descriptions**:
  - Brief Description
  - Detailed Description
  - In-body Description (for methods/functions)

#### Description Types

1. **Brief Description**:
   - Short, one-liner
   - Used for tooltips in HTML output

2. **Detailed Description**:
   - Longer, more comprehensive
   - Can describe implementation details

3. **In-body Description**:
   - Concatenation of all comment blocks within the method or function body

#### Multiple Descriptions
- Having more than one brief or detailed description is allowed but not recommended due to unspecified order.

#### Comment Styles for Detailed Descriptions

1. **Javadoc Style**:
It consist of a C-style comment block starting with two *'s, like this:
   
   ```cpp
   /**
    * ... text ...
    */
   ```

3. **Qt Style**:
   ```cpp
   /*!
    * ... text ...
    */
   ```
   - Optional intermediate *'s are valid
   ```cpp
   /*!
   ... text ...
   */
   ```

4. **C++ Comment Lines**:
   ```cpp
   ///
   /// ... text ...
   ///
   ```
   ```cpp
   //!
   //! ... text ...
   //!
   ```
   - A blank line ends a documentation block

5. **Visible Comment Blocks**:
   ```cpp
   /********************************************//**
    *  ... text
    ***********************************************/
   ```
   ```cpp
   //////////////////////////////////////////////////
   /// ... text ...
   //////////////////////////////////////////////////
   ```
   ```cpp
   /************************************************
    *  ... text
    ***********************************************/
   ```

`Note:` the 2 slashes to end the normal comment block and start a special comment block.

`Note:` be careful when using a reformatter like clang-format as it may see this type of comment as 2 separate comments and introduce spacing between them.

   - Requires JAVADOC_BANNER to be set to YES

#### Comment Examples

1. **JavaDoc Style**:
   ```cpp
   /**
    * A brief history of JavaDoc-style (C-style) comments.
    *
    * This is the typical JavaDoc-style C-style comment.
    * 
    * @param theory A description of the parameter.
    */
   void cstyle(int theory);
   ```

2. **JavaDoc Banner Style**:
   ```cpp
   /*******************************************************************************
    * A brief history of JavaDoc-style banner comments.
    *
    * This is a banner comment. 
    *
    * @param theory A description of the parameter.
    ******************************************************************************/
   void javadocBanner(int theory);
   ```

3. **Doxygen Banner Style**:
   ```cpp
   /***************************************************************************//**
    * A brief history of Doxygen-style banner comments.
    *
    * This is a Doxygen-style banner comment.
    *
    * @param theory A description of the parameter.
    ******************************************************************************/
   void doxygenBanner(int theory);
   ```

#### Brief Description Marking

1. **Using \brief Command**:
   ```cpp
   /*! \brief Brief description.
    *
    *  Detailed description starts here.
    */
   ```

2. **Javadoc Style with JAVADOC_AUTOBRIEF**:
   ```cpp
   /** Brief description. Details follow here. */
   ```
   ```cpp
   /// Brief description. Details follow here.
   ```

3. **Special C++ Style (Single Line)**:
   ```cpp
   /// Brief description.
   /** Detailed description. */
   ```
   ```cpp
   //! Brief description.
   //! Detailed description starts here.
   ```

#### Multiple Detailed Descriptions
- Joined if multiple are present, regardless of location in the code.

#### Documentation Placement
- Can place documentation of members before the definition, allowing it to be in the source file instead of the header file.

### Putting Documentation After Members

#### Overview
- Documentation can be placed after members of files, structs, unions, classes, or enums using an additional `<` marker in the comment block.
- This also works for function parameters.

#### Detailed Description After Members
- Examples:
  ```cpp
  int var; /*!< Detailed description after the member */
  int var; /**< Detailed description after the member */
  int var; //!< Detailed description after the member
           //!<
  int var; ///< Detailed description after the member
           ///<
  ```

#### Brief Description After Members
- Examples:
  ```cpp
  int var; //!< Brief description after the member
  int var; ///< Brief description after the member
  ```

#### Documenting Function Parameters
- Use the `@param` command with direction attributes `[in]`, `[out]`, `[in,out]`.
- Inline documentation example:
  ```cpp
  void foo(int v /**< [in] docs for input parameter v. */);
  ```

#### Example of Usage
- Example with a class:
  ```cpp
  /*! A test class */
  class Afterdoc_Test
  {
    public:
      /** An enum type. 
       *  The documentation block cannot be put after the enum! 
       */
      enum EnumType
      {
        EVal1, /**< enum value 1 */
        EVal2  /**< enum value 2 */
      };
      void member(); //!< a member function.
      
    protected:
      int value; /*!< an integer value */
  };
  ```

#### Restrictions
- These blocks can only be used to document members and parameters.
- They cannot document files, classes, unions, structs, groups, namespaces, macros, and enums themselves.
- Structural commands (like `\class`) are not allowed inside these comment blocks.
- Be cautious when using this construct as part of a macro definition, as it may cause unintended documentation substitution.

#### Example of Documented C++ Code Using Qt Style
- Example with a class:
  ```cpp
  //!  A test class. 
  /*!
    A more elaborate class description.
  */
  class QTstyle_Test
  {
    public:
      //! An enum.
      /*! More detailed enum description. */
      enum TEnum { 
                   TVal1, /*!< Enum value TVal1. */  
                   TVal2, /*!< Enum value TVal2. */  
                   TVal3  /*!< Enum value TVal3. */  
                 } 
           //! Enum pointer.
           /*! Details. */
           *enumPtr, 
           //! Enum variable.
           /*! Details. */
           enumVar;  
      
      //! A constructor.
      /*!
        A more elaborate description of the constructor.
      */
      QTstyle_Test();
   
      //! A destructor.
      /*!
        A more elaborate description of the destructor.
      */
     ~QTstyle_Test();
      
      //! A normal member taking two arguments and returning an integer value.
      /*!
        \param a an integer argument.
        \param s a constant character pointer.
        \return The test results
        \sa QTstyle_Test(), ~QTstyle_Test(), testMeToo() and publicVar()
      */
      int testMe(int a,const char *s);
         
      //! A pure virtual member.
      /*!
        \sa testMe()
        \param c1 the first argument.
        \param c2 the second argument.
      */
      virtual void testMeToo(char c1,char c2) = 0;
     
      //! A public variable.
      /*!
        Details.
      */
      int publicVar;
         
      //! A function variable.
      /*!
        Details.
      */
      int (*handler)(int a,int b);
  };
  ```
  ### Qt Style Documentation with Doxygen

#### Automatic Brief Descriptions
- To have the first sentence of a Qt style documentation block automatically treated as a brief description, set `QT_AUTOBRIEF` to `YES` in the Doxygen configuration file.

#### Documentation Placement
- **Common Locations**: Typically, comment blocks are placed in front of the declaration or definition of a file, class, namespace, or one of its members.
- **Other Locations**: For certain cases, such as file documentation, you must place the documentation block elsewhere, as there is no "in front of a file" position.
- Doxygen allows documentation blocks almost anywhere, except inside function bodies or normal C-style comment blocks.
- If documentation blocks are not placed directly before or after an item, a structural command must be used, leading to potential duplication of information. This should be avoided unless necessary.

#### Structural Commands
- Structural commands start with a backslash (`\`) or an at-sign (`@`), followed by the command name and parameters.
- Example for class documentation:
  ```cpp
  /*! \class Test
      \brief A test class.

      A more detailed class description.
  */
  ```

- **Common Structural Commands**:
  - `\struct` to document a C-struct.
  - `\union` to document a union.
  - `\enum` to document an enumeration type.
  - `\fn` to document a function.
  - `\var` to document a variable, typedef, or enum value.
  - `\def` to document a `#define`.
  - `\typedef` to document a type definition.
  - `\file` to document a file.
  - `\namespace` to document a namespace.
  - `\package` to document a Java package.
  - `\interface` to document an IDL interface.

#### Documentation Examples
- **Global Objects**: To document global objects like functions, typedefs, enums, macros, etc., you must document the file containing them. This requires at least a `/*! \file */` or `/** @file */` line in the file.
- Example of a C header (`structcmd.h`) documented using structural commands:
  ```cpp
  /*! \file structcmd.h
      \brief A Documented file.
      
      Details.
  */

  /*! \def MAX(a,b)
      \brief A macro that returns the maximum of \a a and \a b.
     
      Details.
  */

  /*! \var typedef unsigned int UINT32
      \brief A type definition for a .
      
      Details.
  */

  /*! \var int errno
      \brief Contains the last error code.
   
      \warning Not thread safe!
  */

  /*! \fn int open(const char *pathname,int flags)
      \brief Opens a file descriptor.
   
      \param pathname The name of the descriptor.
      \param flags Opening flags.
  */

  /*! \fn int close(int fd)
      \brief Closes the file descriptor \a fd.
      \param fd The descriptor to close.
  */

  /*! \fn size_t write(int fd,const char *buf, size_t count)
      \brief Writes \a count bytes from \a buf to the file descriptor \a fd.
      \param fd The descriptor to write to.
      \param buf The data buffer to write.
      \param count The number of bytes to write.
  */

  /*! \fn int read(int fd,char *buf,size_t count)
      \brief Read bytes from a file descriptor.
      \param fd The descriptor to read from.
      \param buf The buffer to read into.
      \param count The number of bytes to read.
  */

  #define MAX(a,b) (((a)>(b))?(a):(b))
  typedef unsigned int UINT32;
  int errno;
  int open(const char *,int);
  int close(int);
  size_t write(int,const char *, size_t);
  int read(int,char *,size_t);
  ```
- Each comment block contains a structural command, allowing them to be moved to different locations or files without affecting the generated documentation. However, this duplicates prototypes, requiring changes to be made in multiple places. Avoid structural commands if possible.

#### Handling Special Files
- For files with extensions like `.dox`, `.txt`, `.doc`, `.md`, or `.markdown`, or extensions mapped to `md` via `EXTENSION_MAPPING`, Doxygen hides these files from the file list.
- To document unparsed files, use `\verbinclude`:
  ```cpp
  /*! \file myscript.sh
   *  Look at this nice script:
   *  \verbinclude myscript.sh
   */
  ```
- Ensure the script is listed in the `INPUT` or that `FILE_PATTERNS` includes the `.sh` extension and the script is in the path set via `EXAMPLE_PATH`.
