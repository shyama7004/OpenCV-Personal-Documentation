## EXTENSION_MAPPING
Extension Mapping refers to the process of extending or 
modifying the mapping capabilities of a software or application. 
This can include adding new features, tools, or functionality to enhance 
the mapping experience.

### Types of Extension Mapping

There are several types of extension mapping, including:

1. `Mapping Extensions:` These are software extensions that add new features or functionality to a mapping application, such as Esri’s ArcGIS. Examples include the Production Mapping extension, which provides tools for managing production workflows, and the Mapping Extensions mod, which unlocks special features for Beat Saber players.

2. `Workbooks Mapping Extension:` This is a software extension that provides mapping capabilities for users of the Workbooks CRM(`A Customer Relationship Management (CRM) system is a software solution that helps businesses manage and analyze customer interactions and data throughout the customer lifecycle. It enables organizations to improve relationships with customers, streamline processes, and increase profitability.`) system. It allows users to view the locations of people and organizations on a map and plan their day around specific locations.

3. `GitHub Mapping Extensions:` These are open-source extensions that provide additional mapping features and functionality for developers. Examples include the MappingExtensions project, which provides tools for extending the mapping capabilities of Esri’s ArcGIS.

## Benefits of Extension Mapping

Extension mapping can provide several benefits, including:

- Improved mapping capabilities: Extension mapping can add new features and functionality to a mapping application, making it more powerful and useful for users.

- Increased flexibility: Extension mapping can provide more flexibility and customization options for users, allowing them to tailor their mapping experience to their specific needs.

- Enhanced productivity: Extension mapping can help users work more efficiently and effectively by providing tools and features that streamline their workflow.

Doxygen selects the parser to use depending on the extension of the files it parses. With this tag you can assign which parser to use for a given extension. 
`Doxygen has a built-in mapping`, but `you can override or extend it using this tag`. The format is `ext=language`, 
where `ext is a file extension`, and language is one of the parsers supported by 
Doxygen: `IDL`, `Java`, `JavaScript`, `Csharp (C#)`, `C`, `C++`, `Lex`, `D`, `PHP`,
`md (Markdown)`, `Objective-C`, `Python`, `Slice`, `VHDL`, `Fortran 

`Fortran is an imperative programming language that has been used for over 60 years to create powerful, efficient, and accurate applications, particularly in scientific fields.`

(fixed format Fortran: FortranFixed, free formatted Fortran: FortranFree, unknown formatted Fortran: Fortran. In the later case the parser tries to guess whether the code is fixed or free formatted code, this is the default for Fortran type files).

For instance to make Doxygen treat .inc files as Fortran files (default is PHP), 
and .f files as C (default is Fortran), use: `inc=Fortran f=C`.


`Note:` For files without extension you can use no_extension as a placeholder.

`Note that for custom extensions you also need to set FILE_PATTERNS otherwise the files are not
read by Doxygen`. When specifying no_extension you should add * to the FILE_PATTERNS.
