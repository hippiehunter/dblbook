# Error List

In a Synergy DBL program, an I/O error list is associated with a file I/O statement. It details how the program should handle specific I/O errors by redirecting program control to error-handling labels. Here's the general syntax structure:

```
IO_statement(arguments)[error_list]
```

Where:

- `IO_statement`: This is the file I/O statement that the error list applies to, such as `READ`, `WRITE`, `OPEN`, `FIND`, etc.
- `error_list`: This is a comma-separated list of error handlers in the form of `error_code=label`. You can also specify a catch-all error handler if you just put a label at the end of the list.

**Components of the Error List**

- `error_code`: This is a specific error condition you want to handle, such as `$ERR_EOF`, `$ERR_LOCKED`, `$ERR_NODUPS`, etc. It represents a class of error that the I/O operation might encounter.
- `label`: This is a user-defined label in the program that execution will jump to if the corresponding error occurs.

**Example of an I/O Error List:**

```dbl
READ(channel, recordArea, key) [$ERR_EOF=notfound_label, $ERR_LOCKED=retry_label, catch_all_label]
```

In the above example:

- `READ`: DBL I/O statement that attempts to read a record.
- `recordArea`: The variable into which the read data will be placed.
- `key`: The key value to use for the read operation.
- `channel`: The channel from which the data is being read.
- `$ERR_EOF=notfound_label`: If an end-of-file condition is encountered, control transfers to the `notfound_label`.
- `$ERR_LOCKED=retry_label`: If a record lock error occurs, control transfers to the `retry_label`.
- `catch_all_label`: If any other error occurs, control transfers to the `catch_all_label`.

**Extended Example with Multiple I/O Statements:**

```dbl
TODO add an example that has erorr labels for multiple io statements but doesnt suck
```
