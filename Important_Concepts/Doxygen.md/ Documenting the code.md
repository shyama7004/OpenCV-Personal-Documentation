### Documenting the code

#### Overview

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

   ```cpp
   /**
    * ... text ...
    */
   ```

2. **Qt Style**:

   ```cpp
   /*!
    * ... text ...
    */
   ```

   - Optional intermediate \*'s are valid

   ```cpp
   /*!
   ... text ...
   */
   ```

3. **C++ Comment Lines**:

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

4. **Visible Comment Blocks**:
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

### Notes on Documentation Placement

1. **Documentation in Source File**:

   - Documentation can be placed before the definition of members (including global functions).
   - This allows:
     - Keeping the header file compact.
     - Giving the implementer direct access to the documentation.
   - Example:

     ```cpp
     // In header file
     class Example {
     public:
         void func();
     };

     // In source file
     /**
      * Detailed description of func.
      *
      * This method performs a specific task...
      */
     void Example::func() {
         // Implementation
     }
     ```

   - Alternatively, a compromise approach can be used:

     - Brief description before the declaration.
     - Detailed description before the definition.

     ```cpp
     // In header file
     class Example {
     public:
         /**
          * Brief description of func.
          */
         void func();
     };

     // In source file
     /**
      * Detailed description of func.
      *
      * This method performs a specific task...
      */
     void Example::func() {
         // Implementation
     }
     ```

### Configuration File Options

1. **JAVADOC_AUTOBRIEF**:

   - When set to YES, Javadoc style comment blocks automatically start a brief description that ends at the first dot followed by a space or new line.
   - Example:
     ```cpp
     /** Brief description which ends at this dot. Details follow here. */
     ```
     ```cpp
     /// Brief description which ends at this dot. Details follow here.
     ```

2. **JAVADOC_BANNER**:

   - When set to YES, banner comments (comments starting with more than two asterisks) are treated as valid Doxygen comment blocks.
   - Example:
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

3. **JAVADOC_BLOCK**:

   - When set to YES, it enables banner comments to work as expected with Doxygen.
   - Example:
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

4. **JAVADOC_AUTOBRIEF** (for single-line special C++ comments):
   - Should be set to NO if you want to use single-line comments for brief descriptions without them ending prematurely.
   - Example:
     ```cpp
     //! Brief description.
     //! Detailed description starts here.
     ```

### Conclusion

- **Flexibility**:

  - Doxygen provides various ways to create brief and detailed descriptions using different comment styles.
  - It allows placing documentation close to the implementation, making it easier for developers to maintain.

- **Best Practices**:

  - Avoid multiple brief or detailed descriptions to ensure consistent output.
  - Use configuration file options like JAVADOC_AUTOBRIEF and JAVADOC_BANNER to tailor the documentation style to your needs.

- **Examples**:
  - Ensure to provide clear, concise, and helpful documentation using the appropriate styles and settings to maintain readability and usability.

By following these guidelines, developers can create comprehensive and maintainable documentation that enhances the understanding and usability of their code.

### Notes on Putting Documentation After Members

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
