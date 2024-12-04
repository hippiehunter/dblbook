# Synergy Data Language
<!--Need to change the TOC (which currently says "Repository Schema") to match this heading-->
**Synergy Data Language Rules**

When using the Synergy Data Language (schema) with DBL, adhere to the following rules:

1.  **Case sensitivity**: Names, keywords, and arguments are case-insensitive, except for quoted definitions, which must be uppercase. Non-quoted data gets automatically converted to uppercase upon input.
2.  **Data validity**: If data is missing or invalid, the statement will be disregarded.
3.  **Order of definitions**: Though definitions generally have a flexible order, there are exceptions as detailed in the "Recommended definition order" section.
4.  **Keyword constraints**:
    -   Keywords can appear in any sequence unless otherwise stated.
    -   Multi-word keywords cannot extend over multiple lines.
    -   Some keywords require the keyword and its associated data to stay on the same line. Such instances are noted with the keyword.<!--Do we mean that it's noted in this book?-->
    -   Keyword data with colons should not contain spaces.
    -   Negative values are not allowed for numeric keyword arguments, except where specified.<!--Where is it specified?-->
5.  **String data**:
    -   Strings should be wrapped in matching double (" ") or single (' ') quotes and not extended over multiple lines.
    -   Strings can contain quotation marks, provided they differ from the enclosing marks. For instance, "Type 'Return' to continue" and 'Type "Return" to continue' are both valid.
6.  **Data limitations**: Oversized data will be shortened.
7.  **Comments**: Start a line with a semicolon to indicate a comment. Avoid comments within a Synergy Data Language statement.

**Recommended definition order**:

Although definitions are flexible, there are guidelines for better organization:

1.  Global formats.
2.  Enumerations.
3.  Templates in parent-child order.
4.  Structures in reference order, with their formats, fields, keys, relations, and aliases. Structures referring to another, via an implicit group or Struct data type, should be defined first.
5.  Files.

**Processing rules for schema files**:

Schema files can create a new repository or modify an existing one.

-   **New repositories**: When generating a new repository using a schema file, if errors are detected during the process, the new repository won't be formed, necessitating schema file corrections.
-   **Updating repositories**: For existing repositories, the utility makes a duplicate before performing updates. Errors will result in the deletion of this copy. Once the repository is updated successfully, use the Verify Repository and Validate Repository utilities to check it.
-   **Loading schemas**: The Load Repository Schema utility can handle both new and existing definitions. Here are some behavior differences depending on the options selected: 
    -   For new repositories, duplicate definitions in the schema file cause error logs.
    -   Merging schemas into existing repositories will add new definitions. Existing definitions can either be replaced or overlaid. The "Replace" option discards the existing structure and integrates the new schema's structure. The "Overlay" option updates existing fields from the schema file and incorporates new ones without any deletions.

**Long descriptions**:
Most of the definition types provided by the Synergy Data Language have an option to include a long description. Originally this was intended to be something like a comment to future users of the repository. However, it has evolved into being used by CodeGen to provide extra data when needed. Many of the long description tags are specific to Harmony Core users but can still be useful in other contexts. Here's an example long description tag: `HARMONYCORE_CUSTOM_FIELD_TYPE=type;`.

**Common attributes**: All of the following attributes can be applied to TEMPLATE, FIELD, and GROUP unless otherwise specified:

-   **Mandatory attributes**:

    -   `name`: The name of the template, field, or group. This name can have a maximum of 30 characters
    -   `TYPE type`: The item's type.
        -   Valid Types: ALPHA, DECIMAL, INTEGER, DATE, TIME, USER, BOOLEAN, ENUM, STRUCT, AUTOSEQ, AUTOTIME.
        -   Default formats are assigned to each type, but they can be overridden with the FORMAT attribute:
            - DATE - YYMMDD
            - TIME - HHMM
            - USER - ALPHA
    -   `SIZE size`: The item's size.
        - Defines the field's maximum character length.
        - If omitted, the size is derived from existing fields or the assigned template.      
    
-   **Optional attributes**:

    -   `PARENT template`: (Only for TEMPLATE) Parent template specification.
    -   `STORED store_format`: How it's stored. Must follow the TYPE keyword if present
    -   `ENUM name` or `STRUCT name`: For enumerations or structures.
    -   `LANGUAGE`<!--Is this supposed to be LANGUAGE VIEW/LANGUAGE NOVIEW? Also needs a description-->
    -   `DESCRIPTION "description"`: A description of the field definition. It can have a maximum of 40 characters and must be enclosed in double or single quotation marks ("" or '').
    -   `LONG DESCRIPTION "long_desc"`: Long_desc can contain 30 lines of up to 60 characters each. Each line must be enclosed in double or single quotation marks ("" or '')
-   **Optional presentation attributes**:
    -   `SCRIPT`, `REPORT`: Options for viewing, with additional `VIEW` or `NOVIEW`.
    -   `POSITION`, `FPOSITION`, `PROMPT`, `HELP`, `INFO LINE`, `USER TEXT`, `FORMAT`, `REPORT HEADING`, `ALTERNATE NAME`, `REPORT JUST`, `INPUT JUST`, `PAINT`: Presentation and descriptive attributes.
    -   `RADIO|CHECKBOX`: Toggle choices.
    -   `FONT`, `PROMPTFONT`: Font specifications.
    -   `READONLY`, `DISABLED`: Functional attributes.
    -   `COLOR`, `HIGHLIGHT`, `REVERSE`, `BLINK`, `UNDERLINE`: Aesthetic attributes.
    -   `DISPLAY LENGTH`, `VIEW LENGTH`, `UPPERCASE`, `NODECIMAL`, `DECIMAL_REQUIRED`: Display characteristics.
    -   `RETAIN POSITION`, `DEFAULT`, `AUTOMATIC`, `NOECHO`, `DATE`, `TIME`, `WAIT`, `INPUT LENGTH`, `BREAK`: Behavior specifications.
    -   `REQUIRED`, `NEGATIVE`, `NULL`: Value constraints.
    -   `MATCH CASE`, `MATCH EXACT`: Matching rules.
    -   `SELECTION LIST`, `SELECTION WINDOW`: Selection parameters.
    -   `ENUMERATED`, `RANGE`: Enumeration and range details.
    -    Methods like `ARRIVE METHOD`, `LEAVE METHOD`, `DRILL METHOD`, `HYPERLINK METHOD`, `CHANGE METHOD`, `DISPLAY METHOD`, `EDITFMT METHOD`: Toolkit field processing methods.
    <!--Can we get rid of all the backticks?-->
-    **Optional SMC-specifc attributes**:
     -    `WEB`
     -    `COERCED TYPE`: 
          -   Specifies the data type for xfNetLink Java or .NET clients.
          -   Valid values depend on the `TYPE` attribute.
              - **Valid coerced types by `TYPE`:**
              - **Decimal (without precision):**
                - DEFAULT, BYTE, SHORT, INT, LONG, SBYTE, USHORT, UINT, ULONG, BOOLEAN, DECIMAL, NULLABLE DECIMAL
              - **Decimal (with precision):**
                - DEFAULT, DOUBLE, FLOAT, DECIMAL, NULLABLE DECIMAL
              - **Integer:**
                - DEFAULT, BYTE, SHORT, INT, LONG, SBYTE, USHORT, UINT, ULONG, BOOLEAN
              - **Date/Time/User:**
                - If DATE format is `YYMMDD`, `YYYYMMDD`, `YYJJJ`, or `YYYYJJJ`, or USER subtype is DATE with `^CLASS^=YYYYMMDDHHMISS`, or `^CLASS^=YYYYMMDDHHMISSUUUUUU` the type can be DATETIME, NULLABLE_DATETIME
- **Attribute negations**: Many attributes come with a negation, often prefixed with "NO" (e.g., `NODATE`, `NODESC`). When specifying an attribute, consider whether its positive or negative form is required.

**Specific to FIELD**:

-   `TEMPLATE template`: Links the field to a particular template.

**Specific to GROUP**:

-   `REFERENCE structure`: References a particular structure.
-   `PREFIX prefix`, `COMPILE PREFIX`: Prefix details.
-   `NOSIZE`: Specifies if size shouldn't be defined.

**Defining a field**

The FIELD statement describes a field definition. This field is associated with the enclosing structure or group. If no structure or group has been defined, the field is ignored.

```
FIELD name [TEMPLATE template] TYPE type  SIZE size
attribute
  .
  .
  .
```

**Defining a group**

The GROUP statement describes a group (field) definition. This group will be associated with the most enclosing structure or group. If no structure or group has been defined yet, the group is ignored. 

```
GROUP name TYPE type [SIZE size]
attribute or field or group
  .
  .
  .
ENDGROUP
```

**Defining a template**

```
TEMPLATE name [PARENT template] TYPE type SIZE size
attribute
  .
  .
  .
```

**Purpose**
A template is a set of field characteristics that can be assigned to one or more field or template definitions. Templates are useful for defining common field characteristics that are used in multiple field definitions. Templates can be nested to create a hierarchy of field characteristics. They aren't commonly used but can be thought of as a "base class" for fields.

**Defining a structure**

```
STRUCTURE name filetype [MODIFIED date] [DESCRIPTION "description"]
[LONG DESCRIPTION "long_desc"] [USER TEXT "string"]
```

Despite being called a structure, this isn't exactly like a structure in DBL. It's the definition of a collection of fields/groups that you can .INCLUDE in DBL to define a record, group, or structure.

**FILETYPE**:

-   Indicates the type of file the structure will be assigned to.
-   **Valid values**:
    -   ASCII
    -   DBL ISAM
    -   RELATIVE
    -   USER DEFINED

#### Basic example
```
STRUCTURE info   DBL ISAM
GROUP customer   TYPE alpha   
   FIELD name   TYPE alpha   SIZE 40
   GROUP office   TYPE alpha   SIZE 70
      FIELD bldg   TYPE alpha   SIZE 20
      GROUP address   TYPE alpha   SIZE 50
         FIELD street   TYPE alpha   SIZE 40
         FIELD zip   TYPE decimal   SIZE 10
      ENDGROUP
   ENDGROUP
   GROUP contact   TYPE alpha   SIZE 90
      FIELD name   TYPE alpha   SIZE 40
      GROUP address   TYPE alpha   SIZE 50
         FIELD street   TYPE alpha   SIZE 40
         FIELD zip   TYPE decimal   SIZE 10
      ENDGROUP
   ENDGROUP
ENDGROUP
```


**Defining a file**

```
FILE name filetype "open_filename"
[DESCRIPTION "description"]
[LONG DESCRIPTION "long_desc"]
...
[ASSIGN structure [ODBC NAME name[, structure [ODBC NAME name], ...]]
```

### Arguments:

Most of these arguments are optional and also can be negated by appending "NO" on the front of the argument. For example, `NOCOMPRESS` would be the negation of `COMPRESS`.

1.  **name**: Name for the file definition.
    -   Max length: 30 characters.
2.  **filetype**: Type of file. Valid options:
    -   ASCII
    -   DBL ISAM
    -   RELATIVE
    -   USER DEFINED
3.  **open_filename**: Name of the actual data file with path.
    -   Max length: 64 characters.
    -   Enclosed in double or single quotation marks.
4.  **USER TEXT** "*string*": (Optional)
    -   User-defined text.
    -   Max length: 60 characters. 
5.   **RECTYPE** *rectype*: (Optional)
    -   Specifies the record type. Used exclusively for DBL ISAM filetypes.
    -   Valid options: FIXED (default), VARIABLE, MULTIPLE.

6.   **PAGE SIZE** *page_size*: (Optional)
    -   Specifies the page size for DBL ISAM filetypes.
    -   Valid options: 512, 1024 (default), 2048, 4096, 8192, 16384, 32768.
7.  **DENSITY** *percentage*: (Optional)
    -   Designates the key density percentage for DBL ISAM files.
    -   Percentage range: 50 to 100.
    -   Default is approximately 50%.

8.  **ADDRESSING** *addressing*: (Optional)
    -   Determines the address length of the ISAM file for DBL ISAM file types.
    -   Valid options: 32BIT (default), 40BIT.

9.   **SIZE LIMIT** *size_limit*: (Optional)
    -   Indicates the maximum megabytes allowed for the data file (.is1) for REV 6+ ISAM files.

10.  **RECORD LIMIT** *record_limit*: (Optional)
    -   Sets the maximum record count permissible in the file for REV 6+ ISAM files.

11.  **TEMPORARY**: (Optional)
    -   Describes the file definition as temporary. Excludes it from ReportWriter or xfODBC file listings.

12.  **COMPRESS**: (Optional)
    -   Asserts that file data is compressed, specific to DBL ISAM files.

13.  **STATIC RFA**: (Optional)
    -   Specifies fixed RFA for records across WRITE operations in DBL ISAM files.

14.  **TRACK CHANGES**: (Optional)
    -   Enables change tracking in the file for REV 6+ ISAM files.

15.  **TERABYTE**: (Optional)
    -   Signifies a 48-bit terabyte file for DBL ISAM filetypes.

16.  **STORED GRFA**: (Optional)
    -   Commands the CRC-32 portion of an RFA to be generated and stored for every STORE or WRITE procedure in DBL ISAM files rather than generated on every read.

17.  **ROLLBACK**: (Optional)
    -   Permits change tracking rollbacks for the file.

18.  **NETWORK ENCRYPT**: (Optional)
    -   Mandates encryption for client access for DBL ISAM files.

19.  **PORTABLE** *integer_specs*: (Optional)
    -   Passes non-key portable integer data arguments to the ISAMC subroutine for DBL ISAM files.
    -   Syntax: I=pos:len[,I=pos:len][,...].

20.  **FILE TEXT** *"file_text"*: (Optional)
    -   Adds specified text to the header of REV 6+ ISAM files.
    -   Syntax:
        -   text_size[K]
        -   "text_string"
        -   text_size[K]:"text_string"

21.  **ASSIGN** *structure*: (Optional)
    -   Assigns a structure to the file definition.
    -   Can be assigned multiple structures.
    -   Max length: 30 characters.
    -   Enclosed in double or single quotation marks.

22.  **ODBC NAME** *name*: (Optional)
    -   Sets the table name for ODBC access, capped at 30 characters.
   
### Discussion:

The **FILE** statement describes a file definition. These definitions specify files for access through the Repository and the structures used for access.

**Key points**:

-   Only structures with matching file types can be assigned to a definition.
-   Structure should have at least one field.
-   For assigning multiple structures, primary keys must match.
    -   Key criteria includes size, sort order, duplicates flag, data type, segments, and segment details.

**Defining an enumeration**

**Purpose**: An enumeration offers a way to define a set of named values, ensuring more readable and maintainable code.

**Syntax**:


```
ENUMERATION [name] [DESCRIPTION "description"] [LONG DESCRIPTION "long_desc"]
MEMBERS [member] [value], [member] [value], ...
```

**Arguments**:

-   ***name***: Denotes the title of the enumeration. The name can be a maximum of 30 characters in length.

-   **DESCRIPTION** "*description*": (Optional) Provides a concise description of the enumeration. The description should be encased in either double (" ") or single (' ') quotation marks and can span up to 40 characters.

-   **LONG DESCRIPTION** "*long_desc*": (Optional) Supplies a comprehensive description, providing more information about the enumeration and its application. This can comprise up to 30 lines, with each line not exceeding 60 characters. Every line should be enveloped in either double or single quotation marks.

-   **MEMBERS** *member*: Details the members of the enumeration. Each member's name can be up to 30 characters long. At least one member is mandatory for an enumeration. If there are multiple members, they should be comma-separated.

    -   ***value*** (Optional): Affiliates a numeric value with the member. When used, this value should appear on the same line as its corresponding member.

**Description**:

ENUMERATION provides a mechanism for developers to create a set of named constants, enhancing code clarity. This is a mimic to <!--How about "the same as" or "similar to"-->the enum provided in DBL but with the benefits of a repository definition.

For instance, one might define an enumeration for days of the week. Instead of working directly with numeric values (which could lead to errors and make the code harder to interpret), developers can use the named constants of the enumeration.

**Defining an alias**

**Purpose**: An alias provides an alternate naming convention for a structure or field in the Synergy Data Language.

**Syntax**:


```
ALIAS [alias] [type] [name]
```

**Arguments**:

-   **alias**: The name for the alias. This name can be up to 30 characters long.

-   **type**: Specifies the alias type. Acceptable values include `STRUCTURE` and `FIELD`.

-   **name**: Represents the original name of the structure or field being aliased. This too can have a length of up to 30 characters.

**Description**:

The ALIAS statement in Synergy Data Language provides a mechanism to give an alternate name to either a structure or a field. This is particularly beneficial in scenarios like the following:

-   **Application conversion**: When transitioning an application to use the Repository and there's a preference for lengthier names in contrast to the shorter, more cryptic ones traditionally used. Aliases can act as an intermediary, facilitating smoother code updates in Synergy.

-   **Structfield definition**: When there's a need to define structfields and simultaneously require that the repository structure be a record in the Synergy code. Here, an alias can be formed and used in the structfield definition.

When invoking the .INCLUDE directive to reference a repository in Synergy code, either the original name or its alias can be used. Initially, the compiler will attempt to find a structure or field with the name mentioned in the .INCLUDE command. In the absence of such a name, it looks for an alias. Consequently, all names (whether original or alias) must be distinct. This rule applies for both structures and fields, with the caveat that field names—whether original or alias—need to be unique within a specific structure.

**Positioning**:

In the Synergy Data Language file, an alias should be situated within the structure it points to, known as the "aliased structure." It's permissible to assign multiple aliases to one structure. Similarly, within an aliased structure, one can map numerous aliases to a singular field.

A field with an alias aligns with the last aliased structure defined. In situations where no aliased structure is present, any aliased fields are disregarded. Also, it's important to note that fields defined within a group cannot be aliased.