# Mapping
Memory handles and mapping structures allow for dynamic memory management but also some pretty unsafe hacks. They provide a way to create a structured view over a block of bytes, making it easier to manipulate complex data structures. Let's break down these concepts and how you might see them show up in your codebase.

### Memory Handles
- **Memory Allocation:** DBL uses memory handles to refer to dynamically allocated memory blocks. These blocks can vary in size and are identified by numeric handles.
- **%MEM_PROC Function:** This function allows for allocating (`DM_ALLOC`), reallocating (`DM_RESIZ`), and freeing (`DM_FREE`) memory. Additionally, you can query the size of a memory block (`DM_GETSIZE`) or register external memory segments (`DM_REG`). `DM_REG` is probably a weird one to see in the wild but its needed if you want to work with memory allocated into a raw pointer from another language, such as C.

### Mapping Structures with ^M and ^MARRAY
- **^M Operator:** This operator "maps" a structure to a specified data area or memory handle. It aligns the first character of the data area with the start of the structure and creates a field descriptor that references this data. If the mapped field exceeds the bounds of the data area, an error is triggered.
- **^MARRAY Function:** This function extends the concept of ^M to arrays. It maps a field or structure over a memory handle multiple times, returning an array of descriptors. The size of the allocated area determines the number of elements in the array. However, the array's lifetime should not exceed that of the handle.

### Mapping over an Alpha

### That weird pattern where you have a local structure that contains an a1 field and you overindex it to whatever size you want at runtime

### TODO Whats this stuff used for in the wild?

### History Lesson

### TODO How can you use dynamic arrays, ArrayLists and structures to do this better

### Samples and Diagrams
1. **Sample Programs:**
   - **Basic Memory Allocation:** Demonstrate allocating a memory block and accessing it with a numeric handle.
   - **Structure Mapping:** Show how to map a structure over a memory block, modify the structure, and reflect these changes in the memory.
   - **Array Mapping with ^MARRAY:** Illustrate creating an array of structures or fields using ^MARRAY and accessing individual elements.

2. **svgbob Graphics:**
   - **Memory Block Visualization:** An ASCII diagram showing a memory block and its handle, illustrating the concept of memory allocation.
   - **Structure Mapping Diagram:** A graphic representing the alignment of a structure to a memory block using ^M, showing the start of the structure aligned with the beginning of the memory block.
   - **Array Mapping Illustration:** A diagram depicting how ^MARRAY maps a structure over a memory block, showing the repeated instances of the structure within the allocated memory.
