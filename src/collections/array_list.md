# ArrayList

System.Collections.ArrayList and Synergex.SynergyDE.Collections.ArrayList are both implementations of a dynamically sized array of objects, with the Synergex.SynergyDE.Collections version existing to provide a 1-based index for compatibility with traditional DBL. Unless you have a specific need to use 1-based indexing, it is recommended to use the System.Collections version. If you're using .NET exclusively, it's recommended to make use of generics and the System.Collections.Generic.List<T> class instead. With those disclaimers out of the way, let's look at the ArrayList class.<!--"It is recommended" is used in lots of places in this book, but it doesn't fit very well with the tone I think you're going for. Can we say "it's best to use" or "using the blah blah is recommended" or alternating wording like that?-->

### Everything is an object
When compared to an array or a generic type like List<T>,<!--What is "List<T>"?--> the first thing you'll run into is the lack of any types. ArrayLists are collections of objects, so you can add any type of object to them. This is a double-edged sword: on one hand, it's very flexible; on the other hand, it's very flexible. You can add any type of object to an ArrayList, but you'll need to cast it back to the original type when you retrieve it. This can be a source of runtime errors if you're not careful. You may want to constrain what sort of data types you put into any given ArrayList. If you don't know what types might be in there, it can be impossible to actually use the data for anything. Even though ArrayList can only contain objects, you can still effectively store non-objects using [boxing](../complex_types/objects.md). Here's a simple example to give you a feel for how ArrayLists work:

```dbl
structure simple_structure
    somefield, a10
    someother, d20
endstructure

main
record
    myArrayList, @System.Collections.ArrayList
    myStructure, simple_structure
    myInt, i4
    myString, @string
proc
    myArrayList = new System.Collections.ArrayList()
    myArrayList.Add((@a)"hello a") ;myArrayList[0]
    myArrayList.Add((@d)5);myArrayList[1]

    myArrayList.Add((@string)"hello string") ;myArrayList[2]
    myArrayList.Add((@int)5);myArrayList[3]

    ;;you can put anything into an array list, even another array list!
    myArrayList.Add(new System.Collections.ArrayList()) ;myArrayList[4]
    myStructure.somefield = "first"
    myStructure.someother = 1

    ;;boxes a copy of myStructure
    myArrayList.Add((@simple_structure)myStructure) ;myArrayList[5]

    ;;we can change myStructure now without impacting the first boxed copy
    myStructure.somefield = "second"
    myStructure.someother = 2
    ;;boxes another copy of myStructure
    myArrayList.Add((@simple_structure)myStructure) ;myArrayList[6]

    ;;lets get the values back out again
    ;;all objects implement ToString, its not always useful
    ;;but we can see what comes out
    Console.WriteLine(myArrayList[0].ToString())
    Console.WriteLine(myArrayList[1].ToString())
    Console.WriteLine(myArrayList[2].ToString())
    Console.WriteLine(myArrayList[3].ToString())
    Console.WriteLine(myArrayList[4].ToString())
    Console.WriteLine(myArrayList[5].ToString())
    Console.WriteLine(myArrayList[6].ToString())

    ;;lets get a few objects back to their original type
    myInt = (@i4)myArrayList[3]
    Console.WriteLine(%string(myInt))
    myString = (@string)myArrayList[2]
    Console.WriteLine(myString)
    myStructure = (@simple_structure)myArrayList[5]
    Console.WriteLine(myStructure.somefield) ;this should contain "first" not "second"

    ;;this won't work because we haven't correctly matched the types
    myInt = (@i4)myArrayList[0] ;;this is actually a boxed alpha
endmain
```

> #### Output
> ```
> hello a
> 5
> hello string
> 5
> SYSTEM.COLLECTIONS.ARRAYLIST
> first     00000000000000000001
> second    00000000000000000002
> 5
> hello string
> first
> 
> %DBR-E-INVCAST, Invalid cast operation Class <SYSTEM.TYPE_I> is not an ancestor of <SYSTEM.TYPE_A>
> ```

### Finding things
The IndexOf and LastIndexOf methods in the ArrayList class provide the ability to locate the position of an object within the list. The IndexOf method searches from the beginning of the ArrayList and returns the zero-based index<!--Above we use 1-based, so I think we should be consistent: either one-based and zero-based or 1-based and 0-based--> of the first occurrence of the specified object, while LastIndexOf starts the search from the end and returns the index of the last occurrence. Both methods return -1 if the object is not found. However, it's important to note that these methods rely on the default comparer of the object type stored in the ArrayList. If the objects in the list do not provide a meaningful implementation of the Equals method, then the comparison is based on reference equality, meaning that it checks if the references point to the same memory location, not if their contents are the same. This can be a limitation when working with complex objects or objects from classes that haven't overridden the Equals method to provide value-based comparison. To address this limitation, it's often necessary to implement a custom equality comparison by overriding the Equals method in the object's class, or by using a more type-safe collection like List<T> in .NET where more specific comparison methods or comparers can be employed. Below is an example of using IndexOf and LastIndexOf to find the position of an object in an ArrayList.

```dbl
record
    myArrayList, @System.Collections.ArrayList
    myStructure, simple_structure
proc
    myArrayList = new System.Collections.ArrayList()
    myArrayList.Add((@string)"hello 1") ;myArrayList[0]
    myArrayList.Add((@string)"hello 2") ;myArrayList[1]
    myArrayList.Add((@string)"hello 2") ;myArrayList[2]
    myArrayList.Add((@string)"hello 3") ;myArrayList[3]

    Console.WriteLine(%string(myArrayList.IndexOf((@string)"hello 2")))
    Console.WriteLine(%string(myArrayList.LastIndexOf((@string)"hello 2")))

    myStructure.somefield = "first"
    myStructure.someother = 1
    ;;boxes a copy of myStructure
    myArrayList.Add((@simple_structure)myStructure) ;myArrayList[4]
    
    myStructure.somefield = "second"
    myStructure.someother = 2
    ;;boxes another copy of myStructure
    myArrayList.Add((@simple_structure)myStructure) ;myArrayList[5]

    ;;****WARNING****
    ;;returns 5 in traditional DBL, returns -1 in .NET
    ;;the implementation in .NET does not provide "structural equality" for Synergy structures
    ;;this is because the DBL compiler does not automatically generate an 
    ;;overridden Equals method for "simple_structure"
    ;;Traditional DBL happens to treat structures and alphas the same and so gains 
    ;;the ability to check for structural equality
    Console.WriteLine(%string(myArrayList.IndexOf((@simple_structure)myStructure)))
```

> #### Traditional output
> ```
> 1
> 2
> 5
> ```

> #### .NET output
> ```
> 1
> 2
> -1
> ```