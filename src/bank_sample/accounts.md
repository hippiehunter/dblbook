# Accounts
As we start to model our super simple bank simulation we're going to start with the most basic building block, the account. We're going to start by defining a repository structure for a very simple account that has a balance, an account number and also a name so we can identify it. Go ahead and open up `Repository\repository.scm` and add the following structure definition:

```sdl
STRUCTURE Account DBL ISAM
    DESCRIPTION "Bank account information"
    FIELD AccountId TYPE INTEGER SIZE 4
    FIELD Name TYPE ALPHA SIZE 40
    FIELD Balance TYPE DECIMAL SIZE 28 PRECISION 2
END
```

The field sizes here are pretty arbitrary, later on in this chapter we'll walk through a more in depth analysis of how to determine the appropriate sizes for some example fields. 

The code we're going to write needs to handle the following operations:

1. **Management**: 
   - **Account Creation**: Users can create new bank accounts. Each account will have unique attributes like account number and balance.
   - **Account Details**: Users can view details of an account, including the account number and the current balance.

2. **Transactions**:
   - **Deposits**: Users can deposit money into any account. This involves increasing the account balance by the deposit amount.
   - **Withdrawals**: Users can withdraw money, provided they have sufficient balance. This decreases the account balance.
   - **Transfers**: Users can transfer money from one account to another. This involves withdrawing money from one account and depositing it into another.

3. **Reporting**:
   - **Balance Inquiry**: Users can check the balance of any account.
   - **Total Assets**: The application can calculate and display the total assets held across all accounts.

Most of these operations are going to be handled by the `Account` class. Lets go ahead and create a new class in `BankApp\Account.dbl`:

```dbl
namespace BankApp
    .include "Account" REPOSITORY, structure="AccountData", end
    class Account
        private innerData, AccountData
        
        ;; Constructor for Account
        public method Account
            accountId, int
            name, string
        proc
            innerData.AccountId = accountId
            innerData.Balance = 0.0
            innerData.Name = name
        endmethod

        ;; Method to deposit money
        public method Deposit, void
            amount, d.
        proc
            if (amount > 0)
            begin
                innerData.Balance += amount
            end
        endmethod

        ;; Method to withdraw money
        public method Withdraw, boolean
            amount, d.
        proc
            if (amount > 0 && innerData.Balance >= amount) then
            begin
                innerData.Balance -= amount
                mreturn true
            end
            else
                mreturn false
        endmethod

        public property Balance, d28.2
            method get
            proc
                mreturn innerData.Balance
            endmethod
        endproperty

        public property AccountId, int
            method get
            proc
                mreturn innerData.AccountId
            endmethod
        endproperty

    endclass
endnamespace
```

Now we've hidden the details of the account data structure behind the `Account` class and it's accessors. This is a good start but we're going to need to be able to manage the accounts. We are going to be using `ArrayList` as our primary data storage mechanism. With the account id's being the index into the ArrayList. This is a very simple way to store our data and is not appropriate for a real world application. In a later chapter we're going to revisit this to use a more appropriate data storage mechanism.

**Storing Account Objects**: Each account, represented as an instance of the `Account` class, is stored in an `ArrayList`. This allows us to dynamically add, remove, and access accounts as needed.

**Dynamic Resizing**: Unlike static arrays, `ArrayList` can dynamically resize itself. This is particularly useful for our bank application as the number of accounts can increase over time.

**Data Persistence**: In this version of the application, data is not persisted to disk. When the application is closed, all data is lost. 

Onwards to the implementation of the `Bank` class that will manage our accounts. Go ahead and create a new class in `BankApp\Bank.dbl`:

```dbl
import System.Collections

namespace BankApp
    class Bank
        private accounts, @ArrayList 
        private accountIdOffset, int
        
        ;; Constructor for BankApplication
        public method Bank
            accountIdOffset, int
        proc
            accounts = new ArrayList()
            this.accountIdOffset = accountIdOffset
        endmethod

        ;; Method to create a new account
        public method CreateAccount, @Account
            name, @string
        record
            newAccount, @Account
        proc
            newAccount = new Account(accounts.Count, name)
            accounts.Add(newAccount)
            mreturn newAccount
        endmethod

        ;; Method to find an account by account number
        private method FindAccount, @Account
            accountId, int
        record
            realId, int
        proc
            realId = accountId - accountIdOffset
            mreturn (@Account)accounts[realId]
        endmethod

        ;; Method to deposit money into an account
        public method Deposit, void
            accountId, int
            amount, d.
        record
            account, @Account
        proc
            account = FindAccount(accountId)
            if (account != ^null)
            begin
                account.Deposit(amount)
            end
        endmethod

        ;; Method to withdraw money from an account
        public method Withdraw, boolean
            accountId, int
            amount, d.
        record
            account, @Account
        proc
            account = FindAccount(accountId)
            if (account != ^null) then
                mreturn account.withdraw(amount)
            else
                mreturn false
        endmethod

        ;; Method to get account balance
        public method GetBalance, d28.2
            accountId, int
        record
            account, @Account
        proc
            account = FindAccount(accountId)
            if (account != ^null) then
            begin
                mreturn account.Balance
            end
            else
                mreturn 0.0
        endmethod

        public property TotalAssets, d28.2
            method get
            record
                total, d28.2
            proc
                total = 0.0
                foreach data anAccount in accounts as @Account
                begin
                    total += anAccount.Balance
                end
                mreturn total
            endmethod
        endproperty
    endclass
endnamespace
```


The `CreateAccount` method in your `Bank` class is a factory method. `CreateAccount` encapsulates the logic for creating new `Account` instances. This ensures that all accounts are created consistently and allows for centralized control over the account creation process. By providing a method to create new accounts for a bank and insert them into the accounts list, you simplify the client code. Users of your `Bank` class don't need to know the details of how accounts are managed; they just call `CreateAccount`.

In the future, if the process of account creation becomes more complex (e.g., adding validation, or additional steps in account setup), these changes can be made in one place without affecting the client code. This makes your code more maintainable and scalable.

Let's put it all together in a simple console application. Go ahead and open up `BankApp\Program.dbl` and add the following code:

```dbl
import System
import BankApp

main
record
    bank, @Bank
proc
    bank = new Bank(1000)
    bank.CreateAccount("fred");
    bank.Deposit(1000, 500.0);
    Console.WriteLine("Balance: " + %string(bank.GetBalance(1000)))
    bank.Withdraw(1000, 200.0)
    Console.WriteLine("Balance after withdrawal: " + %string(bank.GetBalance(1000)))
endmain
```

Now we can run our application and see the results:

```console
dotnet run
Balance: 500.00
Balance after withdrawal: 300.00
```

Ok, the building blocks for accounts are in place. In the next section we're going to look at how to handle transfers between accounts.