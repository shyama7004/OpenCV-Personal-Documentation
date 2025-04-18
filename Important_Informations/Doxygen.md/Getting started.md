## Getting started

The executable doxygen is the main program that parses the sources and generates the documentation. See section Doxygen usage for more detailed usage information.

Optionally, the executable doxywizard can be used, which is a graphical front-end for editing the configuration file that is used by Doxygen and for running Doxygen in a graphical environment. For macOS Doxywizard will be started by clicking on the Doxygen application icon.

The following figure shows the relation between the tools and the flow of information between them (it looks complex but that's only because it tries to be complete):

<img src ="https://www.doxygen.nl/manual/infoflow.png" alt="Doxygen information flow">
<h3><div align ="center">Doxygen information flow</div></h3>

### Notes on Using Doxygen for Documentation

### Detailed Notes on Using Doxygen for Documentation

#### Step 0: Check Language Support

1. **Supported Languages by Default**:

   - Programming Languages: C, C++, Lex, C#, Objective-C, IDL, Java, PHP, Python, Fortran, D.
   - Hardware Description Language: VHDL.

2. **Custom Language Support**:
   - Configure file type extensions with `Configuration/ExtensionMappings`.
   - Use preprocessor programs for completely different languages (see Helpers page for details).

#### Step 1: Creating a Configuration File

1. **Purpose**:

   - Doxygen uses a configuration file to determine all its settings.
   - Each project should have its own configuration file, which can cover a single file or an entire source tree.

2. **Generating a Template Configuration File**:

   ```bash
   doxygen -g <config-file>
   ```

   - `<config-file>`: The name of the configuration file.
   - If omitted, `Doxyfile` will be created.
   - Existing files named `<config-file>` will be renamed to `<config-file>.bak`.
   - Use `-` (minus sign) to read from stdin for scripting.

3. **Configuration File Format**:

   - Format similar to a simple Makefile.
   - Consists of assignments (tags) in the form:
     ```plaintext
     TAGNAME = VALUE
     TAGNAME = VALUE1 VALUE2 ...
     ```

4. **Editing the Configuration File**:

   - Most tags can be left at their default values in the generated template.
   - Detailed configuration information is available in the Configuration section.

5. **Doxywizard**:

   - A GUI front-end for creating, reading, and writing Doxygen configuration files.
   - Allows setting configuration options via dialogs.

6. **Setting Up Source Files**:

   - For small projects (few C/C++ files): Leave `INPUT` tag empty; Doxygen searches the current directory.
   - For larger projects (source directory/tree):
     - Assign root directories to the `INPUT` tag.
     - Add file patterns to the `FILE_PATTERNS` tag (e.g., `*.cpp *.h`).
     - Use `RECURSIVE` tag to enable recursive parsing.
     - Exclude specific directories with `EXCLUDE_PATTERNS` (e.g., `*/test/*`).

7. **File Parsing by Extension**:
   - Doxygen uses file extensions to determine the parsing language:
     ```plaintext
     .dox, .doc, .c, .cc, .cxx, .cpp, .c++, .cppm, .ccm, .cxxm, .c++m, .ii, .ixx, .ipp, .i++, .inl, .h, .H, .hh, .HH, .hxx, .hpp, .h++, .mm, .txt, .java, .cs, .d, .php, .php4, .php5, .phtml, .inc, .py, .pyw, .f, .for, .f90, .f95, .f03, .f08, .f18, .idl, .ddl, .odl, .vhd, .vhdl, .ucf, .qsf, .l, .md, .markdown, .ice, .m, .M
     ```
   - Customize with `FILE_PATTERNS` and `EXTENSION_MAPPING`.

Doxygen looks at the file's extension to determine how to parse a file, using the following table:

| Extension | Language | Extension | Language    | Extension | Language |
| --------- | -------- | --------- | ----------- | --------- | -------- |
| .dox      | C / C++  | .HH       | C / C++     | .py       | Python   |
| .doc      | C / C++  | .hxx      | C / C++     | .pyw      | Python   |
| .c        | C / C++  | .hpp      | C / C++     | .f        | Fortran  |
| .cc       | C / C++  | .h++      | C / C++     | .for      | Fortran  |
| .cxx      | C / C++  | .txt      | C / C++     | .f90      | Fortran  |
| .cpp      | C / C++  | .idl      | IDL         | .f95      | Fortran  |
| .c++      | C / C++  | .ddl      | IDL         | .f03      | Fortran  |
| .cppm     | C / C++  | .odl      | IDL         | .f08      | Fortran  |
| .ccm      | C / C++  | .java     | Java        | .f18      | Fortran  |
| .cxxm     | C / C++  | .vhd      | VHDL        | .vhd      | VHDL     |
| .c++m     | C / C++  | .cs       | C#          | .vhdl     | VHDL     |
| .ii       | C / C++  | .d        | D           | .ucf      | VHDL     |
| .ixx      | C / C++  | .php      | PHP         | .qsf      | VHDL     |
| .ipp      | C / C++  | .php4     | PHP         | .l        | Lex      |
| .i++      | C / C++  | .php5     | PHP         | .md       | Markdown |
| .inl      | C / C++  | .inc      | PHP         | .markdown | Markdown |
| .h        | C / C++  | .phtml    | PHP         | .ice      | Slice    |
| .H        | C / C++  | .m        | Objective-C |           |          |
| .hh       | C / C++  | .mm       | Objective-C |           |          |

8. **Documenting Existing Projects**:
   - Set `EXTRACT_ALL` to `YES` to generate documentation for all entities.
   - Warnings for undocumented members will not be generated.
   - Enable `SOURCE_BROWSER` for cross-referencing entities with definitions.
   - Enable `INLINE_SOURCES` to include sources directly in the documentation.

#### Step 2: Running Doxygen

1. **Command to Generate Documentation**:

   ```bash
   doxygen <config-file>
   ```

   - Creates output directories (html, rtf, latex, xml, man, docbook) based on settings.

2. **Output Directory**:

   - Default: Directory where Doxygen is started.
   - Customize root directory with `OUTPUT_DIRECTORY`.
   - Specific format directories can be set with `HTML_OUTPUT`, `RTF_OUTPUT`, `LATEX_OUTPUT`, `XML_OUTPUT`, `MAN_OUTPUT`, `DOCBOOK_OUTPUT`.

3. **HTML Output**:

   - View with a browser by opening `index.html` in the `html` directory.
   - Best results with browsers supporting CSS, DHTML, and JavaScript.

4. **LaTeX Output**:

   - Generated documentation must be compiled with a LaTeX compiler.
   - Makefile provided for compilation.
   - Commands:
     ```bash
     make         # Generates refman.dvi
     make ps      # Converts to PostScript
     make ps_2on1 # Puts 2 pages on one physical page
     make pdf     # Converts to PDF
     make pdf_2on1 # Puts 2 PDF pages on one physical page
     ```

5. **RTF Output**:

   - Single file `refman.rtf` optimized for Microsoft Word.
   - Toggle fields to show actual values.

6. **XML Output**:

   - Structured "dump" of gathered information.
   - Individual XML files for each compound.
   - `combine.xslt` to merge files.

7. **Man Page Output**:

   - View with the `man` program.
   - Ensure the man directory is in `MANPATH`.
   - Some information may be lost due to format limitations.

8. **DocBook Output**:
   - Generates DocBook format output.

#### Step 3: Documenting the Sources

1. **Documented Entities**:

   - `EXTRACT_ALL` set to `NO` (default): Only documented entities are generated.

2. **Documentation Blocks**:

   - Place in front of the declaration/definition or after the member.
   - Use structural commands for documentation blocks elsewhere.

3. **File Documentation**:

   - Use structural commands for files.
   - Documentation blocks before or after file members work fine.

4. **Markdown Parsing**:
   - Replaces formatting with corresponding HTML or special commands.
   - Removes leading whitespace and asterisks.
   - Treats blank lines as paragraph separators.
   - Creates links for documented classes and members.
   - Interprets HTML tags and converts to LaTeX equivalents.

By following these steps, you can effectively use Doxygen to generate comprehensive documentation for your project, ensuring that all necessary entities are documented and cross-referenced.

- **HTML Tags**: Interpreted and converted to LaTeX equivalents for output.
