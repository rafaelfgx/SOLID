# SOLID

SOLID is an acronym created by Michael Feathers from the principles of object-oriented programming identified by Robert C. Martin (Uncle Bob).

These principles aim to make the code more readable, easy to maintain, extensible, reusable and without repetition.

## Single Responsibility Principle

The Single Responsibility Principle says that a class must have only one reason to change and only one responsibility.

### Bad

```cs
public sealed class Customer
{
    public int Id { get; set; }

    public string Name { get; set; }

    public bool Active { get; set; }

    public void Activate()
    {
        Active = true;
    }

    public void Inactivate()
    {
        Active = false;
    }

    public void Insert()
    {
        /// Implementation for ADDING to DATABASE
    }

    public void Delete()
    {
        /// Implementation for DELETING to DATABASE
    }
}
```

**The code is incorrect because it has two responsibilities, business rules and database persistence.**

### Good

```cs
public sealed class Customer
{
    public int Id { get; set; }

    public string Name { get; set; }

    public bool Active { get; set; }

    public void Activate()
    {
        Active = true;
    }

    public void Inactivate()
    {
        Active = false;
    }
}
```

```cs
public sealed class CustomerRepository
{
    public void Insert(Customer customer)
    {
        /// Implementation for ADDING to DATABASE
    }

    public void Delete(Customer customer)
    {
        /// Implementation for DELETING to DATABASE
    }
}
```

**The code is correct because the responsibilities have been split, each class has only one reason to change.**

## Open Closed Principle

The Open Closed Principle says the code must be open for extension and closed for modification.

### Bad

```cs
public enum PaymentMethod
{
    Cash = 1,
    CreditCard = 2,
    DebitCard = 3
}
```

```cs
public class PaymentService
{
    public void Pay(PaymentMethod paymentMethod)
    {
        /// Implementation

        if (paymentMethod == PaymentMethod.Cash)
        {
            /// Implementation
        }
        else if (paymentMethod == PaymentMethod.CreditCard)
        {
            /// Implementation
        }
        else if (paymentMethod == PaymentMethod.DebitCard)
        {
            /// Implementation
        }

        /// Implementation
    }
}
```

**The code is incorrect because it is open for modification. If a new payment method is added, the class has to be changed.**

### Good

```cs
public interface IPaymentMethod
{
    void Pay();
}
```

```cs
public sealed class Cash : IPaymentMethod
{
    public void Pay()
    {
        /// Implementation
    }
}
```

```cs
public sealed class CreditCard : IPaymentMethod
{
    public void Pay()
    {
        /// Implementation
    }
}
```

```cs
public sealed class DebitCard : IPaymentMethod
{
    public void Pay()
    {
        /// Implementation
    }
}
```

```cs
public class PaymentService
{
    private readonly IPaymentMethod _paymentMethod;

    public PaymentService(IPaymentMethod paymentMethod)
    {
        _paymentMethod = paymentMethod;
    }

    public void Pay()
    {
        /// Implementation

        _paymentMethod.Pay();

        /// Implementation
    }
}
```

**The code is correct because if a new payment method is added the class is not modified.**

## Liskov Substitution Principle

The Liskov Substitution Principle says that derived classes must be substitutable for their base classes.

### Bad

```cs
public class Cat
{
    public virtual string GetName()
    {
        return nameof(Cat);
    }

    public void Move()
    {
        /// Implementation
    }

    public void Eat()
    {
        /// Implementation
    }
}
```

```cs
public class Dog : Cat
{
    public override string GetName()
    {
        return nameof(Dog);
    }
}
```

```cs
public static class Program
{
    public static void Main()
    {
        Cat cat = new Dog();
        cat.GetName();
    }
}
```

**The code is incorrect because the Dog class is inheriting from the Cat class only because it has similar behaviors. Executing "cat.GetName()" will display "Dog".**

### Good

```cs
public abstract class Animal
{
    public abstract string GetName();

    public virtual void Move()
    {
        /// Implementation
    }

    public void Eat()
    {
        /// Implementation
    }
}
```

```cs
public sealed class Cat : Animal
{
    public override string GetName()
    {
        return nameof(Cat);
    }
}
```

```cs
public sealed class Dog : Animal
{
    public override string GetName()
    {
        return nameof(Dog);
    }

    public override void Move()
    {
        /// Implementation
    }
}
```

```cs
public static class Program
{
    public static void Main()
    {
        var animals = new List<Animal>
        {
            new Cat(),
            new Dog()
        };

        foreach (var animal in animals)
        {
            animal.GetName();
            animal.Move();
            animal.Eat();
        }
    }
}
```

**The code is correct because the Cat and Dog classes can be replaced by the Animal class without having unexpected behaviors.**

## Interface Segregation Principle

The Interface Segregation Principle says that many specific interfaces are better than a single interface.

### Bad

```cs
public interface IBase
{
    void ChangeId(int id);

    void ChangeAddress(string address);

    void ChangePrice(decimal price);
}
```

```cs
public sealed class Customer : IBase
{
    public int Id { get; set; }

    public string Name { get; set; }

    public string Address { get; set; }

    public void ChangeId(int id)
    {
        Id = id;
    }

    public void ChangeAddress(string address)
    {
        Address = address;
    }

    public void ChangePrice(decimal price)
    {
        throw new NotImplementedException();
    }
}
```

```cs
public sealed class Product : IBase
{
    public int Id { get; set; }

    public string Description { get; set; }

    public decimal Price { get; set; }

    public void ChangeId(int id)
    {
        Id = id;
    }

    public void ChangePrice(decimal price)
    {
        Price = price;
    }

    public void ChangeAddress(string address)
    {
        throw new NotImplementedException();
    }
}
```
**The code is incorrect because the Customer class is required to have the ChangePrice method, and the Product class is required to have the ChangeAddress method, only because they implement the same interface.**

### Good

```cs
public interface IBase
{
    void ChangeId(int id);
}
```

```cs
public interface ICustomer : IBase
{
    void ChangeAddress(string address);
}
```

```cs
public interface IProduct : IBase
{
    void ChangePrice(decimal price);
}
```

```cs
public sealed class Customer : ICustomer
{
    public int Id { get; set; }

    public string Name { get; set; }

    public string Address { get; set; }

    public void ChangeId(int id)
    {
        Id = id;
    }

    public void ChangeAddress(string address)
    {
        Address = address;
    }
}
```

```cs
public sealed class Product : IProduct
{
    public int Id { get; set; }

    public string Description { get; set; }

    public decimal Price { get; set; }

    public void ChangeId(int id)
    {
        Id = id;
    }

    public void ChangePrice(decimal price)
    {
        Price = price;
    }
}
```

**The code is correct because the generic interface has been split into specific interfaces. The classes do not implement methods that are not part of your business logic.**

## Dependency Inversion Principle

The Dependency Inversion Principle says to depend on abstraction, not implementation.

### Bad

```cs
public sealed class Customer
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

```cs
public sealed class CustomerRepository
{
    public CustomerRepository(string connectionString)
    {
        /// Implementation
    }

    public void Add(Customer customer)
    {
        /// Implementation
    }
}
```

```cs
public sealed class CustomerService
{
    private readonly CustomerRepository _customerRepository = new CustomerRepository("ConnectionString");

    public void Add(Customer customer)
    {
        _customerRepository.Add(customer);
    }
}
```

**The code is incorrect because the CustomerService class depends on the CustomerRepository class and also knows how to instantiate it.**

### Good

```cs
public sealed class Customer
{
    public int Id { get; set; }

    public string Name { get; set; }
}
```

```cs
public interface ICustomerRepository
{
    void Add(Customer customer);
}
```

```cs
public sealed class CustomerRepository : ICustomerRepository
{
    public CustomerRepository(string connectionString)
    {
        /// Implementation
    }

    public void Add(Customer customer)
    {
        /// Implementation
    }
}
```

```cs
public sealed class CustomerService
{
    private readonly ICustomerRepository _customerRepository;

    public CustomerService(ICustomerRepository customerRepository)
    {
        _customerRepository = customerRepository;
    }

    public void Add(Customer customer)
    {
        _customerRepository.Add(customer);
    }
}
```

**The code is correct because the CustomerService class depends only on the ICustomerRepository interface. It does not know the implementation or how to instantiate it.**
