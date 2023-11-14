# Overlays

When defining fields within a record, group, or structure, the default behavior is to sequentially allocate memory for each new field, starting from where the previous field ends. But in some cases, you might need to interpret existing data from a fresh angle, map it to a different type, or treat a set of fields as a single entity. You can achieve this by overlaying memory, that is, specifying a field to start at a particular location within the data structure. To do this, you employ a position indicator (@) in your field definition.

It's important to be aware that memory overlays can only be applied to fixed-size data types. These include alpha (a), decimal (d), implied decimal (id), and integer (i) types. In addition to these basic types, complex data structures can also be overlaid, but only if they are composed exclusively of these fixed-size data types.

Memory overlays are particularly valuable when dealing with records that represent different subtypes. Often, the first few bytes of a record indicate its specific subtype. By using memory overlays, you can manage this scenario effectively, delivering customized views of your data according to its subtype.

The area referred to by the position indicator starts at the specified offset within the record or group. If the field extends beyond the record being overlaid, you will encounter a compilation error: "Cannot extend record with overlay field". However, if the field extends beyond a non-overlaid record and doesn't overlay another field, DBL extends the record to fit the newly defined field.

To use a position indicator, the literal record must be named, or the field must exist in a GROUP/ENDGROUP block. There are two forms of indicating a field's starting position: absolute and relative.

The **absolute** form involves specifying a single decimal value to represent the actual character position within the record or group where the field begins. For example, `@2` indicates that the field begins at the second character position.

```dbl
record title
    id,a*       @1,     "Inventory ID"
    onhand,a*   @20,    "Qty On-hand"
    commtd,a*   @35,    "Committed"
    order,a*    @50,    "On Order"
record line
    id          ,a12    @1
    onhand      ,a10    @20
    commtd      ,a9     @35
    order       ,a8     @50
proc
    line.id = "Sausage Roll"
    Console.WriteLine(title.id)
    Console.WriteLine(line.id)

    line.id = "Sausage Roll but too long of a title"

    Console.WriteLine(line)
```

> #### Output
> ```
> Inventory ID
> Sausage Roll
> Sausage Roll
> ```

You can see from the output that the declared size and type of fields is still respected when storing data even though the lin.id field is declared as an **absolute** overlay.

The **relative** form specifies the starting position relative to another record or field using the syntax `name[+/–offset]`. Here, `name` represents a previously defined record or field, and `offset` indicates the number of characters from the beginning of the `name` at which the field begins. The offset can be preceded by a plus (+) or a minus (–).

Let's consider some examples:

```dbl
record contact
    name ,      a25
    phone ,     a14, "(XXX) XXX-XXXX"
    area ,      d3 @phone+1
    prefix ,    d3 @phone+6
    line_num ,  d4 @phone+10
```

In `contact`, `phone` references the entire 14-character field. Furthermore, `area` references the second through fourth characters of `phone`, `prefix` references the seventh through ninth characters, and `line_num` references the last four characters.

You may want to create an overlay, or a new view, on top of an entire existing data structure. This could be a group, record, or common. This can be achieved using the ,X syntax during declaration. When used, `,X` indicates that you're creating an overlay for the most recent nonoverlay entity of the same type. For instance, if you're declaring a new record with ,X, it will overlay the last defined nonoverlay record. Similarly, it works for  groups, and commons. However, there's a special case for external commons - if you use ,X while declaring an external common, it will be ignored. Here's the general format of declaring an overlay

```dbl
record interesting_definition
        aKey,  a10
        aBlob, a50
endrecord
record inventory_layout, x
    id,            a10
    description,   a20
    quantity,      d20
    something_else,a10
endrecord
record customer_layout, x
    id,     a10
    name,   a20
    address,a30
endrecord
proc
    inventory_layout.description = "some kind of item"
    inventory_layout.quantity = 5000000
    Console.WriteLine(inventory_layout.quantity)
    Console.WriteLine(customer_layout.address)
```

> #### Output
> ```
> 5000000
> 00000000000005000000
> ```

You can see from the output that interpreting a decimal field as an alpha can lead to unexpected results, but it is very common for DBL projects to feature this kind of layout. Remember, when adding fields to a record with overlays, you must ensure that the absolute field positions haven't changed. Also, the field specified in the position indicator should have been defined before, and the specified record for overlaying should be the one being overlaid. This memory overlaying feature in DBL allows for efficient memory usage and convenience when working with fixed layouts.


### Absolute Ranging
A ranged reference in DBL allows you to specify a range of characters within a variable's data space. This range is specified in parentheses following the variable reference and represents the starting and ending character positions relative to the start of the variable's data. The value of a ranged variable is the sequence of characters between, and including, these start and end positions. If the specified range exceeds the limit of the first variable, the range continues onto the next variable.

Ranging is possible on real arrays. An example would be `my_alpha_array[1,2](1,2)`. However, subscripted arrays cannot be ranged, and trying to do so, such as `my_alpha_array(4)(1,2)`, will produce an error.

If the variable being ranged is of implied-decimal type, the value is interpreted as a decimal. Please note that ranging is not permissible beyond the defined size of class data fields, records or when the -qcheck compiler option is specified.

Absolute ranging specifies the start and end character positions directly. The format for this type of ranging is variable(start_pos,end_pos). 

### Relative Ranging

Relative ranging allows you to specify a range in terms of a start position and length, or an end position and length backward from that end position. This is done using two numeric expressions separated by a colon in the format variable(position:length).

If the length is positive, the specified position is the starting point, and the length extends forward from this point. The value of the ranged variable is the sequence of characters that begins at the start position and spans the specified length.

Conversely, if the length is negative, the specified position is treated as an end point. The length then extends backward from this end position. In this case, the value of the ranged variable is the sequence of characters that starts the specified number of characters before the end position and ends at the end position.

This relative ranging mechanism provides you with flexible ways to interpret your data, whether you want to view it from a specified starting point forward or from a specified ending point backward.

> #### Range Restrictions
> In Traditional DBL, referencing beyond the defined area up to the end of the data area is permitted, if your dimension specification exceeds the declared size of its corresponding array dimension. This technique, known as 'over subscripting', 'subscripting off the end', or 'over ranging', is however not allowed in .NET. Moreover, it's also disallowed for class data fields in Traditional DBL, or when the '-qstrict' or '-qcheck' options are specified.
> 
> While over subscripting might seem like a handy tool, it can introduce a significant number of bugs in production. Therefore, it's highly recommended to employ the '-qcheck' option for all development environments and '-qstrict' for all production builds to prevent these issues.
> 
> It's essential to note that these rules generally apply to fixed-size data types, such as alpha (a), decimal (d), implied decimal (id), and integer (i), as well as complex structures composed solely of these fixed-size data types. Interestingly, despite not being a fixed-size type, Strings can also be ranged. The compiler handles this by interpreting the contents of the String as if it were an Alpha type, which allows for flexible manipulation within the defined range.

Here's an example showing absolute and relative ranging.

```dbl
record demo
        my_d_array       ,[3,2]d2 , 12 ,34,
        &                           56 ,78,
        &                           98 ,76
        my_a_array        ,[2,4]a3, "JOE" ,"JIM" ,"TED" ,"SAM",
        &                           "LOU" ,"NED" ,"BOB" ,"DAN"
proc
    Console.WriteLine(demo.my_a_array[1,1](1, 2))
    Console.WriteLine(demo.my_a_array[1,1](3:1))
```

> #### Output
> ```
> JO
> E
> ```


