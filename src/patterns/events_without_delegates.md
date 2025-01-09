# Events Without Delegates
In Traditional DBL functions are not considered 'First Class' in that you cannot treat them as data. This means you dont have access to lambdas, events or delegates. We're going to cover an alternative pattern that is often called the Strategy pattern in the Gang of Four patterns book.

The basic structure involves:

* An abstract base class (or interface) that declares the virtual method (the "strategy")
* Concrete classes that inherit from the base and implement the virtual method
* A context class that uses the strategy

You've already been introduced to this concept when we talked about IO Hooks in the IO chapter, but this concept is generally useful so we'll dive right in to the details of when and how to use it in your own designs.

The first new use case is in implementing audit trails or logging systems. For example, you might have a financial transaction processing system where you need different audit requirements for different types of transactions. The base handler class could define standard audit points (BeforeTransaction, AfterTransaction), while specific implementations could handle different regulatory requirements - one implementation might log to a secure database for high-value transactions, another might just maintain in-memory logs for testing.

```dbl
public class TransactionHandlers
    public virtual method BeforeTransaction, void
        required in txnAmount, decimal
    proc
    endmethod
    
    public virtual method AfterTransaction, void
        required in txnAmount, decimal
        required in success, boolean
    proc
    endmethod
endclass

public class HighValueTransactionHandlers extends TransactionHandlers
    public override method BeforeTransaction, void
        required in txnAmount, decimal
    proc
        if (txnAmount > 10000)
            ;;log to secure audit database
        else
            parent.BeforeTransaction(txnAmount)
endclass
```

Another powerful application is in integration scenarios where you need to hook into existing business processes. Consider an order processing system that needs to integrate with various external services. The handler pattern lets you inject different behaviors for different integration points without cluttering the core order processing logic:

```dbl
public class OrderProcessingHandlers
    public virtual method BeforeOrderSave, void
        required in order, @Order
    proc
    endmethod
    
    public virtual method AfterOrderSave, void
        required in order, @Order
    proc
    endmethod
endclass

public class TruckRoutingIntegrationHandlers extends OrderProcessingHandlers
    public override method AfterOrderSave, void
        required in order, @Order
    proc
        ;;Send order truck routing api
        data routeClient = new RouteClient()
        routeClient.SendOrder(order)
endclass
```

What makes this pattern particularly valuable in enterprise systems is that it provides a structured way to handle cross-cutting concerns. You can have multiple handler implementations that address different aspects of your system - monitoring, security, integration, caching - all while keeping these concerns separate from your core business logic. This separation makes the code easier to maintain and test, as you can swap out handlers for testing purposes or gradually add new functionality without touching existing code.

It's worth noting that while this pattern adds some complexity through the additional class hierarchy, it *only* pays off in scenarios where you need to maintain multiple variations of behavior or when you're *certain* that you're building a system that needs to be customizable by other developers. The explicit nature of the handler methods also serves as documentation, making it clear where and when custom behavior can be injected into the process.

## .NET
When running on .NET, DBL gains access to delegates and events, which provide a more flexible and potentially cleaner way to handle these scenarios. Here's how the same pattern could be implemented using delegates:

```dbl
namespace ANamespace
    public class MyInterestingClass
        public delegate BeforeSomethingDelegate, void
        enddelegate
        
        public delegate AfterSomethingDelegate, void
        enddelegate
        
        private beforeHandler, @BeforeSomethingDelegate
        private afterHandler, @AfterSomethingDelegate
        
        public property BeforeHandler, @BeforeSomethingDelegate
            method get
            proc
                mreturn beforeHandler
            endmethod
            method set
            proc
                beforeHandler = value
            endmethod
        endproperty
        
        public DoSomething, void
        proc
            if(beforeHandler != ^null)
                beforeHandler()
            ;;do something
            if(afterHandler != ^null)
                afterHandler()
        endmethod
    endclass
endnamespace
```

The delegate approach offers several advantages:

1. Composition over inheritance - Instead of creating a class hierarchy, you can simply pass in the functions you want to execute. This is more flexible as a single class can handle multiple different behaviors without needing to create new handler classes.

2. Multiple handlers - With delegates, you can easily chain multiple handlers together. This is particularly useful in event-driven scenarios where multiple subsystems might need to respond to the same event.

```dbl
main
record
    theClass, @MyInterestingClass
proc
    theClass = new MyInterestingClass()
    theClass.BeforeHandler += LoggingHandler
    theClass.BeforeHandler += SecurityCheckHandler
endmain
```

3. Lambda expressions - When using .NET, you can use lambda expressions for simple handlers, reducing the ceremonial code needed:

```dbl
main
record
    theClass, @MyInterestingClass
proc
    theClass = new MyInterestingClass()
    theClass.BeforeHandler = lambda() { } 
endmain
```

However, the traditional handler pattern still has its place:

1. When you need a more structured approach with multiple related methods that share state. The class-based approach provides a cleaner way to organize related handlers and maintain shared state between them.

2. In scenarios where you want to enforce a specific interface or contract. The abstract handler class makes it explicit what methods need to be implemented and can include documentation about expected behavior.

3. When working with older codebases or teams more comfortable with traditional OOP patterns. The class-based approach might be more familiar and maintainable in these contexts.

A hybrid approach can often work well, using the traditional pattern for complex handlers with multiple related methods and shared state, while using delegates for simpler scenarios or when you need multiple handlers:

```dbl
public class OrderProcessor
    public delegate OrderValidationDelegate, void
        required in order, @Order
    enddelegate
    
    private validators, @OrderValidationDelegate
    private handlers, @OrderProcessingHandlers
    
    public OrderProcessor
        required in handlers, @OrderProcessingHandlers
    proc
        this.handlers = handlers
    endmethod
    
    public AddValidator, void
        required in validator, @OrderValidationDelegate
    proc
        validators += validator
    endmethod
    
    public ProcessOrder, void
        required in order, @Order
    proc
        if(validators != ^null)
            validators(order)
        handlers.BeforeOrderSave(order)
        ;;process order
        handlers.AfterOrderSave(order)
    endmethod
endclass
```

This hybrid approach lets you use the most appropriate tool for each specific need - delegates for simple validations that might need to be chained together, and the handler pattern for more complex processing steps that need to maintain state or have multiple related methods.