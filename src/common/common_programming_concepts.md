# Common Programming Concepts

In this chapter, we delve into fundamental concepts prevalent across the spectrum of programming languages and examine their applications within DBL. At their roots, most programming languages share many similarities. While none of the concepts we explore in this chapter are exclusive to DBL, our discussion will be framed in the context of DBL, providing explanations around the conventions associated with these concepts.

In particular, we will explore variables, basic data types, routines, comments, and control flow. These foundational elements constitute the backbone of every DBL program. Gaining an early understanding of these elements will equip you with a robust core knowledge to kickstart your journey in DBL programming.

#### Multi-line statements
DBL considers the end-of-line character to be a statement terminator. This means that in order to write a multi-line statement, you need to use the line continuation character, which is "&". The & goes not on the end of the prior line, but instead on the start of the continuing line. This can be an unpleasant surprise for new developers coming from other languages.

#### Identifiers
Identifiers serve as unique names to identify various elements within the code, such as keywords, variables, routines, and labels. The rules governing the structure of identifiers in DBL are designed to ensure readability and manageability of the code. In Traditional DBL, an identifier can have up to 30 characters, while the .NET environment allows for over 1000 characters, accommodating more descriptive naming conventions. The first character of an identifier in Traditional DBL must be alphabetic (A-Z), whereas in the .NET environment, an underscore (\_) is also permissible as the initial character. The subsequent characters can be a mix of alphanumeric characters (A-Z, 0-9), dollar signs (\$), or underscores (\_), allowing for a combination that can include abbreviations, acronyms, or even certain special characters to create meaningful and informative names.

#### Keywords
Unlike many other languages, DBL doesn't use keywords for features existing before its ninth version. This allows you to use terms typically reserved in other languages as variable or function names in DBL. While newer versions have introduced some reserved words, much of DBL's core functionality doesn't rely on specific keywords. If you're used to other languages, this might seem unusual, but it's part of DBL's design. 

#### Case sensitivity
DBL is mostly case insensitive. so while you may see keywords or identifiers shown in UPPERCASE letters, the compiler will treat the identifier upPPeR_cASe identically to UPPER_CASE. The only time DBL is case sensitive is when calling OO things or when it needs to decide between two ambiguous identifiers.