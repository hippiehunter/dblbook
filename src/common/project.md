# Project
The goal of this assignment is to apply basic programming concepts like variables, control flow, and routines within the context of a contrived business scenario. We'll get to collections and complex structures in later chapters and these assignments will get more interesting. For now though, your task will be to simulate a single customer basic banking system, where you'll be dealing with operations like creating a client and depositing and withdrawing money. You will want to start from the .NET Console application template in Visual Studio or `synergy_console` at the dotnet cli.

**Part 1: Client management**

1.  **Client creation routine**: Write a routine called `CreateClient` that accepts client details (client ID and client name) as arguments and assigns these details to fields in a common, along with an initial balance of 0.

2.  **Display client routine**: Write a routine called `DisplayClient` that takes the stored details from an external common and uses `Console.WriteLine` to display these details in a meaningful way.

**Part 2: Bank operations**

1.  **Deposit routine**: Write a routine called `DepositMoney` that accepts a client's balance and an amount to be deposited. This routine should return the new balance. Use control statements to ensure the deposit amount is a positive number; if not, display an appropriate message and return the original balance. This routine should operate on its parameters only and not on the external common used in part 1.

2.  **Withdrawal routine**: Write a routine called `WithdrawMoney` that accepts a client's balance and an amount to be withdrawn. Use control statements to check if the client's balance is sufficient for the withdrawal. If so, return the new balance; if not, display an appropriate message and return the original balance. This routine should operate on its parameters only and not on the external common used in part 1.

**Part 3: Transaction menu**

Create a simple text-based menu routine called `TransactionMenu` that allows the user to choose an operation (create client, display client, deposit, or withdraw) based on a number. Though it's only available when targeting .NET, you can use `Console.ReadLine` to get the user's choice. `Console.ReadLine()` will return a string of input from the user when they press the return key. You will need to declare appropriate variables for use with the available routines you've written. Use a USING statement to handle the user's choice. For instance:

-   If the user chooses "create client", call the `CreateClient` routine and store the returned values into a record.
-   If the user chooses "display client", call the `DisplayClient` routine with the stored client values into a record.
-   If the user chooses "deposit", call the `DepositMoney` routine and update the stored client balance into a record.
-   If the user chooses "withdraw", call the `WithdrawMoney` routine and update the stored client balance into a record.