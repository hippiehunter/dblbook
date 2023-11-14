# Objects
### Object Handle Syntax
When specifying a type, you mostly need to use the `@` symbol to indicate that the type is an object. For example, to declare a variable of type `System.Object`, you would use the following syntax:
```dbl,ignore,does_not_compile
data obj, @System.Object
```

The `@` can be ignored when declaring a variable of type `string` or `System.String` and also in some cases related to generic types but is otherwise required. For example, the following code is valid:
```dbl,ignore,does_not_compile
data str, string
```

### Casting
Casting allows developers to convert one data type into another using the syntax `(typename)variable`. This explicit cast is essential when the compiler cannot automatically determine the type conversion or when you intend to treat an instance of one type as another. While this casting method is direct, it can throw an exception if the cast is invalid. To enhance type-checking capabilities, DBL provides the `is` and `as` operators. The `is` operator checks if an object is of a specific type, returning a boolean result. For example, `if (obj .is. SomeType)` would check if obj is of type SomeType. On the other hand, `^as` performs a safe cast. It attempts to cast an object to a specified type and, if the cast isn't possible, it returns `^null` instead of throwing an exception. For instance, `data result = ^as(obj, @SomeType)` would attempt to cast obj to SomeType, assigning the result to result, or `^null` if the cast fails. `^as` is only available when compiling for .NET. 

### Object Lifetime
When using Traditional DBL the lifetime of an object is controlled by keeping a list of all the live roots. This is sort of like reference counting but it doesnt have a problem with circular references. Destructors will be called immediately when the last reference to an object goes out of scope. This is different from .NET where the garbage collector will run at some point in the future and clean up objects that are no longer referenced. Keep this in mind when dealing with objects that have destructors. In .NET, developers will often use the Dispose pattern to clean up resources. When compiling for .NET, DBL offers some syntax support for this in the form of the `disposable` modifier on a `data` statement. The works similarly to `using` in C# and will call the `Dispose` method on the object when it goes out of scope. For example:

```dbl,ignore,does_not_compile
disposable data obj, @MyDisposableObject 
```

### Boxing and Unboxing
The process of converting value types and descriptor types into objects, and vice versa, is referred to as "boxing" and "unboxing". These actions are seamlessly handled by the compiler and runtime.

**Understanding Boxing**

Boxing essentially wraps a value type, like an integer or a structure, into an object. Declaring a specific typed box can be done by prepending the typename with `@` like `@i` or `@my_struct`. For boxing, you can employ either the specific cast syntax `(@type)` or a generic `(object)`. Depending on the method chosen, the behavior can differ:

-   Using `(@type)` cast:
    ```dbl,ignore,does_not_compile
    obj = (@d)dvar
    ```

Remember, when dealing with numeric literals, you must specify if it's an integer or implied-decimal with `(@i4)` or `(@id)` respectively.

-   Using `(object)` cast:
    ```dbl,ignore,does_not_compile
    obj = (object)mystructvar
    ```

In DBL, boxing takes place automatically under several scenarios, such as:

-   Passing a literal or value type to a `System.Object` parameter.
-   Assigning a value type to a `System.Object` variable.
-   Assigning a literal to a `System.Object` field or property.

When a value gets boxed, it becomes of type `@System.Object`. This limits its accessibility to only the members of `System.Object`.

**Understanding Unboxing**

Unboxing, the counterpart to boxing, extracts the original value from the object. Usually, this involves explicitly casting the object back to its original type. In some scenarios, DBL allows for automatic unboxing, such as when:

-   Passing a boxed type to a matching unboxed type parameter.
-   Assigning a boxed type to a variable with a matching type.
-   Accessing a field of a typed boxed structure.


Using `(type)` cast:
```dbl,ignore,does_not_compile
dval = (d)obj
```