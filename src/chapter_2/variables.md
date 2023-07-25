# Variables

The structure of a DBL program is divided into two main divisions: the data division and the procedure division. The data division is where data items, such as records and groups, are declared and organized. The procedure division, on the other hand, contains the executable code, or the operations performed on these data items.

Historically, the separation between these two divisions was strict, but more recent versions of DBL allow for variable declarations within the procedure division using the 'data' keyword. This offers more flexibility in structuring your code while maintaining the ability to separate data and procedures where needed.

Within the data division, one primary form of data organization is through records. Records are structured data containers that can be either named or unnamed. They can hold multiple related data items of various types, similar to a struct in languages like C#. The compiler doesnt require you to put endrecord at the end of a record but it is considered good form.

In addition to records, DBL allows the use of 'groups.' A group can be nested, providing hierarchical data organization.

Records and groups in DBL can be declared with different storage specifiers: stack, static, or local. These specifiers determine the lifespan and accessibility of the data.

'Stack' variables behave like local variables in most other programming languages. They are allocated when the scope they are declared in is entered and deallocated when that scope is exited.

'Static' variables have a unique characteristic. There's exactly one instance of the variable that is shared throughout the entire program, similar to global variables. Once initialized, they retain their value until the program ends, allowing data to persist between function calls.

'Local' variables, meanwhile, behave similarly to static variables in that they are shared across the program. However, the system might reclaim the memory allocated to local variables if it's running low on memory. This makes local variables a flexible choice for large data structures where memory usage might be a concern.

Variables declared with 'data' are always stored on the stack

These varying types of data declarations in DBL provide a range of options to balance between data persistence, scope, and memory management in your programs.

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


## Paths and abreviated paths
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

