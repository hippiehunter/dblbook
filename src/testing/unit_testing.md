# Unit Testing
DBL developers have two primary options for unit testing: the Synergex Test Framework for Traditional DBL and MSTest for .NET. The Synergex Test Framework attempts to mimic the syntax of MSTest as closely as possible. Both frameworks use attributes to mark classes and methods as tests, and both use the `Assert` class to verify test results. The main visible difference is that MSTest uses the `Microsoft.VisualStudio.TestTools.UnitTesting` namespace, and Traditional DBL uses the `Synergex.TestFramework` namespace. We're going to go over the tools you have available to you in both frameworks, and then we'll walk through creating and running some tests.

### Attributes

**TestClass**: This attribute is used to denote a class that contains unit test methods. In both DBL and MSTest, any class marked with this attribute is recognized as a container for test methods. This is foundational for organizing test scripts, where each test class typically focuses on a specific area of functionality.

**TestMethod**: Applied to methods within a TestClass, this attribute identifies individual tests. Each method marked as a TestMethod represents a distinct test case. This is where the actual testing logic resides, with each method typically testing a single functionality or scenario.

**TestInitialize**: This attribute marks a method that is run before each test in the test class. It's used for setup operations that need to be repeated before every test, such as initializing objects, setting default values, or configuring the test environment. This ensures that each test starts from a consistent state.

**TestCleanup**: The counterpart to TestInitialize, methods with this attribute are executed after each test in the class. This is where you clean up or dispose of resources used during the test, ensuring that one test's side effects don't affect another. It's essential for maintaining isolation between tests.

**ClassInitialize**: Used for setup that needs to run once before any of the tests in the class are executed, this attribute is typically for more resource-intensive operations. This could involve setting up database connections, preparing external resources, or establishing other once-per-class setup tasks.

**ClassCleanup**: The companion to ClassInitialize, this attribute is used for teardown operations that should occur after all tests in a class have been run. It's used to release resources that were set up in the ClassInitialize method, such as closing database connections or cleaning up files.

**Ignore**: This attribute is applied to tests that should be skipped. It's useful for temporarily disabling a test, perhaps because it's failing due to an external dependency or if it's not relevant under certain conditions. The Ignore attribute allows for flexibility in test execution without removing the test code.

### Asserts
Several static methods for verifying test results are available to you in `Synergex.TestFramework.Assert` and `Microsoft.VisualStudio.TestTools.UnitTesting.Assert`. We're going to go over the basics, but if you want to dig into the exact signatures, you can check [Synergex Test Framework documentation](https://www.synergex.com/docs/#vs/Assert.htm) and the [MSTest documentation](https://docs.microsoft.com/en-us/dotnet/api/microsoft.visualstudio.testtools.unittesting.assert?view=mstest-net-1.3.2).

**AreEqual** and **AreNotEqual**: These methods are used to verify that two values are equal or not equal, respectively. They're typically used to compare the actual result of a test to the expected result. The first parameter is the expected value, and the second parameter is the actual value. If the values are equal, the test passes. If they're not equal, the test fails and the test runner displays the expected and actual values along with a message if one is provided.

**AreSame** and **AreNotSame**: These methods are used to verify that two objects are the same or not the same, respectively. They're typically used to compare the actual result of a test to the expected result. The first parameter is the expected object, and the second parameter is the actual object. This comparison is much less common, and `AreEqual` is usually preferred to `AreSame`. If the objects are the same instance, the test passes. If they're not exactly the same instance, the test fails and the test runner displays the expected and actual objects along with a message if one is provided.

**Fail**: This method is used to force a test to fail. It's typically used to indicate that you've reached a code branch that should never be reached. 

**Inconclusive**: This method is used to indicate that a test is inconclusive. It's typically used to indicate that a test is not applicable under certain conditions.

**IsFalse** and **IsTrue**: These methods are used to verify that a condition (typically Boolean) is false or true, respectively. The first parameter is the condition to test, and the second parameter is a message to display if the test fails.

**IsNull** and **IsNotNull**: These methods are used to verify that an object is null or not null, respectively. The first parameter is the object to test, and the second parameter is a message to display if the test fails.

## Traditional DBL
initial project setup

```console
dotnet new synTradUnitTestProj -n MyUnitTestProject
```

running tests
```console
cd 
vstest.console.exe MyUnitTestProject.dbr
```
## .NET
initial project setup

```console
dotnet new synNETUnitTest -n MyUnitTestProject
```

```dbl
import System
import Microsoft.VisualStudio.TestTools.UnitTesting

namespace MyUnitTestProject
	{TestClass}
	public class Test1
		{TestMethod}
		public method TestMethod1, void
		proc
            Assert.AreEqual(1, 1)
            Assert.AreNotEqual(1, 2)
            if(1 != 1)
                Assert.Fail("This should never happen")

		endmethod
	endclass
endnamespace
```

running tests

```console
dotnet test

Starting test execution, please wait...
A total of 1 test files matched the specified pattern.

Passed!  - Failed:     0, Passed:     1, Skipped:     0, Total:     1, Duration: 49 ms - MyUnitTestProject.dll (net6.0)
```