# Transfers
Rather than implement the transfer logic in the `Account` class, we're going to create a `Transaction` abstract class that will be the base class for `Transfer` and any future transaction types we might want to add. Let's take a look at a diagram of our system with the `Transaction` bits added:

```svgbob
+---------------------+     +----------------------+
|        Bank         |<>---|       Account        |
|---------------------|     |----------------------|
| "-accounts:List"    |     | "-accountId:int"     |
|                     |     | "-balance:d28.2"     |
| "#CreateAccount()"  |     | "+Deposit()"         |
| "+Deposit()"        |     | "+Withdraw()"        |
| "+Withdraw()"       |     +----------+-----------+
| "+Balance:d28.2"    |                |
| "+Transfer()"       |                |
+------------+--------+                |
             |                         |
             |      +------------------+------------+
             |      |  Transaction                  |
             +------|-------------------------------|
                    | "-accountNumber:int"          |
                    | "-TransactionType():String"   |
                    | "+Execute():boolean"          |
                    +-------------------------------+
                                  ^
                                  |
                    +-------------+----------------+
                    | Transfer                     |
                    |------------------------------|
                    | "-amount:double"             |
                    | "-toAccountNumber:int"       |
                    | "+Execute():boolean"         |
                    +------------------------------+

```

For our very simple case, this is clearly more work than we need to do, but let's talk about why this sort of design decision matters in larger applications.

### Single responsibility principle (SRP)
The *single responsibility principle* states that a class should have only one reason to change. This principle advocates for separating different functionalities into distinct classes. We don't have to mindlessly follow this principle but it's a good idea to keep it in mind when designing your classes. Let's take a look at some of the benefits of following this principle:

- The `Account` class focuses solely on individual account properties and basic operations like deposit and withdraw.
- The `Bank` class manages collections of accounts and higher-level operations involving multiple accounts.
- The `Transaction` class (and its subclasses) deal exclusively with transaction-specific logic and behaviors.

A modular system promotes reusability of components. By encapsulating the transaction logic within its own class hierarchy, these components can be reused in different contexts without duplication of code.

- You can easily extend the system to include different types of transactions (like direct debits, standing orders, or interest applications) without altering the existing `Account` or `Bank` classes.
- This modular design makes maintenance and extension of the system more manageable.

Separating concerns enhances maintainability and readability. Each class and method does one thing and does it well, making the code easier to understand and maintain.

- The separation of transaction logic into its own class hierarchy makes the system easier to navigate and understand. Changes in one part of the system are less likely to cause unintended side effects in other parts.

A system designed with separate classes for different functionalities is more flexible and easier to scale.

- Introducing new transaction types or changing existing transaction logic can be done without impacting the core account management functionalities.
- The system can evolve to handle more complex scenarios, like multi-step transactions or conditional transactions, without significant restructuring.

Okay, enough loosely applicable theory—let's actually code it. To incorporate the concept of inheritance and further demonstrate object-oriented principles in this bank application, we can design a hierarchy of transaction types. In this revised approach, we'll create a base class named `Transaction` and then derive specific transaction types like `Transfer` from this base class.

### Transaction class hierarchy

#### Abstract transaction class

Having one class per file is generally a good idea. Let's create a new file named `Transaction.dbl`, add it to `BankApp.synproj` as we've done for the other source files, and add the following code:

```dbl
namespace BankApp

    ;; Define the base Transaction class
    abstract class Transaction 
        protected accountNumber, int
        protected amount, d28.2

        public method Transaction
            accountNumber, int 
            amount, d.
        proc
            this.accountNumber = accountNumber
            this.amount = amount
        endmethod

        ;; Abstract method to execute the transaction
        public abstract method Execute, boolean 
            bankApp, @Bank
        proc
        endmethod

        ;; Getters for transaction details
        public property AccountNumber, int
            method get
            proc
                mreturn accountNumber
            endmethod
        endproperty

        public property Amount, d28.2
            method get
            proc
                mreturn amount
            endmethod
        endproperty

        public abstract property TransactionType, String
            method get
            endmethod
        endproperty
    endclass
endnamespace
```

#### Transfer class
Let's create a new file named `Transfer.dbl`, add it to `BankApp.synproj`, and add the following code:

```dbl
;; Define the TransferTransaction class extending Transaction
namespace BankApp
    class Transfer extends Transaction 
        private toAccountNumber, int

        public method Transfer
            fromAccountNumber,int 
            toAccountNumber, int
            amount, d.
        endparams
        parent(fromAccountNumber, amount)
        proc
            this.toAccountNumber = toAccountNumber
        endmethod

        public override method Execute, boolean 
            bankApp, @Bank
        proc
            ;; Withdraw from the source account
            if (bankApp.Withdraw(accountNumber, amount)) 
            begin
                ;; Deposit to the destination account
                bankApp.Deposit(toAccountNumber, amount)
                mreturn true
            end
            mreturn false
        endmethod

        public property ToAccountNumber, int
            method get
            proc
                mreturn toAccountNumber
            endmethod
        endproperty

        public override property TransactionType, String
            method get
            proc
                mreturn "TRANSFER"
            endmethod
        endproperty
    endclass
endnamespace
```

Now we have our `Transfer` class that extends the `Transaction` class. We can now update `main` to use the new `Transfer` class:

```dbl
import System
import BankApp

main
record
    bank, @Bank
proc
    bank = new Bank(1000)
    bank.CreateAccount("fred");
    bank.CreateAccount("fred2");
    bank.Deposit(1000, 500.0);
    Console.WriteLine("Balance: " + %string(bank.GetBalance(1000)))
    bank.Withdraw(1000, 200.0)
    Console.WriteLine("Balance after withdrawal: " + %string(bank.GetBalance(1000)))

    new Transfer(1000, 1001, 100.0).Execute(bank)
    Console.WriteLine("Balance after transfer: " + %string(bank.GetBalance(1000)))
endmain
```

This should print out the expected output:

```
Balance: 500.00
Balance after withdrawal: 300.00
Balance after transfer: 200.00
```

If you get an error about `Transfer` or `Transaction` not being found, make sure you've added the new files to `BankApp.synproj`.

### Exercise
Try extending the `Account` class: Add a new field to the `Account` structure, such as `DateOpened`, and update the `Account` class to handle this new field. The companion repository contains a solution to this exercise.<!--Add link?-->

Implement and use a new transaction type: Create a new transaction type, such as `InterestApplication`, which inherits from the `Transaction` class. This transaction should apply a fixed interest rate to the account balance. 

Once you've completed the exercises, it's time to move on to testing.