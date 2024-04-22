# Repository


### A data dictionary
A data dictionary is a centralized repository of information about data, detailing the description, structure, relationships, usage, and regulations surrounding it. Think of it as a "metadata container," providing developers, database administrators, and other key participants a comprehensive overview of data items in a system. Synergy/DE Repository is the DBL version of a data dictionary.

A typical repository contains entries for each data element or database object and may include its name, type, permissible values, default values, constraints, and description. It may also include details about primary keys, foreign keys, indexes, and relationships between structures.

Beyond mere documentation, a well-maintained repository promotes consistency across large projects. By offering a standardized definition of each data item, it ensures that all stakeholders have a unified understanding, which is especially crucial in large teams or when a codebase has outlasted multiple generations of developers. For instance, when a developer refers to a "customer ID," the repository can provide clarity on its format, whether it's alphanumeric, its length, any constraints, and perhaps even its history or any business rules associated with it.

Moreover, as software systems evolve and scale, the repository evolves with them. When new data elements are introduced or existing ones undergo changes, the repository can be updated accordingly. This dynamic nature makes it an invaluable tool for data governance and auditing, helping organizations trace how data definitions have changed over time.

### Code generation with Repository
CodeGen is a powerful code generator designed for DBL. Rather than writing repetitive, boilerplate code by hand, developers can use CodeGen to automatically generate large portions of their application based on predefined templates and patterns. This not only speeds up the development process but also ensures that the generated code adheres to best practices and is consistent throughout the application.

Now, imagine harnessing the detailed data definitions from Repository and feeding them into CodeGen. This integration transforms the development process. With the insights from the data dictionary, CodeGen can produce code that's tailored to the specific data structures and relationships defined in Repository. For example, if Repository defines a customer entity with specific attributes and relationships, CodeGen can automatically generate the data access layer, CRUD operations, and even user interface components for managing customer data.

Furthermore, as data definitions evolve in Repository, developers can rerun CodeGen to update the corresponding parts of the application, ensuring that the software remains aligned with the latest data schema. This iterative process reduces manual errors, enhances maintainability, and ensures that the application remains data-centric.

### Defining a repository
There are two ways to build a repository: textually using the Synergy Data Language or visually using the repository program<!--Do we mean the Repository application"-->. We will focus on the Synergy Data Language in this book because it's more flexible and easier to maintain. However, the repository program is a great tool for visualizing the data dictionary and can be used to generate the data definition language.

### Include from Repository
The .INCLUDE statement is used to include a repository structure in a program. The use of the word "structure" here is a bit misleading because it<!--What does "it" refer to?--> can produce a variety of data types, including structures, commons, records, and groups. Think of `.INCLUDE "structure" REPOSITORY` as your language interface to the data structures stored in your data dictionary.

`.INCLUDE "structure" REPOSITORY ["rpsfile_log"][, type_spec][, qualifier]`

### Key components:

1.  **Repository source**:

    -   **rpsfile_log**: Refers to a logical name representing the repository main and text filenames.
    -   If it's not specified, the default logical DBLDICTIONARY is used.
    -   If DBLDICTIONARY is undefined, the compiler resorts to using RPSMFIL and RPSTFIL.
    -   **Best practice**: Always capitalize the logical in .INCLUDE statements for accuracy and to prevent errors.
2.  **Inclusion of fields**:

    -   Fields without the "Excluded by Language" flag are included by their names.
    -   Fields with the "Excluded by Language" flag become unnamed fields with the appropriate size. Overlay fields with this flag won't be included.
3.  **Type specification (type_spec)**:

    -   Determines the data structure type, with options like COMMON, RECORD, STRUCTURE, etc.
    -   If unspecified, RECORD is the default.
    -   NORECORD results in field creation without a RECORD statement.
4.  **Modifiers and qualifiers**:

    -   Depending on the .INCLUDE location, suitable modifiers can be appended.
    -   Access modifiers (PUBLIC, PROTECTED, PRIVATE) define the access scope.
    -   OPTIONAL and REQUIRED designate argument necessity.
    -   Directional modifiers (IN, INOUT, OUT) stipulate data flow.
5.  **Prefixes**:

    -   If a repository structure has a group with a defined "Member prefix," this prefix is added to the group's member fields only if the "Use by compiler" flag is active.
    -   Using the PREFIX qualifier results in an additional prefix, compounded with any prefixes specified for group members when the "Use by compiler" flag in Repository is active.
    -   Prefixes are often used to avoid naming conflicts between fields in different records or groups, though this has become less of an issue with improvements to the abbreviated path mechanism.

### Usage contexts:

-   **Enumerations**: Can be included either globally or within classes/namespaces. Inclusion terminates automatically.
-   **Structures**: Suitable for various contexts such as argument groups, class records, and global data sections. Qualifiers can be adjusted depending on the context.

### Recommendations:

-   Use RPSMFIL and RPSTFIL instead of DBLDICTIONARY because these environment variables are compatible with the Synergy/DE Repository.
### Relationship to SMC
<!--Need content for this section-->