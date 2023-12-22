# Repository
Up first, we have DBL code for the data structures we're going to use. Eventually we're going to define these in your repository using SDL.

```dbl
structure InventoryItem
    ItemId,     a10
    Name,       a40
    Quantity,   i4
endstructure

structure Order
    OrderId,    a10
    ItemId,     a10
    Quantity,   i4
    OrderDate,  d8
endstructure

structure Trend
    ItemId, a10
    ItemCount, i4
    OrderCount, i4
    HistoricCount, i4
endstructure

structure Restock
    ItemId, a10
    Quantity, i4
endstructure
```

You'll notice that these structures all have the `ItemId` field in common. This is going to be how we keep track of which items are which. We'll use this field to join the data together later on. We'll also be using the `ItemId` to look up the item name in the `InventoryItem` structure. This is a common pattern that you'll see a lot of in the wild. It's a good idea to keep this in mind when you're designing your own data structures. Now lets take a look at the SDL for these structures.

```sdl
STRUCTURE InventoryItem DBL
    DESCRIPTION "Inventory item details"
    FIELD ItemId TYPE ALPHA SIZE 10
    FIELD Name TYPE ALPHA SIZE 40
    FIELD Quantity TYPE INTEGER SIZE 4
END

STRUCTURE Order DBL
    DESCRIPTION "Order details"
    FIELD OrderId TYPE ALPHA SIZE 10
    FIELD ItemId TYPE ALPHA SIZE 10
    FIELD Quantity TYPE INTEGER SIZE 4
    FIELD OrderDate TYPE DECIMAL SIZE 8
END

STRUCTURE Trend DBL
    DESCRIPTION "Trend analysis data"
    FIELD ItemId TYPE ALPHA SIZE 10
    FIELD ItemCount TYPE INTEGER SIZE 4
    FIELD OrderCount TYPE INTEGER SIZE 4
    FIELD HistoricCount TYPE INTEGER SIZE 4
END

STRUCTURE Restock DBL
    DESCRIPTION "Restock information"
    FIELD ItemId TYPE ALPHA SIZE 10
    FIELD Quantity TYPE INTEGER SIZE 4
END
```

It looks very similar, but there are a few differences. First, we've added a `DESCRIPTION` to each structure. This is a good practice to get into. It makes it easier for other developers to understand what the structure is for. It can also make it easier to generate documentation for your code. The syntax for the fields is a little different, generally much more verbose than the DBL version. Now that we have our structures defined, we need to build them into a repository. To start, lets get a directory made for this project, call it trend and put it somewhere appropriate. Next write the SDL code above to a file named `repository.scm` in your project directory. From inside a command prompt that has the DBL environment set up, while inside your project folder, run the following command:

```console
set RPSMFIL=%CD%\rpsmain.ism
set RPSTFIL=%CD%\rpstext.ism
dbs RPS:rpsutl -i repository.scm -ir
```

First, we're going to set `RPSMFIL` and `RPSTFIL` to the current directory. This is going to tell `rpsutl` where to put the repository files and later on it will tell `dbl` where to find the repository. Next, we're going to run `rpsutl` with the `-i` flag to tell it to build a repository from the SDL file. The `-ir` flag tells `rpsutl` to rebuild the repository if it already exists. Keep this console open, we're going to using it again in a minute. If you look in your project directory, you should see a few new files. These are the repository files that `rpsutl` generated for us. Now that we have a repository, if we want to use it in our program, we need to tell the compiler about it. When we get to writing the bulk of this program we're going to use an `.include` directive. Lets take a look at how that will work. 

```dbl,ignore,does_not_compile
.include "InventoryItem" REPOSITORY, structure, end
.include "Order" REPOSITORY, structure, end
.include "Trend" REPOSITORY, structure, end
.include "Restock" REPOSITORY, structure, end
```

`.include` can also be used to insert the contents of files on disk but in this case we have the REPOSITORY keyword to tell the compiler that we want to include from the repository we just built. The STRUCTURE keyword tells the compiler that we want it to generate a structure instead of the default of a record. The END keyword tells the compiler that we want it to put END at the end of the included code. Things like records don't actually need an END keyword but global structures do. Time to move on now, we have code to write!