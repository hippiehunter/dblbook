# Input/Output (I/O) Operations

In this chapter, we delve into the input/output (I/O) operations available and commonly used by DBL programmers. I/O operations form the backbone of any application, providing essential capabilities to interact with various data sources and destinations. Our journey will traverse the spectrum of I/O functionalities in DBL, starting from the basics of simple terminal I/O, advancing through the intricacies of handling raw and ISAM files, and culminating in networked file access and semi relational data manipulation using the powerful `Select` class. By exploring these built-in routines and classes, you will gain a robust understanding of how DBL stores, receives and accesses data, whether it be reading user input from the console, managing large volumes of data in ISAM files. This chapter is designed not only to impart technical knowledge but also to equip you with practical skills for transforming your codebase to best take advantage of modern hardware.

A core concept essential for understanding input/output (I/O) operations is the mechanism of channels. These channels are at the heart of how DBL manages various types of I/O operations, whether they are related to files or terminal interactions. To kick off the topic of I/O, let's delve into a few key aspects of channels:

### Numeric Representation of I/O Handles
- **Range:** In traditional DBL platforms, I/O channels are numerically designated and range from 1 to 1024. In DBL implementations on the .NET framework, this range extends up to 8096.
- **Function:** Each channel number uniquely identifies an I/O stream. This could be a file, a terminal session, a network port, or another input/output device.

### The OPEN Statement
The `OPEN` statement in DBL is used to initiate an I/O operation. It associates a specific channel number with a file or device, setting up the parameters for subsequent I/O interactions. The syntax for the commonly used parts of the `OPEN` statement is as follows:

`OPEN(channel, mode_spec, file_spec) [error_list]`

### Logicals
TODO: Write something about the usage of logicals in the `OPEN` statement. DAT: bla.ism

### Case Sensitivity and extensions
TODO: Write something about default extensions and case sensitivity on linux platforms

### Dynamic Channel Allocation
- **Automatic Assignment:** DBL provides a feature when opening a file or terminal, if the channel variable is set to zero, the runtime automatically assigns the next available channel number. This channel number is then updated in the variable. This is the only thread-safe way to allocate channels. Usage of `%syn_freechn` is strongly discouraged, due to the very subtle, difficult to track down, catastrophic bugs that will be introduced by using it.
- **Search Order:** The runtime searches for a free channel starting from the highest number (1024 or 8096) and moves downwards.
- **Data Type Requirements:** The variable intended to hold the channel number must be capable of storing a four-digit number to avoid a BIGNUM error. Older codebases may use `d` fields for this purpose, but it's recommended to use `i4` fields instead.

### Application in Terminal and File I/O
- **Terminal I/O:** For terminal operations, channels link to terminal sessions, enabling data to be read from or written to the terminal. You specify that you're opening a terminal channel by passing "tt:" as the file specification.
- **File I/O:** In file operations, channels are used to perform all of the basic file operations. A given file can be opened multiple times each with its own independent channel number.

### Channel Context
Particularly with file channels, there is state the follows a channel. For example the current position in the opened file along with any record locks that have been taken. We'll get into much more detail later in this chapter.
