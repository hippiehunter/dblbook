# Quiz Answers

### Variables
1.  What are the two main divisions in a DBL program?
    -   [ ] Procedure division and memory division
    -   [x] data division and procedure division
    -   [ ] data division and memory division
    -   [ ] Procedure division and static division
2.  What keyword allows for variable declarations within the procedure division in recent versions of DBL?
    -   [ ] Var
    -   [x] Data
    -   [ ] Local
    -   [ ] Static
3.  What are the storage specifiers available for records and groups in DBL?
    -   [ ] Static, local, and global
    -   [ ] Stack, global, and local
    -   [x] Stack, static, and local
    -   [ ] Local, global, and data
4.  What is the primary difference between 'stack' and 'static' variables in DBL?
    -   [ ] Stack variables are shared across the program, while static variables are unique to each function.
    -   [x] Static variables retain their value between function calls, while stack variables are deallocated when the scope is exited.
    -   [ ] Stack variables persist until the program ends, while static variables are deallocated when the scope is exited.
    -   [ ] Static variables are shared across the program, while stack variables are unique to each function.
6.  How are 'data' variables stored in DBL?
    -   [ ] They are stored statically.
    -   [ ] They are stored locally.
    -   [x] They are stored on the stack.
    -   [ ] They are stored globally.
7.  What data containers in DBL can hold multiple related data items of various types?
    -   [ ] Functions
    -   [x] Groups
    -   [x] Records
    -   [ ] Variables
8.  True or False: It's mandatory to put 'endrecord' at the end of a record declaration in DBL.
    - False. It's not mandatory to put 'endrecord' at the end of a record declaration in DBL, but it is considered good form


### Primatives
1. What are the three forms of alpha types in DBL?
   - [ ] a, alpha, a10
   - [ ] a, a*, asize
   - [x] a, a10, a*
   - [ ] a, a*, asize+
   
2. What's the maximum length of alpha types in 32-bit Windows and Linux when running Traditional DBL?
   - [x] 65,535 characters
   - [ ] 32,767 characters
   - [ ] 2,147,483,647 characters
   - [ ] 2,147,483,648 characters
   
3. In Traditional DBL, how is a byte represented?
   - [ ] Eight-bit unsigned integer
   - [x] Eight-bit signed integer
   - [ ] Sixteen-bit signed integer
   - [ ] Sixteen-bit unsigned integer
   
4. What's the main difference between the decimal and implied-decimal types in DBL?
   - [ ] Decimal types are unsigned, implied-decimal types are signed.
   - [x] Decimal types are whole numbers, implied-decimal types have a have a sized precision.
   - [ ] Decimal types have a fractional precision, implied-decimal types are whole numbers.
   - [ ] Decimal types are signed, implied-decimal types are unsigned.
   
5. What is the default value for the .NET System.Double structure in DBL?
   - [ ] 1.0
   - [x] 0.0
   - [ ] NULL
   - [ ] Undefined
   
6. What type does short map to in Traditional DBL and DBL on .NET respectively?
   - [ ] i2 and System.Int32
   - [ ] i2 and System.Int64
   - [x] i2 and System.Int16
   - [ ] i4 and System.Int16
   
7. When defining a record, when must you include a size for certain synergy types (a,d,i,id)?
   - [ ] Only when the fields need to be stored in an array.
   - [ ] Only when the fields will be processed in a loop.
   - [x] When the fields define a memory layout (most of the time).
   - [ ] When the fields are initialized with a certain value.
   
8. In Synergy DBL, when is the size of parameters and non ^VAL return types determined?
   - [ ] At compile time
   - [x] At runtime
   - [ ] When they are initialized
   - [ ] When they are declared
   
9. What happens when you pass the string "a slightly longer alpha" to a routine with an unsized alpha parameter myFld, a?
   - [ ] An error occurs as the parameter is unsized.
   - [x] The parameter myFld will have a size of 23.
   - [ ] The parameter myFld will have a size of 24, including the end character.
   - [ ] The program will crash.