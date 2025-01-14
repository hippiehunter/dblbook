# Dictionary
There is nothing built into Traditional DBL that behaves exactly like Dictionary in .NET. However, DBL contains a few of the necessary building blocks, so we're going to use this as an opportunity to build our own Dictionary class. This will be a good exercise in using some of the collections and concepts we've covered so far.

### What's this useful for?
Depending on your historical context, you might be wondering, why should I care about a dictionary when I can just use an ISAM file? Alternatively, you might be wondering why there isn't much of a built-in in-memory associative lookup data structure. I'll start by trying to sell you on the benefits of an in-memory dictionary.

Using in-memory dictionaries offers several benefits:

    Speed: Accessing and modifying data in memory is orders of magnitude faster than disk operations.
    Efficiency: In-memory operations reduce the overhead of disk I/O, making data processing more efficient.
    Simplicity: Working with data in memory often simplifies the code, reducing the complexity associated with file management. 
    Serialization: There's no need to serialize and deserialize data when it's already in memory. More importantly, there's no need to worry about data structures like string that can't be written directly to ISAM files.

Now it's time to discuss the downsides, and these are probably why in-memory dictionaries aren't used much in traditional DBL code. DBL programs often handle large volumes of data, and while the amount of RAM installed on your production servers may have grown significantly over the last 30 years, there are still some operations where you should work in on-disk structures like a temporary ISAM file.

That said, there are still plenty of scenarios where an in-memory dictionary is a good fit. For example, if you need to perform a series of lookups on a small set of data, it's often more efficient to load the data into memory and perform the lookups there, rather than repeatedly accessing the disk. This is especially true if the data is already in memory, such as when it's being passed from one routine to another. In such cases, using an in-memory dictionary can be a good option. As with all things architecture and performance related, your mileage may vary, and you should always test your assumptions.

## Implementation
Let's jump into a high-level overview for our custom implementation of a dictionary-like data structure, combining the Symbol Table API with System.Collections.ArrayList to manage string lookups of arbitrary objects.

### Overview
- **Purpose:** To create a dictionary for string-based key lookups.
- **Key components:**
  - Symbol Table API: For handling key-based lookups.
  - System.Collections.ArrayList: For storing objects.
- **Operations Supported:** Add, Find, Delete, and Clear entries.

### Class structure

#### `StringDictionary` class
- **Purpose:** Acts as the main dictionary class.
- **Key components:**
  - `symbolTableId`: The identifier for the symbol table.
  - `objectStore`: An ArrayList to store objects.
  - `freeIndices`:<!--TODO: Should this be freeIndices throughout, including the sample code?--> An ArrayList to manage free indices in `objectStore`.

#### `KeyValuePair` inner class
- **Purpose:** Represents a key-value pair.
- **Components:**
  - `Key`: The key (string).
  - `Value`: The value (object).

### Constructor: `StringDictionary()`
- Initializes `objectStore` and `freeIndices`.
- Calls `nspc_open` to create a symbol table with specific flags.
- Flags used:
  - `D_NSPC_SPACE`: Leading and trailing spaces in entry names are significant.
  - `D_NSPC_CASE`: Case sensitivity for entry names.

### Destructor: `~StringDictionary()`
- Closes the symbol table using `nspc_close`.

### Methods overview
1. **Add method:**
   - Adds a new key-value pair to the dictionary.
   - Checks for duplicate keys using `nspc_find`.
   - If no duplicate, adds the key-value pair using `nspc_add`.

2. **TryGet method:**
   - Tries to get the value for a given key.
   - Uses `nspc_find` to locate the key.
   - If found, retrieves the value from `objectStore`.

3. **Get method:**
   - Retrieves the value for a given key.
   - Similar to `TryGet` but throws an exception if the key is not found.

4. **Set method:**
   - Sets or updates the value for a given key.
   - If the key exists, updates the value.
   - If the key doesn't exist, adds a new key-value pair.

5. **Remove method:**
   - Removes a key-value pair from the dictionary.
   - Uses `nspc_find` to locate the key.
   - Deletes the entry using `nspc_delete`.

6. **Contains method:**
   - Checks if a key exists in the dictionary.

7. **Clear Method:**
   - Clears the dictionary.
   - Uses `nspc_reset` to clear the symbol table.

8. **Items Method:**
   - Returns a collection of all key-value pairs in the dictionary.

### Internal Methods
1. **AddObjectInternal:**
   - Manages adding objects to the `objectStore`.
   - Uses `freeIndices` to reuse free slots in `objectStore`.

2. **RemoveObjectInternal:**
   - Manages removing objects from `objectStore`.
   - Adds the index to `freeIndices`.

### Symbol Table API integration
- The Symbol Table API (%NSPC_ADD, %NSPC_FIND, %NSPC_DELETE, etc.) is used for managing keys in the dictionary.
- `objectStore` holds the actual objects, while the symbol table keeps track of the keys and their corresponding indices in `objectStore`.

### Error handling
- The class includes error handling for situations like duplicate keys or keys not found.

### Usage
- This `StringDictionary` class can be used for efficient key-value pair storage and retrieval, especially useful in scenarios where the keys are strings and the values are objects of arbitrary types.


```dbl
import System.Collections

.include 'DBLDIR:namspc.def'

namespace DBLBook.Collections
	public class StringDictionary
		public class KeyValuePair
			public method KeyValuePair
				key, @string
				value, @object
			proc
				this.Key = key
				this.Value = value
			endmethod


			public Key, @string
			public Value, @Object
		endclass


		private symbolTableId, i4
		private objectStore, @ArrayList
		private freeIndices, @ArrayList
		public method StringDictionary
		proc
			objectStore = new ArrayList()
			freeIndices = new ArrayList()
			symbolTableId = nspc_open(D_NSPC_SPACE | D_NSPC_CASE, 4)
		endmethod

		method ~StringDictionary
		proc
			xcall nspc_close(symbolTableId)
		endmethod

		private method AddObjectInternal, i4
			value, @object
		proc
			if(freeIndices.Count > 0) then
			begin
				data freeIndex = (i4)freeIndices[freeIndices.Count - 1]
				freeIndices.RemoveAt(freeIndices.Count - 1)
				objectStore[freeIndex] = value
				mreturn freeIndex
			end
			else
				mreturn objectStore.Add(value)
		endmethod

		private method RemoveObjectInternal, void
			index, i4
		proc
			freeIndices.Add((@i4)index)
			;;can't just call removeAt because it would throw off all of the objects that are stored after it
			;;so we just add to a free list and manage the slots that way
			objectStore[index] = ^null
		endmethod

		public method Add, void
			req in key, @string
			req in value, @object
			record
				existingId, i4
				newObjectIndex, i4
		proc
			
			if(nspc_find(symbolTableId, key,, existingId) == 0) then
			begin
				newObjectIndex = AddObjectInternal(new KeyValuePair(key, value))
				nspc_add(symbolTableId, key, newObjectIndex)
			end
			else 
				throw new Exception("duplicate key")
		endmethod

		public method TryGet, boolean
			req in key, @string 
			req out value, @object
			record
				objectIndex, i4
				kvp, @object
		proc
			if(nspc_find(symbolTableId, key,objectIndex) != 0) then
			begin
				kvp = objectStore[objectIndex]
				value = ((@KeyValuePair)kvp).Value
				mreturn true
			end
			else
			begin
				value = ^null
				mreturn false
			end
		endmethod

		public method Get, @object
			req in key, @string 
			record
				objectIndex, i4
				kvp, @Object
		proc
			if(nspc_find(symbolTableId, key,objectIndex) != 0) then
			begin
				kvp = objectStore[objectIndex]
				mreturn ((@KeyValuePair)kvp).Value
			end
			else
				throw new Exception("index not found")

		endmethod

		public method Set, void
			req in key, @string 
			req in value, @object
			record
				objectIndex, i4
		proc
			if(nspc_find(symbolTableId, key,objectIndex) != 0) then
			begin
				objectStore[objectIndex] = new KeyValuePair(key, value)
			end
			else
				Add(key, value)

		endmethod

		public method Remove, void
			req in key, @string
			record
				objectAccesCode, i4
				objectIndex, i4
		proc
			if((objectAccesCode=%nspc_find(symbolTableId,key,objectIndex)) != 0)
			begin
				nspc_delete(symbolTableId, objectAccesCode)
				RemoveObjectInternal(objectIndex)
			end
		endmethod

		public method Contains, boolean
			req in key, @string
		proc
			mreturn (nspc_find(symbolTableId, key) != 0)
		endmethod

		public method Clear, void
		proc
			nspc_reset(symbolTableId)
			freeIndices.Clear()
			objectStore.Clear()
		endmethod

		public method Items, [#]@StringDictionary.KeyValuePair
			record
				itm, @StringDictionary.KeyValuePair
				result, [#]@StringDictionary.KeyValuePair
				itemCount, int
				i, int
		proc
			itemCount = 0
			foreach itm in objectStore
			begin
				if(itm != ^null)
					incr itemCount
			end

			result = new KeyValuePair[itemCount]
			i = 1
			foreach itm in objectStore
			begin
				if(itm != ^null)
				begin
					result[i] = itm
					incr i
				end
			end
			mreturn result
		endmethod

	endclass
endnamespace
```