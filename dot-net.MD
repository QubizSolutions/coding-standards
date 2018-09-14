# Coding standards
This document intends to bring out the key points to follow by the development team. Everyone should adhere to these as we do not want to have multiple formats of the code spread around the entire solution/teams. There is no priority set on the below items. All are to be considered equal. Keep it clean and simple!
  * [Global coding standards](#global-coding-standards)
  * [Layer coding standards](#layer-coding-standards)
  * [Repository coding standards](#repository-coding-standards)
  * [Unit and integration testing guidelines](#unit-and-integration-testing-guidelines)


## Global coding standards(these do not apply to Javascript)
  * Pascal case for class and method names
  * Camel case for local variables, parameters and private variables
  * No code comments - Following agile practices the names of your interfaces / classes / methods / variables should communicate their purpose and intended usage
  * Naming should come from the *ubiquitous language*! If the business people are naming an entity Employee you shoud have in code as such rather than something different. Look below at an example:
    ``` c#
    public class Foo
    {
        void Bar1()
        {
            // class name is User but variable name is employee => not consistent
            User employee = new User();
        }
        
        void Bar2()
        {
            // class name is Employee and variable name is also employee => consistent
            Employee employee = new Employee();
        }
    }
    ```

      * this helps also in renaming
  * Dependency injected variables should be declared as ***private readonly*** and the constructor parameter should have the same name as the variable. Use **this** for variable assignment inside the constructor
  * When checking if a **Nullable< T >** has a value assigned, use **Nullable< T >.HasValue**
  * Use the **AutoMapper** extensions,  **.DeepCopyTo< T >** and **Map(source, dest)**, whenever possible to streamline the flow. This implies having the same property names on both objects which in fact is a must where possible

## Layer coding standards(this applies to each layer of the application)
  * Each layer should **accept** and **return** its own ***data types***


## Repository coding standards
  * Repository method naming conventions are as follows(try and follow them as much as possible, exceptions might occur):
  
    * **List**: When listing a collection of object. Append the filter name if applied(Ex: ***ListByAdmin***, ***ListByType***, ***ListByAdminAndType***)

    ``` c#
    public interface IFoo
    {
        Foo[] List();
        Foo[] ListByAdmin(Guid adminID);
        Foo[] ListByType(FooType type);
        Foo[] ListByAdminAndType(Guid adminID, FooType type);
    }
    ```

    * **Get**: When listing a single object. Append the filter name if applied(Ex: ***GetByID***, ***GetByAdmin***, ***GetByType***, ***GetByAdminAndType***)

    ``` c#
    public interface IFoo
    {
        Foo GetByID(Guid id);
        Foo GetByAdmin(Guid adminID);
        Foo GetByType(FooType type);
        Foo GetByAdminAndType(Guid adminID, FooType type);
    }
    ```

    * **Delete**: When deleting object(s). Append the filter name if applied(Ex: ***DeleteByID***, ***DeleteByAdmin***, ***DeleteByType***, ***DeleteByAdminAndType***)

    ``` c#
    public interface IFoo
    {
        void DeleteByID(Guid id);
        void DeleteByAdmin(Guid adminID);
        void DeleteByType(FooType type);
        void DeleteByAdminAndType(Guid adminID, FooType type);
    }
    ```

## Unit and integration testing guidelines
Examples contain references to [Moq](https://github.com/moq/moq4) which is the most popular and friendly mocking framework for .NET

  * Individual tests should follow the naming convention of ***TestMethod_WhenScenario_ThenExpectedResult*** and be as descriptive as possible

    ``` c#
    [TestMethod]
    public void Create_WhenDuplicateName_ThenReturnsError()
    {
        // test method body
    }
    ```
  
  * Never use **DateTime.Now** in a test, always declare your own ***DateTime*** using the year, month, day or other parts if needed

    ``` c#
    [TestMethod]
    public void Some_Test_Method()
    {
        // lets say the DateTime.Now is equal to new DateTime(2010, 01, 01)
        DateTime now = DateTime.Now;
        
        DateTime birthDate = new DateTime(1980, 01, 01);
        
        int age = bar.Age(birthDate, now);
        
        // If we run this today, we get proper results
        // If we run this after a period of time, lets say a year,
        // it's gonna fail misserably since is going to see that the age went
        // up a notch which is perfectly correct, 'now' is going to be new DateTime(2011, 01, 01)
    }
    ```
  
  * Keep unit tests small, test **ONLY ONE** specific scenario. Try not to test the same thing multiple times. Unit tests should be quick to write and easy to maintain
  * Mocking should be used where possible so that only the specific code being tested needs to be executed
  * Mocked objects should reflect as much as possible what the real object would return
  * Every mocked object should be prefixed with **Mock** and declared as a ***private field***
  * Use **MockBehavior.Strict** whenever possible and verify them all at the end of each test
  * Always use the TestInitialize and TestCleanup methods to instantiate mocked objects and the tested object and respectively verify all mocks

    ``` c#
    [TestClass]
    public class FooTest
    {
        private Mock<IBar> barMock;
        
        private Foo foo;
    
        [TestInitialize]
        public void TestInitialize()
        {
            barMock = new Mock<IBar>(MockBehavior.Strict);
            
            foo = new Foo(barMock.Object);
        }
        
        [TestCleanup]
        public void TestCleanup()
        {
            barMock.VerifyAll();
        }
    }
    ```
