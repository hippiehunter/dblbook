# ISAM
Indexed Sequential Access Method (ISAM) files represent a cornerstone in database file organization, offering a blend of sequential and direct access methods. Essentially, ISAM files are structured to facilitate both efficient record retrieval and ordered data processing. They consist of two primary components: the data records themselves and a set of indexes that provide fast access paths to these records. This makes ISAM files particularly suitable for customer databases, inventory systems, and any context where data needs to be quickly accessed using keys yet also iterated in a specific order. The power of ISAM lies in its ability to handle large volumes of data with quick access times. Synergy ISAM files support multiple keys on a single file, which allows for fast lookups even when you have multiple access patterns. As we delve deeper into the workings of ISAM files in DBL, it's helpful to think of your application as running inside the database, rather than just querying it from a distance. I'll try to point out the places where this matters most as we work though this chapter.

## CRUD
CRUD, an acronym for create, read, update, and delete, represents the essential set of operations in database management and application development, forming the cornerstone of most data-driven systems by enabling the basic interaction with stored information. ISAM files in DBL support all four of these operations, and we'll cover these basics and a few extras in this section. 

### Common concepts
- **Channel**: All of these operations require an open channel to the ISAM file. This channel is used to identify the file and track its state (for example, the current position in the file and any locks that have been taken). 
- **Data area**: If you're reading, creating, or updating a record, you'll need a data area to hold the record. This is a variable that is defined as a structure or alpha that matches the size of the records in the file.
- **GETRFA**: Returns the record’s global record file address (GRFA). If you pass an argument with space for 6 bytes, this will be filled with the RFA. If you pass an argument with space for 10 bytes, it gains a 4-byte hash that can be used to implement optimistic locking. This feature will be covered in more detail later in this chapter. TODO: mention the stability that comes from static rfas and also the loss of them if you rebuild a file
- **error_list** This argument allows you to specify an I/O error list for robust error management, which ensures the program can handle exceptions like locked records or EOF conditions gracefully.

### Create
`STORE(channel, data_area[, GETRFA:new_rfa][, LOCK:lock_spec]) [[error_list]]`

The STORE statement adds new records to an ISAM file. The statement requires an open channel in update mode (U:I) and a data area that contains the record to be added.

**Optional qualifiers**:
- **GETRFA**: This optional argument is used to return the record file address (RFA) of the newly added record, which can be essential for subsequent operations like updates or deletions.
- **LOCK**: While automatic locking is not supported for STORE, this optional qualifier can be used to apply a manual lock to the newly inserted record. 

When executing a STORE, DBL checks for key constraints. For instance, if the ISAM file is set to disallow duplicate keys, attempting to store a record with a duplicate key will trigger a "Duplicate key specified" error (\$ERR_NODUPS). It's important to note that if the data area exceeds the maximum record size defined for the ISAM file, an "Invalid record size" error (\$ERR_IRCSIZ) is raised, and the operation is halted.

**Transactional capability**:
The combination of the `LOCK` qualifier and the `GETRFA` argument in a STORE statement lends a degree of transactional capability to the operation. The lock ensures that the record remains unaltered during subsequent operations, while the RFA provides a reference to the specific record. This is not commonly seen in DBL applications, but it's worth noting in case you find yourself in a situation where you need to implement some form of transactional capability.

**Basic example**:
> These examples make use of the ISAMC subroutine to create a new ISAM file with a single key. ISAMC will be explained later in more detail but for now we'll just use it without explanation
TODO: Add example

```dbl

```

### Read
```
READ(channel, data_area, key_spec[, KEYNUM:key_num][, LOCK:lock_spec]
  [, MATCH:match_spec][, POSITION:position_spec][, RFA:rfa_spec]
  [, WAIT:wait_spec]) [[error_list]]
```

The READ statement is used to read a specified record from a file that's open on a given channel. It's vital for accessing data in ISAM files, where the channel must be opened in input, output, append, or update mode.
- **Key arguments**:
  - `channel`: Specifies the channel on which the file is open.
  - `data_area`: A variable that receives the information from the read operation.
  - `key_spec`: Defines how the record is located, either by its position, key value, or qualifiers like `^FIRST` or `^LAST`.

**Additional qualifiers**:
- **KEYNUM**: Optional. Specifies a key of reference for the read operation.
- **LOCK**: Optional. Determines the locking behavior for the record being read.
- **MATCH**: Optional. Defines how the specified key is matched, which is crucial for precise data retrieval.
- **POSITION**: Optional. Allows you to position to the first or last record in the file rather than doing a key lookup.
- **RFA**: Optional. The RFA argument in the READ statement specifies the exact address of a record to be read directly from the file, facilitating targeted data retrieval. The size of the RFA argument changes its behavior: a 6-byte RFA represents just the locator, pinpointing the record's location, while a 10-byte RFA includes both the locator and a hash value. The hash serves as a data integrity check; if the record at the specified address doesn't match the hash, indicating that the data has been altered or is no longer valid, a "Record not same" error (\$ERR_RECNOT) is triggered.
- **WAIT**: Optional. Specifies how long the program should wait for a record to become available if it's locked. This is more efficient than calling READ in a loop with a SLEEP statement because the call will be blocked until the record is available rather than waiting the entire sleep duration and then checking again.

**Functional insights**:
- The `LOCK` qualifier specifies whether a manual lock is applied to the record. This feature is critical in concurrent environments where data integrity is paramount.
- The `MATCH` qualifier defines how a record is located. This determines the behavior when an exact match isn't found. For historical reasons, the default behavior is `Q_GEQ`, but that is likely to lead to an unpleasant surprise for someone writing new code. The following options are available:
  - **Q_GEQ (0)**: Finds a record with a key value greater than or equal to the specified key. If an exact match isn't found, it reads the next higher record, potentially raising a "Key not same" error (\$ERR_KEYNOT) if no exact match is found or an “End of file” error if no higher record exists.
  - **Q_EQ (1)**: Searches for a record that exactly matches the specified key value. If there's no exact match, it triggers a "Record not found" error (\$ERR_RNF).
  - **Q_GTR (2)**: Looks for a record with a key value greater than the specified key. It only raises an "End of file" error if it reaches the end of the file without finding a matching record.
  - **Q_RFA (3)**: Locates a record using the record file address specified in the RFA qualifier.
  - **Q_GEN (4)**: Finds a record whose key value is equal to or the next in sequence after the specified key. An "End of file" error occurs if no such records are found.
  - **Q_SEQ (5)**: Retrieves the next sequential record after the current one, regardless of the key specification. It raises an “End of file” error only if it reaches the end of the file.

**Practical application**:
When you lock a record after a READ operation in a database, you are essentially ensuring that the data remains unchanged until your next action, be it a DELETE or a WRITE. This lock is how you maintaining data consistency because it prevents other users or processes from modifying the record during this interim period. In environments where multiple transactions occur simultaneously, locking is key to preventing conflicts. Without locking, there’s a risk that another transaction might alter the data after you've read it but before you've had the chance to update or delete it, leading to inconsistencies. By locking the record, you ensure that any subsequent WRITE or DELETE reflects the state of the data as it was when you first read it, thereby preserving the accuracy and integrity of your database operations. It's very important that applications not keep records locked for indefinite periods of time. This can lead to deadlocks, performance issues, and frustrated users.

On the other side of locking, the `WAIT` argument exists to help you gracefully handle locked records in multi-user environments or scenarios involving concurrent data access. This argument specifies the duration for which the READ operation should wait for a locked record to become available before timing out.

In terms of error control, appropriate use of the `WAIT` argument can prevent your application from failing immediately when encountering locked records. By setting a reasonable wait time, you allow your application to pause and retry the READ operation, thereby handling temporary locks effectively without leading to spurious errors or immediate application failure.

However, developers need to be careful to avoid potential infinite loops or excessively long wait times. This caution is particularly important in high-throughput systems where waiting for extended periods can significantly impact performance. One approach is to implement a retry logic with a maximum number of attempts or a cumulative maximum wait time. After these thresholds are reached, the application can either log an error, alert the user, or take alternative action. This strategy ensures that the application retries sufficiently to handle temporary locks but not indefinitely, thus maintaining a balance between robust error handling and application responsiveness.

### Update
`WRITE(channel, data_area[, GETRFA:new_rfa]) [[error_list]]`

The WRITE statement in DBL is used to update a record in a file. When you execute a WRITE operation on a channel that is open in output, append, or update mode, the specified record is updated with the contents of the data area.

`GETRFA` is optional. It returns the RFA after the WRITE operation, which is useful in files with data compression, for variable-length records, or when trying to get the new hash for the updated record.

**Constraints**:
The record being modified must be the one most recently retrieved and locked. Modifying unmodifiable keys or primary keys directly with WRITE is not allowed and will result in errors. If the data area exceeds the maximum record size, an “Invalid record size” error (\$ERR_IRCSIZ) occurs.

#### Immutable keys
Individual keys in an ISAM file can be marked as immutable, meaning they cannot be updated. This constraint is enforced when calling WRITE on a record with an immutable key. Immutable keys are a choice in database design that can play a role in maintaining data integrity, ensuring consistency, and simplifying database management.

**Immutable as identifiers**: One primary reason to enforce immutable keys is to preserve the integrity of unique identifiers. In many database designs, certain key fields serve as definitive identifiers for records, akin to a Social Security number for an individual. Allowing these key values to change can lead to complications in tracking and referencing records, potentially causing data integrity issues. For example, if a key field used in foreign key relationships were allowed to change, it could create orphaned records or referential integrity problems.

**Avoiding designs with complex updates**: Allowing keys to change can lead to complex update scenarios where multiple related records need to be updated simultaneously. This can increase the complexity of CRUD operations, making the system more prone to errors and harder to maintain. By enforcing immutability, developers can simplify update logic, as they don’t have to account for cascading changes across related records or indexes.

### Delete

#### Soft delete vs hard delete
Delete is a pretty fundamental operation in DBL applications, but your codebase has probably already decided how it's going to handle deletes. We're going to cover both soft and hard approaches here to help you better understand the tradeoffs that your application's designers originally made. 

### Soft deletes
Soft delete is a form of delete where a record is not physically removed from the database; instead, it's marked as inactive or deleted. This is usually implemented using a flag, such as `is_deleted`, or a timestamp field, like `deleted_at`, to indicate that the record is no longer active. If you need to delete a record in a soft-delete system, the order of operations is as follows:
1. READ the record.
2. Update the field in the record to mark it as deleted.
3. WRITE the record back to the file.

**Advantages**:
1. **Data recovery**: Soft deletes allow for easy recovery of data. Mistakenly deleted records can be restored without resorting to backup data, which is especially useful in user-facing applications where accidental deletions might occur.
2. **Audit trails and historical data**: Maintaining historical data is essential in many systems for audit trails. Soft deletes enable you to keep a complete history of all transactions, including deletions, without losing the integrity of historical data.
3. **Referential integrity**: In databases with complex relationships, soft deletes help maintain referential integrity. Removing a record might break links with other data. Soft deletes prevent such issues, ensuring that all relationships are preserved.

### Hard deletes
Hard delete is the complete removal of a record from the database. Once a record is hard deleted, it is permanently removed from the database table.

`DELETE(channel) [[error_list]]`

The DELETE statement is used to remove a record from a file. DELETE requires an open channel in update mode (U:I) positioned to a record that previously has been locked. If you need to delete a record in a hard delete system, the order of operations is as follows:
1. READ or FIND the record with automatic locking or manual locking enabled.
2. Call DELETE on the same channel as the READ or FIND operation.

**Considerations**:
1. **Space efficiency**: Hard deletes free up space in the database, making them suitable for applications where data storage is a concern.
2. **Simplicity**: Hard deletes can be simpler to implement and manage, as they involve straightforward removal without the need for additional mechanisms to handle the deleted state.
3. **Privacy compliance**: For certain data, especially personal or sensitive information, hard deletes might be necessary to comply with privacy regulations like GDPR, where individuals have the "right to be forgotten."

### Why opt for soft deletes

A system might be designed to use soft deletes for several reasons:
1. **Undo capability**: Soft deletes provide an "undo" option, which is crucial in many business applications for correcting accidental deletions without resorting to more complex data restoration processes.
2. **Data analysis and reporting**: Having historical data, including records marked as deleted, can be valuable for trend analysis and reporting. Soft deletes allow you to maintain and query this historical data.
3. **System integrity**: In systems with intricate data relationships, soft deletes help maintain the integrity of the system by ensuring that deleting one record does not inadvertently impact related data.

## Beyond CRUD

### Sequential access

Sequential access in databases, exemplified by the READS statement in DBL, is a method where records are processed in an order determined by a key. For certain tasks, this approach is particularly advantageous, versus reading individual records one at a time, for several reasons:

1. **Efficiency in processing ordered data**: When data needs to be processed in the order it's stored (such as chronological logs or sorted lists), sequential access allows for a streamlined and efficient traversal. It eliminates the overhead associated with locating each record individually based on specific criteria or keys.

2. **Optimized for large-volume data handling**: Sequential access is ideal for operations involving large datasets where each record needs to be examined or processed. Accessing records in sequence reduces the computational cost and complexity compared to individually querying records, especially in scenarios like data analysis, report generation, or batch processing.

3. **Simplicity and reduced complexity**: Implementing sequential access in applications simplifies the code, as it follows the natural order of records in the file. This contrasts with the more complex logic required for random or individual record access, where each read operation might require a separate query or key specification.

```
READS(channel, data_area[, DIRECTION:dir_spec][, GETRFA:new_rfa][, LOCK:lock_spec]
  [, WAIT:wait_spec]) [[error_list]]
```

**Basics of READS**:
- The READS statement is designed to retrieve the next sequential record from a file, based on the collating sequence of the index of reference. This means that it reads records in either ascending or descending order, depending on the key's ordering.

**Navigating through records**:
- READS is particularly adept at navigating through records in a file, moving logically from one record to the next. It maintains a context of "next record," established through operations like READ and FIND.
- READS does not perform any filtering or matching; it simply reads the next record in the file based on the order of the key that was used to position it. If you want to stop reading records after the criteria is no longer matching, you'll need to check manually against the data area or use the Select class.

**Directional reading**:
- With the `DIRECTION` qualifier, READS offers the ability to traverse records in reverse order. In the distant past before this functionality existed, keys had to be declared reverse order to support this access pattern, so you may still see files where there are two keys that differ only by the declared order.

### Error handling and record locking

**Handling EOF and errors**:
- When READS encounters the end (or beginning, when reverse reading) of the file, it triggers an “End of file” error (\$ERR_EOF). This error sets the current record position to undefined, so it's important to handle it appropriately. This can be done by checking for the \$ERR_EOF error and taking appropriate action, such as exiting the loop or performing cleanup operations.

**Record locking**:
- In update mode, READS automatically locks the retrieved record, ensuring that the data remains consistent during any subsequent update operations. TODO: rewrite This automatic locking is crucial in scenarios where data integrity is paramount, particularly in multi-user environments.

### Best practices and considerations
Consider using Select instead of READS for sequential access, especially if you need to filter the records you're reading. Select is covered in more detail later in this chapter. TODO: there are more best practices for READS to cover here

## Repositioning, checking for existence, or targeted locking
The FIND statement is designed to position the pointer to a specific record in a file, setting the stage for its subsequent retrieval. Unlike the READ statement, which fetches the record's data immediately, FIND simply locates the record, allowing the next READS statement on the same channel to retrieve it.

```
FIND(channel[, record][, key_spec][, GETRFA:new_rfa][, KEYNUM:krf_spec]
&   [, LOCK:lock_spec][, MATCH:match_spec][, POSITION:pos_spec]
&   [, RFA:match_rfa][, WAIT:wait_spec]) [[error_list]]
```

**Key parameters**:
- **record**: Don't use this argument.
- **key_spec**: This works the same as READ and is used to specify the key to use for locating the record.
  
**Special qualifiers**:
These special qualifiers all work the same as READ and are used to control the behavior of the FIND operation.
- **KEYNUM**
- **LOCK**
- **MATCH**
- **POSITION**
- **RFA**
- **WAIT**

**Positioning vs retrieving**:
- While READ retrieves and locks the record in one operation, FIND only attempts to position the pointer to the record. 

**Use cases for FIND**:
- FIND will set the context for a future READS operation. This has been used in applications to slightly simplify the READS loop to avoid the specialized initial step of dealing with the first record.
- FIND is useful if you need to check for the existence of a record without retrieving it, for example, if you want to check for duplicates or for the existence of a record before creating it.
- FIND is also useful if you need to lock a record without retrieving it, for example, for optimistic locking or for locking a record before overwriting it.

**Locking behavior**:
- By default, FIND does not lock the located record unless explicitly specified with the `LOCK` qualifier or enabled through compiler options. READ, on the other hand, automatically locks the retrieved record if used on a channel opened for update.

## Keyed lookups

Keyed lookups in ISAM files are a fundamental aspect of database operations in DBL, offering a distinct approach compared to non-ordered keyed lookups, such as those in a hash table. Understanding these differences, as well as the concept of partial keyed reads, is crucial for database developers. 

### Keyed lookups in ISAM files

**Ordered nature of ISAM lookups**:
- ISAM files in DBL utilize ordered keys for data retrieval, meaning the records are sorted based on the key values. This ordered structure enables efficient range queries and sequential access based on key order. For example, you can quickly locate a record with a specific key or navigate through records in a sorted manner.

**Comparison with hash table lookups**:
- Unlike ISAM files, hash tables are typically unordered. A hash table provides fast access to records based on a hash key, but it doesn't maintain any order among these keys. While hash tables excel in scenarios where you need to quickly access a record by a unique key, they don't support range queries or ordered traversals as ISAM files do.

**Efficiency considerations**:
- In scenarios where the order of records is important, ISAM files are more efficient than hash tables. The ability to perform range scans and ordered retrievals makes ISAM files preferable for applications where such operations are frequent.

### Partial keyed reads in ISAM

**Understanding partial keyed reads**:
- Partial keyed reads in ISAM files allow for searches based on a portion of the key, starting from the leftmost part. This means you can perform lookups using only the beginning segment of the key, and the search will return records that match this partial key.

**Left-to-right application in keyspace**:
- The key structure in ISAM files is significant when it comes to partial keyed reads. The search considers the key's structure from left to right. For instance, if you have a composite key made of two fields, say, region and department, a partial key search with just the region will return all records within that region, regardless of the department.

**Use case scenarios**:
- Partial keyed reads are particularly useful in scenarios where data is hierarchically structured. For instance, if you're dealing with geographical data, you might want to retrieve all records within a certain area without specifying finer details initially. Partial keyed reads allow this level of query flexibility.

```svgbob
+---------------------------------------------------+
| EmployeeRecord                                    |
+-------------------+-------------------------------+
| id (4 bytes)      | [0000]                        |
| dept (4 bytes)    | [dept] (2 chars)              |
| region (4 bytes)  | [region] (4 chars)            |
| manager (4 bytes) | [0001] (4 bytes of id)        |
+-------------------+-------------------------------+

+---------------------+
| Ordered By id       |
+---------------------+
| [0000EXECWEST0000]  |
| [0001HR  WEST0001]  |
| [0002DEV EAST0001]  |
| [0003HR  CENT0001]  |
| [0004HR  MNT 0001]  |
| [0005HRI MNT 0004]  |
| [0006HR  MNT 0003]  |
+---------------------+

+---------------------+
| Ordered By dept     |
+---------------------+
| [0002DEV EAST0001]  |
| [0000EXECWEST0000]  |
| [0004HR  MNT 0001]  |
| [0001HR  WEST0001]  |
| [0003HR  CENT0001]  |
| [0006HR  MNT 0003]  |
| [0005HRI MNT 0004]  |
+---------------------+

+---------------------+
| Ordered By region   |
+---------------------+
| [0003HR  CENT0001]  |
| [0002DEV EAST0001]  |
| [0004HR  MNT 0001]  |
| [0006HR  MNT 0003]  |
| [0005HRI MNT 0004]  |
| [0000EXECWEST0000]  |
| [0001HR  WEST0001]  |
+---------------------+

+-------------------------+
| Ordered By manager + id |
+-------------------------+
| [0000EXECWEST0000]      |
| [0001HR  WEST0001]      |
| [0002DEV EAST0001]      |
| [0003HR  CENT0001]      |
| [0004HR  MNT 0001]      |
| [0006HR  MNT 0003]      |
| [0005HRI MNT 0004]      |
+-------------------------+
```

In this diagram
- Each `EmployeeRecord` is represented with different key orderings: by `id`, `dept`, `region`, and `manager`.
- The records are sorted based on each key ordering, demonstrating how the positioning of the key fields affects the sorting and access pattern.
- The sorting order shows how queries and partial key reads would be efficient for different key combinations, illustrating the significance of key ordering in database design and querying.
- The sort order of a composite key (`manager + id`) shows that if you partially key read on the `manager` field, you'll get all the records for that manager in order by `id`.
- Region as defined here must be a non-unique key because there are multiple records with the same region. This is a common pattern in ISAM files where you have both a unique key and a non-unique key that is used for range queries. This is also true for `dept` but not for `id` or (`manager + id`).
- FIND and READ can specify a partial key value in order to position on the first matching record in a sequence. READS will advance to the next record in the file based on the ordering of the key, but it will not filter out records that don't match the partial key value.

If you wanted to access records by the keys in the diagram above, you would specify the `KEYNUM` argument to READ or FIND. The mapping of the KEYNUM argument to the key in the file is literally the ordering you specified to ISAMC or the declaration order in your XDL file. It's a good idea to use a variable to hold this mapping so you can change the order of the keys in the file without having to change all of your code.

### Context-driven key design

**Analyzing user needs**:
- When designing keys, it’s essential to consider the user's perspective and the tasks they need to accomplish. For instance, if there’s a user role focused on managing inventory in a specific warehouse, it would be beneficial to key inventory records by warehouse ID. This approach allows for rapid access and management of all items within a given warehouse.

**Example**:
- In an inventory management system, if users frequently access all items in a specific warehouse, designing a composite key like `[WarehouseID, ItemID]` would optimize data retrieval for these users.

### Key design for data relationships

**Facilitating efficient joins**:
- Often, one of the main reasons for accessing a particular dataset is to join it with another. In such cases, it’s vital to design keys that can easily and accurately match the related records. The key should include fields that are present in the driving record of the join operation.

**Example**:
- Suppose you have an `Orders` table and a `Customers` table. If orders are frequently retrieved based on customer information, having a `CustomerID` in both tables as part of their keys allows for efficient joins between these tables.

**Considerations for composite keys in joins**:
- When using composite keys, ensure that the key components align with the fields used in join conditions. The order of elements in a composite key can affect the efficiency of the join operation, especially in ISAM databases where key order dictates access patterns.

## Optimistic concurrency
Optimistic concurrency control is a method used in database management where transactions are processed without locking resources initially, but there's a check at the end of the transaction to ensure no conflicting modifications have been made by other transactions.

**How GRFA facilitates optimistic concurrency**:
1. **Record identification**: When a record is read, its GRFA can be retrieved. This GRFA acts as a locator to get back to that record at a later time.
2. **Concurrent modifications**: While a transaction is processing a record, other transactions might also access and modify the same record. 
3. **Final validation**: At the point of updating or committing the transaction, the current GRFA of the record is compared with the GRFA obtained at the start. If the hash part of the GRFA has changed, it indicates that the record has been modified by another transaction since it was read.
4. **Conflict resolution**: In case of a mismatch in GRFA values, the transaction knows that a conflicting modification has occurred. The system can then take appropriate actions, such as retrying the transaction, aborting it, or triggering a conflict resolution mechanism.

### Practical implications

**Advantages**:
- Optimistic concurrency reduces the need for locking, thereby enhancing performance in environments with high concurrency. It allows multiple transactions to proceed in parallel without waiting for locks.
- It provides a mechanism to ensure data integrity without the overhead of locking resources for the duration of the transaction.

**Considerations**:
- While optimistic concurrency control increases throughput, it requires careful handling of conflict scenarios. Applications must be designed to handle cases where transactions are rolled back or retried due to GRFA mismatches.
- It's most effective in scenarios where read operations are frequent but actual conflicts are relatively rare.
- Harmony Core uses optimistic concurrency along with transactional rollback capability to ensure data integrity in concurrent environments.

**GRFA stability**:
- The RFA part will be stable as long as the file is not rebuilt. The hash part will be stable as long as the record is not modified. If the record is modified, the hash will change. If the file is rebuilt, the RFA will change. This means you should not store the GRFA or RFA in a file or database, because it will become invalid if the file is rebuilt. If you need to store a reference to a record, you should store a unique key value and use that to retrieve the record.

### SLEEP vs WAIT
Comparing the use of a SLEEP statement in a loop for retrying a read operation with a lock to employing a `WAIT` qualifier on a READ or READS statement reveals some key differences, particularly in terms of efficiency and resource management. When you use a SLEEP statement in a loop to retry a locked read operation, your approach is essentially a form of polling. In this scenario, the program repeatedly checks if the lock is released, interspersed with sleep intervals. While this method is straightforward, it can be inefficient, as it continuously consumes CPU cycles and can lead to increased response times due to the sleep duration.

On the other hand, utilizing a WAIT qualifier with a READ statement leverages operating system–level blocking I/O with a timeout. This approach is generally more efficient because it allows the operating system to suspend the execution of your thread until the lock is released or the timeout is reached. During this suspension, the CPU resources that would have been consumed by polling are freed up for other tasks. The blocking mechanism is more responsive as well, as the system can resume the thread's execution immediately after the lock is released, without waiting for the next polling interval. This method not only improves resource utilization but also tends to offer better overall performance, particularly in high-concurrency environments or applications where minimizing the response time is crucial.