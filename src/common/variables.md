# Variables
`ML is actively working on this (11/16), so comments/changes are provisional at this point`

The structure of a DBL program`"structure of a DBL routine(?)"` is divided into two main divisions: the data division and the procedure division. The data division is where data items, such as records and groups, are declared and organized. The procedure division, on the other hand, contains the executable code, or the operations performed on these data items.

Historically, the separation between these two divisions was strict, but more recent versions of DBL allow for variable declarations within the procedure division using the 'data' keyword. This is similar to the transition from C89 to C99, where variable declarations were allowed within the body of a function. This change has been a welcome addition to DBL, as it allows for more readable and maintainable code.

Within the data division, records are structured data containers that can be either named or unnamed. They can hold multiple related data items of various types, but they differ from the aggregate data structures in other languages in that they represent an instance as well as a type definition. The compiler doesn't require you to put ENDRECORD `**keywords in all caps (like doc)?` at the end of a record but it is considered good form. Records are considered a top-level declaration within the data division so while you can nest groups within records you cannot nest records within records.

#### Named vs Unnamed Records
The existence of named or unnamed records can be a little confusing for developers new to DBL. So when should you use one over the other? Named records have two use cases. The first is is code style: if you have a lot of fields, you might want to group them by purpose to make it easier to reason about them. The second and much more complex use is when you want to refer to all of the data`**"to multiple fields with a single variable"?` as a single variable. That last sentence is doing a lot of heavy lifting, so let's unpack it. 

`would a space before every closing ) or ] in the ascii art text prevent the ) or ] from stepping on the previous character?`
```svgbob
+---------------------------------------------------+
| EmployeeRecord                                    |
+-------------------+-------------------------------+
| id (4 bytes)      | [0000]                        |
| name (30 bytes)   | [name         ] (30 chars)    |
| salary (10 bytes) | [0000000000]                  |
+-------------------+-------------------------------+
                |
                | (referred to as a single variable)
                V
+---------------------------------------------------+
| [id][name.........................][salary]       |
|  4     30 chars                        10         |
+---------------------------------------------------+
| [0000name                          0000000000]    |
+---------------------------------------------------+
```
`**In this diagram, the bottom rectangle shows EmployeeRecord referred to as a single entity, but the arrow is pointing to the rectangle above that, which is showing the constituent variables, it seems. Or am I missing something?`

You can see from the above diagram that we're treating all of the data as a single big alpha. This is very common in DBL code, and it's one of the things that makes I/O very natural. You can write the entire record to disk or send it over the network without any serialization effort. This is not the case with unnamed records. Unnamed records are just a way to group related data together, they are not a type. You can't pass them to a routine or return them from a routine. They are just a way to group data.

## Groups
`**This heading is needed now; otherwise the following text would be under the "Named vs Unnamed Records" heading` 

'Groups'`...are another type of structured data container that can be used in the data division, but groups` allow for nested organization and fixed-size arrays for data hierarchies. Although groups are frequently employed as complex data types`**Because groups are named, they lend themselves to this use? So why are they named?`, the preferred approach for new code is to use a 'structure'. (We'll get around to structures in the chapter on [complex types](../complex_types/structures.md).) This suggestion to prefer structures stems from the fact that these complex data types, even when implemented as group parameters, essentially function as alpha arguments. Consequently, the compiler's type checker is unable to assist in detecting mismatches or incompatibilities, making 'structures' a safer and more efficient option.

`**The next four paragraphs apply only to records, so they'd be better placed if they preceded the "Named vs. unnamed" section. They could follow that section, but then they'd need their own heading.`
Top-level data division records can be declared with different storage specifiers: stack, static, or local. These specifiers determine the lifespan and accessibility of all of the variables and nested groups under them.

'Stack' variables behave like local variables in most other programming languages. They are allocated when the scope they are declared in is entered and deallocated when that scope is exited.

'Static' variables have a unique characteristic. There's exactly one instance of the variable that is across all invocations of their defining routine. This behavior is similar to global variables, but with the key difference that the scope of these variables is specifically limited to the routine that defines them. Once initialized, they retain their value until the program ends, allowing data to persist between calls.

'Local' variables, meanwhile, behave similarly to static variables in that they are shared across all invocations of their defining routine. However, the system might reclaim the memory allocated to local variables if it's running low on memory. When this feature was introduced, computers had significantly less RAM, so local variables were flexible choice for large data structures. There is no reason to use them today.

### Common and Global Data Section (GDS)

Both the COMMON statement and the GLOBAL data sections serve to establish shared data areas that are accessible by multiple routines within a program. However, they differ in how they manage and access the shared data.

The COMMON statement, with its two forms, GLOBAL COMMON and EXTERNAL COMMON, is used to define records accessible to other routines or the main routine. GLOBAL COMMON creates new data space, while EXTERNAL COMMON references data defined in a GLOBAL COMMON statement in another routine.  

The main distinguishing factor with COMMON statements is that the data layout (types, sizes, sequence of fields, etc.) is fixed at the point of the GLOBAL COMMON declaration and cannot be checked during the compilation of EXTERNAL COMMON statements. When these statements are compiled, the compiler creates a symbolic reference to the named common variable, with the linking process determining the correct data address for each symbolic reference.

In contrast, global data sections are defined by the name of the area rather than the record or field name. The RECORD statement within a GLOBAL-ENDGLOBAL block is used to define shared data in these sections. Each GLOBAL statement names a data area that is independent of and accessible to any routine, allowing a global data section to be defined in one place and referenced anywhere. Here's an example that will illustrates how commons are bound at link time.

```dbl
global common
    fld1, a10
    fld2, a10
global common named
    nfld1, a10
    nfld2, a10
proc
    fld1 = "fld1"
    fld2 = "fld2"
    nfld1 = "nfld1"
    nfld2 = "nfld2"
    Console.WriteLine("initial values set")
    Console.WriteLine("fld1 = " + fld1)
    Console.WriteLine("fld2 = " + fld2)
    Console.WriteLine("nfld1 = " + nfld1)
    Console.WriteLine("nfld2 = " + nfld2)
    xcall common_binding_1()
    xcall common_binding_2()
end


subroutine common_binding_1
    common
        fld1, a10
        fld2, a10

    common named
        nfld1, a10
        nfld2, a10
proc
    Console.WriteLine("initial values common_binding_1")
    Console.WriteLine("fld1 = " + fld1)
    Console.WriteLine("fld2 = " + fld2)
    Console.WriteLine("nfld1 = " + nfld1)
    Console.WriteLine("nfld2 = " + nfld2)

    xreturn
endsubroutine

subroutine common_binding_2
    common named
        nfld2, a10
        nfld1, a10
    common
        fld2, a10
        fld1, a10
proc
    Console.WriteLine("initial values common_binding_2")
    Console.WriteLine("fld1 = " + fld1)
    Console.WriteLine("fld2 = " + fld2)
    Console.WriteLine("nfld1 = " + nfld1)
    Console.WriteLine("nfld2 = " + nfld2)
    xreturn
endsubroutine
```

> #### Output
> ```
> initial values set
> fld1 = fld1
> fld2 = fld2
> nfld1 = nfld1
> nfld2 = nfld2
> initial values common_binding_1
> fld1 = fld1
> fld2 = fld2
> nfld1 = nfld1
> nfld2 = nfld2
> initial values common_binding_2
> fld1 = fld1
> fld2 = fld2
> nfld1 = nfld1
> nfld2 = nfld2
> ```

You can see from the output that it doesn't matter what order the fields are declared in, the linker will bind them to the correctly named fields in the common. By contrast the unique feature of global data sections is that they are essentially overlaid record groups, with each routine defining its own layout of a given global data section. This functionality was abused in the past to reduce the memory overhead of programs. The size of each named section is determined by the size of the largest definition. Here's an example to demonstrate the binding differences from common with global data sections that don't have the same definitions.

```dbl
global data section my_section, init
    record
        fred, a10
    endrecord
    record named_record
        another_1, a10
        another_2, a10
    endrecord
endglobal
proc
    fred = "fred"
    another_1 = "another1"
    another_2 = "another2"
    Console.WriteLine("initial values set")
    Console.WriteLine("fred = " + fred)
    Console.WriteLine("another_1 = " + another_1)
    Console.WriteLine("another_2 = " + another_2)
    xcall gds_example_1
end


subroutine gds_example_1
    global data section my_section
        record named_record
            another_1, a10
            another_2, a10
        endrecord

        record
            fred, a10
        endrecord
        
    endglobal
proc
    Console.WriteLine("values in a routine, bound differently")
    Console.WriteLine("fred = " + fred)
    Console.WriteLine("another_1 = " + another_1)
    Console.WriteLine("another_2 = " + another_2)
end
```

> #### Output
> ```
> initial values set
> fred = fred
> another_1 = another1
> another_2 = another2
> values in a routine, bound differently
> fred =   another2
> another_1 = fred
> another_2 =   another1
> ```

You can see from the output that the fields names inside the GDS don't matter to the linker. A GDS is just a section of memory to be overlaid with whatever definition you tell it to use.

In terms of storage, both COMMON and global data sections occupy separate spaces from data local to a routine, and the data space of a record following a COMMON or global data section is not contiguous with the data space of the COMMON or global data section.

Often in the past, developers chose to use COMMON and GDS instead of parameters, either because they were told it was more performant or because it didn't require any refactoring when they wanted additional data inside their routines. But here's a list of reasons why you might want to avoid their usage wherever possible:

1.  **Encapsulation and modularity:** Routines that rely on parameters are self-contained and only interact with the rest of the program through their parameters and return values. This makes it easier to reason about their behavior, since you only have to consider the inputs and outputs, not any external state.

2.  **Easier debugging and maintenance:** Since parameters limit the scope of where data can be changed, it makes it easier to track down where a value is being modified when bugs occur. If a routine is misbehaving, you generally only have to look at the routine itself and the places where it's called. On the other hand, if a global variable is behaving unexpectedly, the cause could be anywhere in your program.

3.  **Concurrency safety:** In multi-threaded environments like .NET, global variables can lead to issues if multiple threads are trying to read and write the same variable simultaneously, whereas parameters are local to a single call to a routine and don't have this problem.

4.  **Code reusability:** Routines that operate only on their inputs and outputs, without depending on external state, are much more flexible and can be easily reused in different contexts.

5.  **Testing:** Routines that use parameters are easier to test, as you can easily provide different inputs for testing. With global variables, you need to carefully set up the global state before each test and clean it up afterwards, which can be error-prone.

### Data

In version 9 of DBL, data declarations were introduced, bringing with them significant improvements to the way variables are managed. Declaring variables using data close to where they are used not only bolsters code readability and maintainability, but it also discourages the problematic practice of reusing variables for different types or purposes.

The same variable being utilized in multiple roles can obscure its current state and purpose within the code, making it challenging to understand and maintain. Using data declarations improves readability by encouraging each variable to be used for a single, well-defined purpose within a specific context.

By using data declarations to create variables for single, specific purposes within their immediate contexts, we can mitigate these issues. This practice enhances code clarity by localizing variables to their points of use, which in turn minimizes the risk of errors related to mismanaged variable types or states.

Adopting this approach supports the creation of self-documenting code. Each variable declared with data inherently carries a meaningful name that accurately reflects its purpose within that specific part of the code. It's also worth noting that data variables are always stored on the stack. 

the use of type inference can create a double-edged sword when it comes to refactoring. On one hand, type inference can simplify refactoring. For instance, if you change the type of a function's return value, any variables that infer their type from this function won't need to be manually updated. This can speed up refactoring and reduce errors caused by incomplete changes.

`data` declarations also support type inference using the syntax `data variable_name = some_expression`. The compiler to deduce the type of the variable based on the assigned expression, eliminating the need for explicit type declarations. This, in effect, can lead to more compact and, in some scenarios, more readable code.

While type inference can result in less explicit code, thereby potentially causing confusion particularly in a team setting or when revisiting older code, this concern is largely mitigated by modern Integrated Development Environments (IDEs). They can often display the deduced type of the variable, thus reducing the downside of less explicit code. 

Type inference can greatly simplify refactoring. For instance, if you change the type of a function's return value, any variables that infer their type from this function won't need to be manually updated. This can speed up refactoring and reduce errors caused by incomplete changes.

However, on the flip side, type inference can make refactoring more risky. Changing the expression assigned to a variable could unintentionally change the variable's type, potentially introducing bugs that are difficult to detect. This risk is particularly high if the variable is used in many places or in complex ways, as the impact of the type change could be widespread and unexpected.

> #### Type inference restrictions
> If a routine return value is of type 'a', 'd', or 'id', the compiler will not be able to infer the data type for initial value expressions. In contrast, data types such as structures, classes, and sized primitives can be correctly inferred by the compiler.

> #### Traditional DBL note
> `data` declarations must declared at the top of a `begin-end` scope, this restriction doesnt exist in DBL running on .NET


TODO: note about goto/call out of scopes with local data declarations

Here's an example showing the basics of data declarations.

```dbl
proc
    begin   ;this enclosing begin-end is only required in Traditional DBL
            ;you can drop it on .NET
        data expression = true
        
        if(expression)
        begin
            data explicitly_typed_inited, a10, "hello data"
            data just_typed, int
            just_typed = 5
            Console.WriteLine(explicitly_typed_inited)
            ;;in Traditional DBL the following line would be an error if it wasnt commented
            ;;See the note above about data declaration restrictions in Traditional DBL
            ;;data another_declaration, a10, "hello data"
        end
    end
```

> #### Output
> ```
> hello data
> ```

### Scope shadowing
DBL follows variable shadowing rules similar to those in other languages, meaning an identifier declared within a scope can shadow an identifier with the same name in an outer scope. For example, if a global variable and a local variable within a function have the same name, the local variable takes precedence within its scope. The follows in the pattern of the most narrowly scoped declaration of a variable being silently chosen by the compiler. This can lead to situations where changes to the local variable do not affect the global variable, even though they share the same name. While shadowing can be used to create private instances of variables within scopes, it can also lead to confusion and errors if not managed carefully, as it may not be clear which variable is being referred to at a given point in the code. If you don't already have code review conventions to manage this risk, it's worth considering implementing them. Here's a short example to illustrate the concept:

```dbl
record
    my_field, a10
proc
    my_field = "hello"
    begin
        data my_field, d10
        ;;this is a totally different variable
        my_field = 999999999
        Console.WriteLine(%string(my_field))
    end
    ;;once we exit the other scope, we're back to the original variable
    Console.WriteLine(my_field)
```

> #### Output
> ```
> 999999999
> hello
> ```

## Paths and abbreviated paths
You can declare the same field name multiple times within a group or named record structure. However, when accessing fields with the same name, ensure that the path used to reach the field is unique. This requirement is due to the compiler's need for specificity when navigating nested structures. Here is an example:

```dbl
import System

main
    record inhouse
        accnt           ,d5
        name            ,a30
        address         ,a40
        zip             ,d10
    record client
        accnt           ,[100]d5
        group customer  ,[100]a
            name          ,a30
            group bldg    ,a
                group address ,a
                    street    ,a4
                    zip       ,d10
                endgroup
            endgroup
            group contact ,a
                name        ,a30
                group address ,a
                    street    ,a40
                    zip       ,d10
                endgroup
            endgroup
        endgroup
proc
    ;;succeeds because its fully qualified
    Console.WriteLine(contact.name)
    Console.WriteLine(contact.address.street)
    Console.WriteLine(customer[5].bldg.address.street)
    Console.WriteLine(client.accnt)
    Console.WriteLine(inhouse.accnt)
    Console.WriteLine(inhouse.name)

    ;;succeeds though there is a more deeply nested alternative
    Console.WriteLine(name)
    Console.WriteLine(customer[1].name)

    ;;Fails
    Console.WriteLine(client.name)
    Console.WriteLine(address.street)
    Console.WriteLine(accnt)
endmain
```

In this data structure, the following paths are valid because they unambiguously lead to a single field:

```dbl,ignore,does_not_compile
contact.name
contact.address.street
customer[5].bldg.address.street
client.accnt
inhouse.accnt
inhouse.name
```

The following paths are valid because they are actually a fully qualified path even though there are more deeply nested variables with the same name. These accesses would have failed prior to version 12.

```dbl, ignore,does_not_compile
customer[1].name
name
```

However, the following paths are invalid because they could refer to multiple fields:

```dbl,ignore,does_not_compile
client.name
address.street
accnt
```

Using any of these paths will result in a helpful compiler error that will tell you what the other considered symbols were.

> #### Compiler Output
> ```
> %DBL-E-AMBSYM, Ambiguous symbol client.name
> 1>%DBL-I-ERTXT2,   MAIN$PROGRAM.client.customer.name
> 1>%DBL-I-ERTXT2,   MAIN$PROGRAM.client.customer.contact.name : Console.WriteLine(client.name)
> ```

> #### Quiz
> 1.  What are the two main divisions in a DBL program?
>     -   [ ] Procedure division and memory division
>     -   [ ] Data division and procedure division
>     -   [ ] Data division and memory division
>     -   [ ] Procedure division and static division
> 2.  What keyword allows for variable declarations within the procedure division in recent versions of DBL?
>     -   [ ] Var
>     -   [ ] Data
>     -   [ ] Local
>     -   [ ] Static
> 3.  What are the storage specifiers available for records and groups in DBL?
>     -   [ ] Static, local, and global
>     -   [ ] Stack, global, and local
>     -   [ ] Stack, static, and local
>     -   [ ] Local, global, and data
> 4.  What is the primary difference between 'stack' and 'static' variables in DBL?
>     -   [ ] Stack variables are shared across the program, while static variables are unique to each function.
>     -   [ ] Static variables retain their value between function calls, while stack variables are deallocated when the scope is exited.
>     -   [ ] Stack variables persist until the program ends, while static variables are deallocated when the scope is exited.
>     -   [ ] Static variables are shared across the program, while stack variables are unique to each function.
> 6.  How are 'data' variables stored in DBL?
>     -   [ ] They are stored statically.
>     -   [ ] They are stored locally.
>     -   [ ] They are stored on the stack.
>     -   [ ] They are stored globally.
> 7.  What data containers in DBL can hold multiple related data items of various types?
>     -   [ ] Functions
>     -   [ ] Groups
>     -   [ ] Records
>     -   [ ] Variables
> 8.  True or False: It's mandatory to put 'endrecord' at the end of a record declaration in DBL.