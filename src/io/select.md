# Overview of Select

The `Select` class in DBL is a powerful and versatile tool for data retrieval, manipulation, and querying. It offers SQL-like capabilities within the DBL environment, allowing developers to perform complex data operations with ease. The `Select` class can be used in conjunction with the `FOREACH` statement for iterating over data sets, or individually for more specific data operations.

### Key components of Select

1. **From class**: Specifies the data source for the selection. It defines the file or database table to be queried and the structure of the records.

2. **Where class**: Used to set conditions for data selection. It allows filtering of records based on specified criteria, similar to the WHERE clause in SQL.

3. **OrderBy class**: Facilitates sorting of the retrieved data. It can order the data based on one or more fields, either in ascending or descending order.

4. **GroupBy class**: Enables grouping of records based on one or more fields. This is akin to the GROUP BY clause in SQL, providing a way to aggregate data.

5. **Join functionality**: Allows the combination of records from multiple data sources, akin to JOINs in SQL. This includes capabilities for inner joins and left outer joins.

6. **Sparse class**: Offers performance optimization, particularly for networked data access with xfServer. It allows partial record retrieval, reducing data transfer over the network.

## An example
For starters, we're going to need some data. Go grab the `employee.txt` and `employee.xdl` files from the `data` folder in the companion repository. This is a simple text file with some employee data in it and a par file to tell `fconvert` some info about our record. We're going to use this to create a simple ISAM file. To do this, we're going to use the `fconvert` utility. This is a command line utility that comes with Synergy/DE. It's used for creating and manipulating ISAM files. To create our file, make sure you're in a dbl command prompt, and then we're going to run the following command:
```console
fconvert -i1 employees.txt -oi employee -d employee.xdl
```

It shouldn't output anything to the screen if it worked correctly, but if you look in your project directory, you should see a new file named `employee.ism`. This is our ISAM file. Now that we have our data, let's write a program to read it. Create a new file named `select.dbl` in your project directory and add the following code:

```dbl
main
record emp_rec
    EmployeeID,   a5
    Name,         a30
    Department,   a20
    Salary,       d7.2
endrecord

proc
    record
        channel, i4      ; Channel for file access
        fromObj, @From   ; From object for Select
        whereObj, @Where ; Where object for Select
        selectObj, @Select
    endrecord

    ; Open the employee file
    open(channel=0, I:I, "employee.ism")

    ; Create a From object for the employee file
    fromObj = new From(channel, emp_rec)

    ; Define a Where clause (e.g., selecting employees from a specific department)
    whereObj = (Where)emp_rec.Department.eq."Sales"

    ; Create a Select object with the From and Where objects and 
    ; an OrderBy clause (e.g., ordering by Salary)
    selectObj = new Select(fromObj, whereObj, OrderBy.Descending(emp_rec.Salary))

    ; Iterate over the selected records
    foreach emp_rec in selectObj
    begin
        ; Process each record (e.g., display employee details)
        Console.WriteLine( "ID: " + emp_rec.EmployeeID + 
        &        ", Name: " + emp_rec.Name + 
        &        ", Department: " + emp_rec.Department + 
        &        ", Salary: " + %string(emp_rec.Salary))
    end

    ; Close the file channel
    close channel
end
```

Pretty easy right? You've already seen most of this stuff plenty of times before, so let's just talk about a few of the interesting bits. The `Where` clause lets you build your filter criteria using regular DBL syntax. Here are a few more valid `Where` snippets to drive that home.

```dbl, ignore, does_not_compile
whereObj = (Where)emp_rec.Department == "Sales"
whereObj = (Where)emp_rec.Department == "Sales" && emp_rec.Salary.gt.100000
whereObj = (Where)emp_rec.Department.eq."Sales".and.
&   emp_rec.Salary > 100000 || emp_rec.Salary < 50000
```

Notice that you can use the `&&` and `||` operators to build more complex criteria. You can also use the `eq` and `gt` operators instead of `==` and `>`. The choice of symbols or words is really a stylistic choice. As a comparison, the query in our program would look something like this in SQL:

```sql
SELECT * FROM employee WHERE Department = 'Sales' ORDER BY Salary DESC
```

## From
The `From` object acts as the primary binding link between the data area and the data source. When a `From` object is created, it is bound to a specific data area, which is defined by a record, group, or structure within the program. This binding process essentially establishes a mapping between the data structure in the program and the corresponding structure of the data source. You will use this bound data area when defining your `Where` and `OrderBy` clauses, as well as when accessing the data retrieved from the data source. It may feel slightly odd to have the binding expressed in the `From` object and also when iterating over the FOREACH loop, but if you follow the pattern of using the `From` object to define the data area and then using the data area in the FOREACH loop, you'll be fine. The `From` object maintains a connection to the original data area throughout its lifecycle, and therefore, the data area you specify must live at least as long as the `From` object you create.

## Changing things in the data
The `Select` class is not read only. If your file channel is opened for update, you can effectively use it to update and delete records. Let's revisit our example from above and add a few lines to update the salary of all the employees in the sales department. First change the OPEN statement to the following:

```dbl, ignore, does_not_compile
open(channel=0, U:I, "employee.ism")
```

Now add the following code after the FOREACH loop:

```dbl
; Give a 10% raise
emp_rec.Salary = emp_rec.Salary * 1.10
Select.GetEnum().Current = emp_rec
```

Now if you compile and run the program, you should see the salaries of all the employees in the sales department increase by 10%. If you keep running it, they will just keep going up! You can also use the `Select` class to delete records. This one is the easiest of all. Whatever your `Select` criteria is, you just have to call `selectObj.Delete()` and it will delete all the records that match your criteria. Let's write a new program to delete all the employees from the "CORP" department.

```dbl
import Synergex.SynergyDE.Select
main
record emp_rec
    EmployeeID,   a5
    Name,         a30
    Department,   a20
    Salary,       d9.2
endrecord
record
        channel, i4      ; Channel for file access
        fromObj, @From   ; From object for Select
        whereObj, @Where ; Where object for Select
        selectObj, @Select
endrecord
proc
    

    ; Open the employee file
    open(channel=0, U:I, "employee.ism")

    ; Create a From object for the employee file
    fromObj = new From(channel, emp_rec)

    ; Define a Where clause (e.g., selecting employees from a specific department)
    whereObj = (Where)emp_rec.Department.eq."CORP"

    ; Create a Select object with the From and Where objects
    ; and an OrderBy clause (e.g., ordering by Salary)
    selectObj = new Select(fromObj, whereObj)

    Console.WriteLine("Deleted " + %string(selectObj.Delete()) +
    &   " employees from the CORP department")
    close(channel)
endmain
```

When you run this program the first time, you should see the following output:

```console
Deleted 12 employees from the CORP department

%DBR-S-STPMSG, STOP
```

If you run it again, it will delete 0 employees, because there are no more employees in the CORP department. The update and delete operations we've just shown would look like this in SQL:

```sql
UPDATE employee SET Salary = Salary * 1.10 WHERE Department = 'Sales'
DELETE FROM employee WHERE Department = 'CORP'
```

There are some important things to keep in mind when using `Select` over a channel opened for update. First, you need to be aware that each record you read is going to be locked until you move to the next one or finish the loop. If you don't intend to update records in the loop, you should use the `Select` class over a channel opened for input only. Next you need to know how to handle locked records, because there could be someone else with a lock on a record you're trying to read. 

## Events
To handle locked records, you can implement a class that derives from the `Synergex.SynergyDE.Select.Event` class. This class has just one member, `onLock`, which is called when a locked record is encountered by your `Select` object. Let's take a look at a basic implementation of this class:
    
```dbl
; Define a class that extends the Event class for handling locks
class LockHandler extends Event

    ; Override the onLock method to handle the lock event
    public override method onLock, boolean
        inout lock,   n
        inout wait,   n
        in rec,       a
        in rfa,       a
    proc
        ; set wait to 1 to retry the locked operation
        wait = 1
        ; Return true to retry the locked operation
        ; or false to throw an exception
        mreturn true  
    endmethod
endclass
```

This class is pretty simple, but you can do a lot with those four arguments. You can use the `lock` and `wait` parameters to write out what you want the `Select` class to do on retry. You can set `wait` to 1 and then return true if you want to retry with a wait similar to `WAIT: 1` in a READS statement. Since you get the contents of the record in `rec`, you can use that to decide on your course of action. You can use the `rfa` argument to get the RFA of the record that was locked. You can either store this RFA somewhere to try again later or offer a list of exceptions to the user, depending on what makes sense in your situation. If you want to skip the locked record, you can set `lock` to `Q_NO_LOCK` or `Q_NO_DELETE`, depending on whether this is a read or a delete operation. You can register your event handler with the `Select` object by calling `selectObj.RegisterEvent(new LockHandler())` prior to starting your `Delete()` or FOREACH loop.

## N + 1 problem
The N + 1 query problem is a common performance issue in database operations that is particularly evident in scenarios where a loop over a set of records triggers additional queries for each record. This problem is especially pertinent in an environment using xfServer.

The N + 1 query problem occurs when an application makes one query to retrieve N records from a primary table (the "driving table") and then iterates over these records, making an additional query for each one to retrieve related data from another table. This results in a total of N + 1 queries (1 for the initial fetch and N for each record).

Consider two related tables: `Employees` (the driving table) and `Departments`. A typical operation might involve fetching all employees and then, for each employee, fetching data from the `Departments` table to get department details.

An initial `READS` operation retrieves each employee record from the `Employees` table. Inside a loop over these employee records, a READ operation is used to fetch the corresponding department information for each employee from the `Departments` table. Take a look at this diagram showing the flow of operations:

```svgbob
+----------------+           +----------------+
|                |  READS    |                |
|  Employees     | --------> | Employee #1    |
|  (Driving      |           |                |
|   Table)       |           +----------------+
+----------------+                 |
    |    |                         | READ
    |    |                         V
    |    +----------------->  +----------------+
    |                         |                |
    |                         | Department     |
    |                         | (for Employee  |
    |                         |  #1)           |
    |                         |                |
    |                         +----------------+
    |
    |    +----------------->  +----------------+
    |                         |                |
    |                         | Employee #2    |
    |                         |                |
    |                         +----------------+
    |                                |
    |                                | READ
    |                                V
    +----------------->  +----------------+
                         |                |
                         | Department     |
                         | (for Employee  |
                         |  #2)           |
                         |                |
                         +----------------+
    
    ... (and so on for each employee) ...

    +----------------->  +----------------+
                         |                |
                         | Employee #N    |
                         |                |
                         +----------------+
                                |
                                | READ
                                V
                         +----------------+
                         |                |
                         | Department     |
                         | (for Employee  |
                         |  #N)           |
                         |                |
                         +----------------+

```

The approach described above results in one query for the driving table plus one additional query for each record in the driving table. This means if there are 100 employees, the system performs 1 (initial READS) + 100 (individual READs) = 101 database queries. Such a high number of queries can significantly degrade performance, particularly in scenarios with large datasets or over networked database systems like xfServer. Let's take a look at the solution to this problem.

### Addressing the problem with Select Join

- **Efficiency of Joins**: The `Select` class with `Join` functionality in DBL offers a more efficient way to handle related data from multiple tables. Instead of multiple queries, a single `Select` statement with `Join` can retrieve all the necessary data in one go.
- **Reduced queries**: This method significantly reduces the number of queries sent to the database, alleviating the performance issues associated with the N + 1 query problem.
- **Optimized data retrieval**: `Select` with Join optimizes data retrieval, making it particularly beneficial for applications that require data from multiple related tables.

To run this next example, you'll need to grab the `department.txt` and `department.xdl` files from the `data` directory in the companion repository. These are the same as the `employee.txt` and `employee.xdl` files we used earlier, but for the departments table. We're going to use these to create a second ISAM file. Just like before, make sure you're in a dbl command prompt, and then we're going to run the following command:

```console
fconvert -i1 departments.txt -oi department -d department.xdl
```

Now let's write a program to read both of these files at the same time. Create a new file named `join.dbl` in your project directory and add the following code:

```dbl
import Synergex.SynergyDE.Select

main
record employee
    EmployeeID,   a5
    Name,         a30
    Department,   a20
    Salary,       d9.2
endrecord

record department
    DepartmentID, a20
    Description, a2000
endrecord

record
    channelEmp, i4        ; Channel for employee file
    channelDept, i4       ; Channel for department file
    joinSelectObj, @JoinSelect
    rowObj, @Rows
endrecord

proc
    ; Open employee and department files
    open(channelEmp, I:I, "DAT:employees.ism")
    open(channelDept, I:I, "DAT:departments.ism")

    ; Create From objects for both files
    fromEmp = new From(channelEmp, employee)
    fromDept = new From(channelDept, department)

    ; Create a Select object with an Inner Join
    joinSelectObj = new Select(fromEmp.InnerJoin(fromDept, 
    &   (On)(employee.Department == department.DepartmentID))).Join()

    ; Iterate over the joined records
    foreach rowObj in joinSelectObj
    begin
        rowObj.fill(employee)   ; Fill employee record
        rowObj.fill(department) ; Fill department record

        ; Process the joined records (e.g., display details)
        Console.WriteLine( "ID: " + employee.EmployeeID +
        & ", Name: " + employee.Name +
        & ", Department: " + department.DepartmentID +
        & ", Department Description: " + %atrim(department.Description))
    end

    ; Close the file channels
    close channelEmp
    close channelDept
endmain
```

The condition (`employee.Department == department.DepartmentID`) dictates how the rows from the two tables are matched. It essentially says, "Link each employee to their respective department by matching the department ID in the employee record with the department ID in the department record." Only those employee records that have a corresponding department entry will be included in the result set. When using Select Join, the record that is being matched must use a key. In this case the `department.xdl` file specifies `DepartmentID` as a key. If you were writing this query in SQL, it would look something like this:

```sql
SELECT *
FROM Employees
INNER JOIN Departments
ON Employees.Department = Departments.DepartmentID;
```

The Select class offers two types of joins: `InnerJoin` and `LeftJoin`. Let's take a look at the differences between these two types of joins.

### InnerJoin

- **Definition**: An `InnerJoin` combines rows from different files where there are matching values in both files.
- **Behavior**: If a row in the first file does not have a corresponding match in the second file, it is excluded from the result set.
- **Use case**: Use `InnerJoin` when you only want to retrieve records that have matching data in both files.
- **SQL example**: `SELECT * FROM table1 INNER JOIN table2 ON table1.id = table2.id;`
- **Result**: Only returns rows where there is a match in both `table1` and `table2`.

### LeftJoin (Left outer join)

- **Definition**: A `LeftJoin` returns all rows from the left (first) file and the matched rows from the right (second) table. If there is no match, `rowObj.Fill(table2)` returns `false`.
- **Behavior**: Includes all rows from the left file, regardless of whether they have matches in the right file.
- **Use case**: Use `LeftJoin` when you want all records from the left file and only the matched records from the right file. For unmatched entries in the left file, the `rowObj.Fill(table2)` call will return `false`.

### Key differences

1. **Inclusion of unmatched rows**: `InnerJoin` only includes rows with matches in both files. `LeftJoin` includes all rows from the left file, regardless of whether they have matches in the right file.

2. **Result set size**: `InnerJoin` can result in a smaller result set if many rows don’t have matches across the files. `LeftJoin` ensures that all rows from the left file are present in the result.

3. **Use in data analysis**: `LeftJoin` is particularly useful in data analysis and reporting where you need to maintain all records from one dataset while including related records from another dataset when available.

By understanding the differences between `InnerJoin` and `LeftJoin`, you can more effectively retrieve the necessary data based on the relationships and matching criteria between different ISAM files.
