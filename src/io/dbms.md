# DBMS
You've already seen hints of how to create ISAM files in prior sections of this book. In this section, we'll dive deeper and also look at some of the tools that are available to help you manage your ISAM files.

## Creation and Definition of ISAM Files
The `bldism` utility is central to this process, offering a way to create ISAM files from the command line. This can be done through keyboard input, parameter files, or an ISAM Definition Language (XDL) keyword file. 

The ISAM Definition Language (XDL) is another mini language for you to learn, fortunately it's pretty simple. XDL is a keyword-driven language where each keyword represents a specific attribute of the ISAM file. For instance, the `FILE` keyword indicates the beginning of the XDL description, and `NAME filename` specifies the name of the ISAM file you are creating. The `ADDRESSING` keyword defines the address length of the file. This choice used to be important but now you should really just choose `48`, commonly referred to as a TByte file. `PAGE_SIZE` sets the size of the index blocks. You should not choose a number smaller than your native disk block size, today this is almost always exposed as `4096`. Understanding these keywords and their impact on the ISAM file structure is vital for effective database management.

Let’s consider an example of creating a basic ISAM file. To begin, you would start with the `FILE` keyword to indicate the initiation of an ISAM file definition. Following this, you might specify `NAME my_isam_file`, giving your file a unique identifier. The `ADDRESSING` attribute could be set to `48` for a large file size capability, and `PAGE_SIZE` set to `4096` for optimal indexing performance. Additional keywords would be added as required to define the structure and attributes of the file, such as `KEYS` to determine the number of keys, or `RECORD` to define the data records.

An XDL file for a simple ISAM file might look something like this:

```
FILE
    NAME            my_isam_file
    ADDRESSING      48
    PAGE_SIZE       4096
    KEYS            1
RECORD
    SIZE            100
    FORMAT          fixed
KEY 0
    START           1
    LENGTH          10
    TYPE            alpha
```

In this example, a file named `my_isam_file` is created with 48-bit addressing and a page size of 4096 bytes. It's defined to have one key, and the record section indicates a fixed format with a size of 100 bytes. The key definition starts at position 1, has a length of 10 characters, and is of alpha type. Choosing the data type of the key changes how the data is ordered in the file. For instance, if the key is numeric, the data is ordered numerically. If the key is alpha, the data is ordered alphabetically. 

Here's a cheat sheet for XDL:

#### Basic Structure
- **FILE**: Begins the ISAM file definition.
- **RECORD**: Starts the record definition.
- **KEY n**: Defines the nth key (n is the key number).

#### File Attributes
- **NAME [filename]**: Sets the name of the ISAM file.
- **ADDRESSING [32|40|48|NO40|NO48]**: Sets the file address length.
- **PAGE_SIZE [size]**: Sets the size of the index blocks (e.g., 512, 4096, 8192).
- **KEYS [num_keys]**: Specifies the number of keys (1 to 255).

#### Optional File Attributes
- **ERASE_ON_DELETE [yes|no]**: Controls record erasure on deletion.
- **NETWORK_ENCRYPT [yes|no]**: Enables/disables network encryption.
- **ROLLBACK [yes|no]**: Controls rollback functionality.
- **SIZE_LIMIT [max_size]**: Sets the maximum file size in MB.
- **RECORD_LIMIT [recs]**: Sets the maximum number of records.
- **TRACK_CHANGES [yes|no]**: Enables/disables change tracking.
- **TEXT ["text_string"]**: Adds text to the file header.
- **TEXT_ALLOCATION [text_size[K]]**: Allocates space for user-defined text.
- **DENSITY [file_density]**: Sets the packing density of index blocks.
- **RESILIENT [yes|no]**: Enables constant synchronization between index and data.
- **FULLRESILIENT [yes|no]**: Enhances resilience with direct disk writes.
- **STATIC_RFA [yes|no]**: Indicates if records have constant RFAs.
- **STORED_GRFA [yes|no]**: Specifies storing of CRC-32 in record header.
- **PORT_INT [pos:len]**: Defines position and length of nonkey integer data.

#### Record Attributes
- **SIZE [rec_size]**: Sets the size of the data records.
- **FORMAT [fixed|multiple|variable]**: Specifies record format.
- **COMPRESS_DATA [yes|no]**: Enables/disables data compression.

#### Key Definition
- **START [pos_1[:pos_n]]**: Sets start positions for key segments.
- **LENGTH [len_1[:len_n]]**: Defines lengths of key segments.
- **TYPE [type_1[:type_n]]**: Specifies types for key segments (e.g., alpha, integer, decimal).
- **ORDER [order_1[:order_n]]**: Sets sort order for key segments.
- **NAME [key_name]**: Assigns a name to the key.
- **DUPLICATES [yes|no]**: Specifies if duplicate keys are allowed.
- **DUPLICATE_ORDER [fifo|lifo]**: Sets order for duplicate key records.
- **MODIFIABLE [yes|no]**: Indicates if the key is modifiable.
- **NULL [replicate|noreplicate|short]**: Defines null key behavior.
- **VALUE_NULL [null_val]**: Sets the null value for a null key.
- **DENSITY [key_density]**: Sets the packing density for the current key.
- **SEED [start_value]**: Sets starting value for a sequence autokey.

#### Usage Notes
- Comments start with an exclamation point.
- Keywords and values can be abbreviated but must be distinguishable.
- Use semicolons or carriage returns to separate keywords and values.
- Keywords must follow their section: File keywords after `FILE`, record keywords after `RECORD`, key keywords after `KEY`.

### Disaster Recovery: Handling Data and Index Corruption in ISAM Files
Sometimes, despite your best efforts, things go wrong. In the case of ISAM files, this can mean data corruption. Corruption can occur in the data file, the index file, or both. The `isutl` utility is a powerful tool for identifying and recovering from ISAM file corruption. It's a command-line utility that can be used to perform a variety of operations on ISAM files, including verification, recovery, and maintenance.

#### Common Causes of ISAM File Corruption
**Forced Termination** Abrupt termination of programs (e.g., using `kill -9` in Unix/Linux or Task Manager in Windows) while they are writing data.
**Hardware Failures** Issues such as disk crashes, power outages, or network disruptions during data operations.

#### Identifying Corruption with Isutl
**Using Isutl -v**
- This command verifies the integrity of Revision 4 or higher ISAM files using optimized methods.
- If `isutl` detects potential data access issues, it raises an error and halts the operation.
- For a thorough check, the `-z` option performs a linear scan, but it's more time-consuming and should be used judiciously, typically under the guidance of Synergex Developer Support.

#### Recovery Strategies Using Isutl
**Basic Re-indexing (Isutl -r)** This command re-indexes the ISAM file without altering the data file, including Record File Addresses (RFAs). It's the first step in recovery, especially when there's no evident data corruption.

**Extended Recovery (Isutl -r with -d)** When corruption in the data file is confirmed or suspected, combining `-r` with `-d` ensures a more rigorous recovery process. It involves low-level checking and validation of the data file during recovery. If you have SGRFA enabled, this option also checks the CRC-32 checksums in the record headers.

#### Preventative Measures and Best Practices
1. **Regular Backups**: Frequent and systematic backups are essential for disaster recovery.
2. **Avoid Abrupt Termination**: Ensure safe shutdown of applications and databases to prevent data corruption.
3. **Hardware and Network Reliability**: Invest in reliable hardware and stable network infrastructure.
4. **Monitor System Health**: Regularly check for signs of disk or network issues.
5. **Access Control**: Limit direct file access to trained personnel and use application-level interfaces for database interactions.
6. **Regular Maintenance**: Use tools like `isutl` for routine checks to identify and fix minor issues before they escalate.
7. **Disaster Recovery Plan**: Have a clear, tested plan in place for different scenarios of data loss or corruption. If you don't execute the plan from time to time, you don't really have a plan.

By understanding the causes of ISAM file corruption and utilizing `isutl` effectively for both identification and recovery, you can significantly mitigate the risks associated with data loss. Coupled with preventative strategies and best practices, these measures provide a robust framework for managing ISAM file integrity and ensuring data reliability in DBL environments.

### Stronger Data Integrity
The RESILIENT and FULLRESILIENT options in DBL offer enhanced safety for file operations, particularly in environments where data integrity is paramount. However, these options come with trade-offs in terms of performance and system resource utilization. 

### RESILIENT Option
- **Implementation**: The RESILIENT option ensures that during file updates, such as writing or deleting records, there is a constant synchronization between the data file and the index file. This feature enhances the integrity of ISAM files by ensuring that both index and data are consistently in sync.
- **Trade-offs**: 
  - **Performance Impact**: There is a moderate performance overhead due to the additional synchronization steps. This might affect high-throughput scenarios where speed is critical.
  - **System Resources**: Increased use of system resources to maintain the synchronization, though typically not to a degree that would significantly impact overall system performance.
- **Safety Offered**: It provides a robust safeguard against data inconsistencies, especially in scenarios where a system crash or abrupt termination could leave the data in an inconsistent state.

### FULLRESILIENT Option
- **Implementation**: FULLRESILIENT builds upon the RESILIENT feature. In addition to maintaining synchronization between data and index files, it ensures that all write operations are immediately flushed to the disk. This is achieved using specific file open flags - `FILE_FLAG_WRITE_THROUGH` on Windows and `O_DSYNC` on UNIX.
- **Trade-offs**: 
  - **Performance Impact**: FULLRESILIENT can significantly slow down disk access. Every write operation incurs the overhead of ensuring data is written to the disk, which can be a bottleneck in write-intensive applications.
  - **System Resources**: The demand on system resources is higher compared to the RESILIENT option due to the continuous disk write operations.
- **Safety Offered**: This option offers the highest level of data safety. In the event of a system failure, FULLRESILIENT minimizes the risk of data corruption or loss, as all changes are immediately committed to the disk.

### Making the Right Choice
- **Assessing Needs**: The choice between RESILIENT and FULLRESILIENT should be based on the specific requirements of the application and the environment in which it operates. For applications where data integrity is more critical than performance, FULLRESILIENT is suitable. In contrast, if the application demands higher throughput and can tolerate a slight risk of data inconsistency in the event of a failure, RESILIENT might be the better choice.
- **Understanding the Environment**: The impact of these options also depends on the underlying hardware and system architecture. Systems with faster disk write capabilities and high-performing I/O subsystems can mitigate some of the performance penalties associated with FULLRESILIENT.
- **Balancing Trade-offs**: It’s essential to balance the trade-offs between data safety and application performance. In many cases, a thorough testing and analysis phase might be required to understand the impact of these options on the overall system performance and data integrity.

In summary, RESILIENT and FULLRESILIENT offer different levels of safety for data operations in DBL. While RESILIENT ensures synchronization between data and index files, FULLRESILIENT goes a step further by writing changes immediately to the disk. The choice between them should be guided by the specific data integrity requirements and performance expectations of the application.