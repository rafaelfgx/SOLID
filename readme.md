# SOLID

SOLID is an acronym created by Michael Feathers from the principles of object-oriented programming identified by Robert C. Martin (Uncle Bob).

These principles aim to make the code more readable, easy to maintain, extensible, reusable and without repetition.

## Single Responsibility Principle

The Single Responsibility Principle says that a class must have only one reason to change and only one responsibility.

### Bad

```cs
public interface IUserService
{
    public void Activate(); // Domain

    public void InsertDatabase(); // Database

    public void SendEmail(); // Email
}

public sealed class UserService : IUserService
{
    public void Activate() { /* Domain */ }

    public void InsertDatabase() { /* Database */ }

    public void SendEmail() { /* Email */ }
}
```

**The code is incorrect because it has two responsibilities, business rules and database persistence.**

### Good

```cs
public interface IUserService
{
    public void Activate();
}

public sealed class UserService : IUserService
{
    public void Activate() { }
}

public interface IUserRepository
{
    public void Insert();
}

public sealed class UserRepository : IUserRepository
{
    public void Insert() { }
}

public interface IEmailService
{
    public void Send();
}

public sealed class EmailService : IEmailService
{
    public void Send() { }
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

public sealed record Payment(PaymentMethod PaymentMethod);

public interface IPaymentService
{
    public void Pay(Payment payment);
}

public sealed class PaymentService : IPaymentService
{
    public void Pay(Payment payment)
    {
        // Code

        if (payment.PaymentMethod == PaymentMethod.Cash)
        {
            // Code
        }
        else if (payment.PaymentMethod == PaymentMethod.CreditCard)
        {
            // Code
        }
        else if (payment.PaymentMethod == PaymentMethod.DebitCard)
        {
            // Code
        }

        // Code
    }
}
```

**The code is incorrect because it is open for modification. If a new payment method is added, the class has to be changed.**

### Good

```cs
public sealed record Payment;

public interface IPaymentMethod
{
    void Pay(Payment payment);
}

public sealed class Cash : IPaymentMethod
{
    public void Pay(Payment payment) { }
}

public sealed class CreditCard : IPaymentMethod
{
    public void Pay(Payment payment) { }
}

public sealed class DebitCard : IPaymentMethod
{
    public void Pay(Payment payment) { }
}

public interface IPaymentService
{
    public void Pay(Payment payment);
}

public sealed class PaymentService : IPaymentService
{
    private readonly IPaymentMethod _paymentMethod;

    public PaymentService(IPaymentMethod paymentMethod)
    {
        _paymentMethod = paymentMethod;
    }

    public void Pay(Payment payment)
    {
        // Code
        _paymentMethod.Pay(payment);
        // Code
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
    public void Eat() { }

    public void Run() { }

    public virtual void Sound() { }
}

public class Dog : Cat { }

public static class Program
{
    public static void Main()
    {
        Cat animal = new Cat();

        animal.Sound();

        animal = new Dog();

        animal.Sound();
    }
}
```

**The code is incorrect because the Dog class is inheriting from the Cat class only because it has similar behaviors.**

### Good

```cs
public abstract class Animal
{
    public void Eat() { }

    public void Run() { }

    public abstract void Sound();
}

public sealed class Dog : Animal
{
    public override void Sound() { }
}

public sealed class Cat : Animal
{
    public override void Sound() { }
}

public static class Program
{
    public static void Main()
    {
        Animal animal = new Cat();

        animal.Sound();

        animal = new Dog();

        animal.Sound();
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

public sealed class Customer : IBase
{
    public void ChangeId(int id) { }

    public void ChangeAddress(string address) { }

    public void ChangePrice(decimal price) => throw new NotImplementedException();
}

public sealed class Product : IBase
{
    public void ChangeId(int id) { }

    public void ChangeAddress(string address) => throw new NotImplementedException();

    public void ChangePrice(decimal price) { }
}
```

**The code is incorrect because the Customer class is required to have the ChangePrice method, and the Product class is required to have the ChangeAddress method, only because they implement the same interface.**

### Good

```cs
public interface IBase
{
    void ChangeId(int id);
}

public interface ICustomer : IBase
{
    void ChangeAddress(string address);
}

public interface IProduct : IBase
{
    void ChangePrice(decimal price);
}

public sealed class Customer : ICustomer
{
    public void ChangeId(int id) { }

    public void ChangeAddress(string address) { }
}

public sealed class Product : IProduct
{
    public void ChangeId(int id) { }

    public void ChangePrice(decimal price) { }
}
```

**The code is correct because the generic interface has been split into specific interfaces. The classes do not implement methods that are not part of your business logic.**

## Dependency Inversion Principle

The Dependency Inversion Principle says to depend on abstraction, not implementation.

### Bad

```cs
public sealed record User;

public sealed class UserRepository
{
    public UserRepository(string connectionString) { }

    public void Add(User user) { }
}

public interface IUserService
{
    void Add(User user);
}

public sealed class UserService : IUserService
{
    private readonly UserRepository _userRepository;

    public UserService()
    {
        _userRepository = new UserRepository("ConnectionString");
    }

    public void Add(User user)
    {
        _userRepository.Add(user);
    }
}
```

**The code is incorrect because the UserService class depends on the UserRepository class and also knows how to instantiate it.**

### Good

```cs
public sealed record User;

public interface IUserRepository
{
    void Add(User user);
}

public sealed class UserRepository : IUserRepository
{
    public UserRepository(string connectionString) { }

    public void Add(User user) { }
}

public interface IUserService
{
    void Add(User user);
}

public sealed class UserService : IUserService
{
    private readonly IUserRepository _userRepository;

    public UserService(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    public void Add(User user)
    {
        _userRepository.Add(user);
    }
}
```

**The code is correct because the UserService class depends only on the IUserRepository interface. It does not know the implementation or how to instantiate it.**
