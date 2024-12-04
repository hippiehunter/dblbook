# Inheritance and Polymorphism
One of the earliest object-oriented programming concepts taught is that of inheritance and polymorphism. At its core, this concept teaches that a derived class can inherit properties and behaviors from a base class and can override or extend these behaviors. This foundational concept is pivotal in building scalable and maintainable software. A common example is different shapes (`Circle`, `Rectangle`, etc.) all inheriting from a base `Shape` class. But we're going to try something just the slightest bit less contrived that you might actually be able to use. As developers delve deeper into OOP and software design patterns, the utility of polymorphism begins to shine in more subtle, advanced, and powerful ways. One of these is the delegation pattern, which can be thought of as an evolution or specialized use of polymorphism.

### The delegation pattern via base class

In the delegation pattern, instead of performing a task itself, an object delegates the task to a helper object. This can be achieved elegantly using polymorphism by defining an [interface](../beyond_types/interfaces.md) or a base class and then creating delegate classes that implement the specific behavior.

Consider an application that needs to notify users of certain events. Multiple notification methods exist: email, SMS, push notification, etc. In the future, there could be additional notification methods, and you don't really want to have a super method that handles every possible kind of notification. So you do this:
```dbl
import System.Collections

namespace DelegatePattern

	abstract class Notifier
        public abstract method Notify, void
            user, @string
            message, @string
        proc
        endmethod

    endclass

    class EmailNotifier extends Notifier
        public override method Notify, void
            user, @string
            message, @string
        proc
            ;; Logic to send an email
        endmethod
    endclass

    class SMSNotifier extends Notifier
        public override method Notify, void
            user, @string
            message, @string
        proc

            ;; Logic to send an SMS
        endmethod
    endclass

    class PushNotifier extends Notifier
        public override method Notify, void
            user, @string
            message, @string
        proc

            ;; Logic to send a push notification
        endmethod
    endclass

    class EventManager
        private notifierInstances, @ArrayList

        public method EventManager
        proc
            notifierInstances = new ArrayList()
        endmethod

        public method AddHandler, void
            aNotifier, @Notifier
        proc
            notifierInstances.Add(aNotifier)
        endmethod

        public method AlertUser, void
            user, @string
            message, @string
        proc
            foreach data instance in notifierInstances as @Notifier
                instance.Notify(user, message)

        endmethod
    endclass

endnamespace
```

In this design, the `EventManager` doesn't need to know how the user is notified—it just knows it has a method to do so. We've abstracted the notification mechanism and encapsulated it within specific "delegate" classes (EmailNotifier, SMSNotifier, PushNotifier). After creating an instance of `EventManager`, the specific notifier is added to the internal array list. This provides a high degree of flexibility.

1.  **Flexibility**: Easily add new notification methods without changing the `EventManager` code.
2.  **Single responsibility**: Each class has a distinct responsibility. The notifiers just notify, and the `EventManager` manages events.
3.  **Decoupling**: The `EventManager` is decoupled from the specifics of notification, making the system more modular and easier to maintain.

In this example, the power of polymorphism is not just in categorizing similar objects but in defining behaviors that can be swapped out or combined as needed. It's a step toward more advanced design patterns and showcases the depth and versatility of OOP principles in real-world scenarios.

### Overriding virtual functions in concrete base classes

In many scenarios, the base class serves as a default implementation that's appropriate for most cases. However, certain specialized situations may require variations or enhancements to this default behavior. This is where the power of overriding in concrete (non-abstract) base classes shines.

For instance, consider a WebServiceClient class that already provides a concrete implementation of fetching data. This is a little bit contrived, but if you suspend your disbelief for a moment and imagine you want to bake in some extra HTTP headers, you might implement that by extending the base client, overriding the part that does the HTTP request, add in your headers, and then let the base type implementation finish the job. It would look something like this example code:

```dbl
namespace WrappingBaseFunctionality

    public class WebServiceClient

        public method ExampleSize, int
        proc
            mreturn FetchData(^null).Length
        endmethod

        protected virtual method FetchData, @string
            requestHeaders, [#]string
            record
                status, int
                errtxt, string
                responseHeaders, [#]string
                responseBody, string
            endrecord
        proc
            status = %http_get("http://example.com/",5,responseBody,errtxt,requestHeaders,responseHeaders,,,,,,,"1.1")
            mreturn responseBody
        endmethod

	endclass

    public class FancyWebServiceClient extends WebServiceClient

        protected override method FetchData, @string
            requestHeaders, [#]string
            record
                status, int
                errtxt, string
                localRequestHeaders, [#]string
                responseHeaders, [#]string
                responseBody, string
            endrecord
        proc
            localRequestHeaders = new string[requestHeaders != ^null ? requestHeaders.Length : 1]
            localRequestHeaders[localRequestHeaders.Length - 1] = "X-SOME-AUTH-HEADER: value"
            ;;now that we've added our new header, we can call the base class implementation
            mreturn parent.FetchData(localRequestHeaders)
        endmethod

    endclass

endnamespace
```