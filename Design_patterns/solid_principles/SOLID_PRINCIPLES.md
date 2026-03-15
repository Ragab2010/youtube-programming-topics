# SOLID PRINCIPLES

The **SOLID principles** are five core design principles in **object-oriented programming** that help make software **maintainable, scalable, and easy to extend**.
✅ **In one sentence: SOLID makes code cleaner, easier to change, easier to test, and easier to grow**

Here is the **simplified idea of what SOLID gives to your code**

### 1️⃣ Easy to Understand

Each class has **one clear job**, so the code is easier to read.

---

### 2️⃣ Easy to Maintain

When you change something, you **don’t break many other parts** of the program.

---

### 3️⃣ Easy to Extend

You can **add new features without modifying old code**.

---

### 4️⃣ Easy to Test

Small independent classes make **unit testing easier**.

---

### 5️⃣ More Flexible

Your code can **work with different implementations**.

---

# Single Responsibility Principle (SRP)

The **Single Responsibility Principle** is the first principle of SOLID.

> **A class should have only one reason to change.**
> 

That means:

- A class should do **one job**
- That job should be **clearly defined**
- If requirements change, only **one aspect** of the system should force that class to change

---

# ❌ Bad Example (Violates SRP)

Let’s say we build a `Report` class:

```cpp
class Report {
public:
    void generate() {
        // business logic
    }

    void saveToFile(const std::string& filename) {
        // file I/O
    }

    void print() {
        // printing logic
    }
};
```

### What's wrong?

This class has **3 responsibilities**:

1. Business logic (generate report)
2. File persistence
3. Printing

If:

- File format changes → class changes
- Printing logic changes → class changes
- Business rules change → class changes

👉 **Multiple reasons to change → SRP violation**

---

# ✅ Correct SRP Design

Separate responsibilities:

```cpp
class Report {
public:
    std::string generate() {
        return "report content";
    }
};

class ReportFileSaver {
public:
    void save(const std::string& content, const std::string& filename) {
        // file logic
    }
};

class ReportPrinter {
public:
    void print(const std::string& content) {
        // print logic
    }
};
```

Now:

- Report changes only if business logic changes
- ReportFileSaver changes only if file logic changes
- ReportPrinter changes only if printing changes

✔ Each class has one reason to change.

---

# 🎯 PlantUML Diagram (SRP Applied)

```
@startuml

class Report {
    +generate() : string
}

class ReportFileSaver {
    +save(content : string, filename : string)
}

class ReportPrinter {
    +print(content : string)
}

ReportFileSaver --> Report
ReportPrinter --> Report

@enduml
```

![image.png](SOLID%20PRINCIPLES/image.png)

---

# 💡 Real-World Practical Example (Banking System)

### ❌ Wrong Design

```cpp
class BankAccount {
public:
    void deposit(double amount);
    void withdraw(double amount);
    void saveToDatabase();
    void sendEmailNotification();
};
```

Problems:

- Financial logic
- Database logic
- Email logic

That's **3 responsibilities**

---

### ✅ Better Design

```cpp
class BankAccount {
public:
    void deposit(double amount);
    void withdraw(double amount);
};

class AccountRepository {
public:
    void save(const BankAccount& account);
};

class NotificationService {
public:
    void sendWithdrawalEmail(const BankAccount& account);
};
```

Now:

- Account logic is separated
- Infrastructure logic is separated

This makes:

- Testing easier
- Code more maintainable
- Code more scalable

---

# 🧠 Important Clarification

SRP is **NOT**:

- "One method per class"
- "Small classes only"
- "Only one function allowed."

It is:

> One **responsibility axis**
> 

If a class changes for unrelated reasons→ it violates SRP.

---

🚀 A "responsibility" is tied to an **actor**.

Ask:

- Who would request this change?
    - Business?
    - DBA?
    - DevOps?
    - UI team?

If multiple actors can force modifications → you probably violated SRP.

---

# Open–Closed Principle (OCP)

> **Software entities should be open for extension, but closed for modification.**
> 

✔**Open for extension**

❌**Closed for modification**

Meaning:

- You should be able to **add new behavior**
- Without changing existing, tested code

This reduces:

- Risk of breaking working features
- Merge conflicts

---

# ❌ Bad Example (Violates OCP)

Imagine a payment processor:

```cpp
class PaymentProcessor {
public:
    void processPayment(const std::string& type) {
        if (type == "credit") {
            // credit logic
        }
        else if (type == "paypal") {
            // paypal logic
        }
    }
};
```

### What's wrong?

If we add:

- Apple Pay
- Crypto
- Bank transfer

We must modify `PaymentProcessor`.

👉 Every new feature modifies old code

👉 Violates OCP

---

# ✅ Correct OCP Design (Using Runtime/dynamic Polymorphism)

Use abstraction.

```cpp
class PaymentMethod {
public:
    virtual ~PaymentMethod() = default;
    virtual void pay(double amount) = 0;
};
```

Concrete implementations:

```cpp
class CreditCard : public PaymentMethod {
public:
    void pay(double amount) override {
        std::cout << "Paid " << amount << " with Credit Card\n";
    }
};

class PayPal : public PaymentMethod {
public:
    void pay(double amount) override {
        std::cout << "Paid " << amount << " with PayPal\n";
    }
};
```

Processor:

```cpp
class PaymentProcessor {
public:
    void process(PaymentMethod& method, double amount) {
        method.pay(amount);
    }
};
```

---

### Adding New Payment Type

```cpp
class Crypto : public PaymentMethod {
public:
    void pay(double amount) override {
        std::cout << "Paid " << amount << " with Crypto\n";
    }
};
```

✅ No change to `PaymentProcessor`

✅ No change to existing code

✅ Only extension

That’s OCP.

```cpp
#include <iostream>
#include <string>

using namespace std;

// Abstraction
class PaymentMethod {
public:
    virtual ~PaymentMethod() = default;
    virtual void pay(double amount) = 0;
};

// Concrete Implementation 1
class CreditCard : public PaymentMethod {
public:
    void pay(double amount) override {
        cout << "Paid " << amount << " with Credit Card" << endl;
    }
};

// Concrete Implementation 2
class PayPal : public PaymentMethod {
public:
    void pay(double amount) override {
        cout << "Paid " << amount << " with PayPal" << endl;
    }
};

// New Extension (without modifying existing code)
class Crypto : public PaymentMethod {
public:
    void pay(double amount) override {
        cout << "Paid " << amount << " with Crypto" << endl;
    }
};

// Processor (Closed for modification)
class PaymentProcessor {
public:
    void process(PaymentMethod& method, double amount) {
        method.pay(amount);
    }
};

int main() {

    PaymentProcessor processor;

    CreditCard credit;
    PayPal paypal;
    Crypto crypto;

    cout << "Processing payments:\n\n";

    processor.process(credit, 100.0);
    processor.process(paypal, 250.5);
    processor.process(crypto, 75.25);

    return 0;
}
```

---

# 🎯 PlantUML Diagram

```
@startuml

interface PaymentMethod {
    +pay(amount : double)
}

class CreditCard
class PayPal
class Crypto

class PaymentProcessor {
    +process(method : PaymentMethod, amount : double)
}

PaymentMethod <|.. CreditCard
PaymentMethod <|.. PayPal
PaymentMethod <|.. Crypto
PaymentProcessor --> PaymentMethod

@enduml
```

![image.png](SOLID%20PRINCIPLES/image%201.png)

---

# ✅ Correct OCP Design (Using Compiletime/static Polymorphism)

```cpp
#include <iostream>
#include <string>

using namespace std;

// Payment types (no inheritance needed)

class CreditCard {
public:
    void pay(double amount) {
        cout << "Paid " << amount << " with Credit Card" << endl;
    }
};

class PayPal {
public:
    void pay(double amount) {
        cout << "Paid " << amount << " with PayPal" << endl;
    }
};

class Crypto {
public:
    void pay(double amount) {
        cout << "Paid " << amount << " with Crypto" << endl;
    }
};

// Static OCP Processor (compile-time polymorphism)

class PaymentProcessor {
public:
		template<typename TPayment>
    void process(TPayment& payment, double amount) {
        payment.pay(amount);
    }
};

int main() {

    CreditCard credit;
    PayPal paypal;
    Crypto crypto;

    PaymentProcessor<CreditCard> creditProcessor;
    PaymentProcessor<PayPal> paypalProcessor;
    PaymentProcessor<Crypto> cryptoProcessor;

    cout << "Static OCP Payments:\n\n";

    creditProcessor.process(credit, 100);
    paypalProcessor.process(paypal, 200);
    cryptoProcessor.process(crypto, 300);

    return 0;
}
```

# 🧠 Real-World Practical Example (Discount Engine)

## ❌ Bad Design

```cpp
double calculateDiscount(std::string customerType, double price) {
    if (customerType == "regular")
        return price * 0.05;
    else if (customerType == "vip")
        return price * 0.20;
    else if (customerType == "employee")
        return price * 0.30;
}
```

Every new customer type → modify function.

---

## ✅ OCP-Compliant Version

```cpp
class DiscountStrategy {
public:
    virtual ~DiscountStrategy() = default;
    virtual double apply(double price) const = 0;
};

class RegularDiscount : public DiscountStrategy {
public:
    double apply(double price) const override {
        return price * 0.05;
    }
};

class VipDiscount : public DiscountStrategy {
public:
    double apply(double price) const override {
        return price * 0.20;
    }
};
```

Usage:

```cpp
double calculateFinalPrice(double price, const DiscountStrategy& strategy) {
    return price - strategy.apply(price);
}
```

Add new discount → create new class.

No modification required.

---

# 💡 Advanced C++ Insight

OCP works best with:

- Polymorphism (virtual functions)
- Strategy Pattern
- Dependency Injection
- Template specialization (compile-time OCP)

Example with templates:

```cpp
template<typename TPayment>
class PaymentProcessor {
public:
    void process(TPayment& payment, double amount) {
        payment.pay(amount);
    }
};
```

This gives **compile-time extension** without modifying existing logic.

---

**🔥 OCP is often misunderstood.**

Bad OCP:

- Adding inheritance everywhere
- Creating abstractions "just in case."

Correct OCP:

- Apply when variation is expected
- Don’t abstract early

---

# 🧩 Mental Model

Ask:

> “If I add a new feature tomorrow, will I modify existing code or just add new code?”
> 

If you modify → likely OCP violation.

If you extend → OCP respected.

---

# Liskov Substitution Principle (LSP)

> **Subtypes must be substitutable for their base types without breaking correctness.**
> 

If `Derived` is a subtype of `Base`, then:

```cpp
Base* obj = new Derived();
```

The program should behave correctly **without knowing it's a Derived**.

If replacing a base class with a derived class breaks behavior → **LSP is violated**.

---

# 🚨 Classic C++ Example (Rectangle / Square Problem)

## ❌ Wrong Design

```cpp
class Rectangle {
protected:
    int width;
    int height;

public:
    virtual void setWidth(int w) { width = w; }
    virtual void setHeight(int h) { height = h; }

    int getArea() const { return width * height; }
};
```

Now:

```cpp
class Square : public Rectangle {
public:
    void setWidth(int w) override {
        width = height = w;
    }

    void setHeight(int h) override {
        width = height = h;
    }
};
```

Client code:

```cpp
void process(Rectangle& rect) {
    rect.setWidth(5);
    rect.setHeight(4);
    std::cout << rect.getArea();
}
```

Expected:

```
5 * 4 = 20
```

But if `Square` is passed:

```
4 * 4 = 16
```

❌ Behavior changed

❌ Substitution broke expectations

❌ LSP violated

---

# 🎯 Why This Is a Violation

The base class assumes:

```
width and height are independent
```

But `Square` changes that contract.

Inheritance here models **"is-a" incorrectly**.

Mathematically, a square is a rectangle.

Behaviorally in software → it is not.

---

# ✅ Correct Design (Better Modeling)

Instead of forcing inheritance:

```cpp
class Shape {
public:
    virtual ~Shape() = default;
    virtual int area() const = 0;
};
```

Concrete implementations:

```cpp
class Rectangle : public Shape {
    int width, height;
public:
    Rectangle(int w, int h) : width(w), height(h) {}
    int area() const override { return width * height; }
};

class Square : public Shape {
    int side;
public:
    Square(int s) : side(s) {}
    int area() const override { return side * side; }
};
```

Now both are substitutable:

```cpp
void printArea(const Shape& shape) {
    std::cout << shape.area();
}
```

✔ No broken assumptions

✔ Clean polymorphism

✔ LSP respected

---

# 📐 PlantUML Diagram

```
@startuml

abstract class Shape {
    +area() : int
}

class Rectangle {
    -width : int
    -height : int
    +area() : int
}

class Square {
    -side : int
    +area() : int
}

Shape <|-- Rectangle
Shape <|-- Square

@enduml
```

![image.png](SOLID%20PRINCIPLES/image%202.png)

---

# 🔥 Real-World C++ Example (Bank Accounts)

## ❌ LSP Violation

```cpp
class BankAccount {
public:
    virtual void withdraw(double amount) {
        balance -= amount;
    }
protected:
    double balance = 0;
};
```

Derived:

```cpp
class FixedDepositAccount : public BankAccount {
public:
    void withdraw(double amount) override {
        throw std::logic_error("Withdrawals not allowed");
    }
};
```

Problem:

Client expects:

```cpp
account.withdraw(100);
```

But the derived class throws.

❌ Base contract broken

❌ Not substitutable

❌ LSP violation

---

# ✅ Better Design

Split behavior properly:

```cpp
class Account {
public:
    virtual ~Account() = default;
};

class WithdrawableAccount : public Account {
public:
    virtual void withdraw(double amount) = 0;
};
```

Now only accounts that support withdraw inherit it.

✔ No broken contracts

✔ Honest modeling

---

# 🧠 Deep C++ Insight (What Really Breaks LSP)

LSP is about:

### 1️⃣ Preconditions cannot be strengthened

Derived class cannot require more than base.

### 2️⃣ Postconditions cannot be weakened

Derived class cannot return less than promised.

### 3️⃣ Invariants must be preserved

Internal state rules must hold.

---

# ⚠️ Hidden LSP Violations in C++

### ❌ Overriding and throwing new exceptions

### ❌ Changing the return meaning

### ❌ Breaking const-correctness

### ❌ Violating base class invariants

Example:

```cpp
class Bird {
public:
    virtual void fly() = 0;
};

class Penguin : public Bird {
public:
    void fly() override {
        throw std::logic_error("Penguins can't fly");
    }
};
```

This is a modeling error.

Not all birds fly → base abstraction is wrong.

---

# 🧩 Mental Model

Ask:

> “If I replace every Base with Derived, will the system still behave correctly?”
> 

If you need:

- `if (dynamic_cast<Derived*>())`
- type checks
- special cases

Then LSP is likely broken.

---

**🚀Inheritance is about:**

> Behavioral substitutability — not taxonomy.
> 

Just because something **is-a** conceptually

does not mean it should be inherited in code.

Before inheriting, ask:

> **Is this truly an "is-a" relationship or just code reuse?**
> 

If behavior changes → **use composition instead**.

---

# Interface Segregation Principle (ISP)

> **Clients should not be forced to depend on interfaces they do not use.**
> 

In simple terms:

👉 **Prefer many small, focused interfaces over one large general-purpose interface.**

If a class is forced to implement methods it doesn't need → ISP is violated.

---

# ❌ Bad Example (Fat Interface)

```cpp
class Machine {
public:
    virtual void print() = 0;
    virtual void scan() = 0;
    virtual void fax() = 0;
};
```

Now, suppose we implement a simple printer:

```cpp
class SimplePrinter : public Machine {
public:
    void print() override {
        std::cout << "Printing...\n";
    }

    void scan() override {
        throw std::logic_error("Not supported");
    }

    void fax() override {
        throw std::logic_error("Not supported");
    }
};
```

### 🚨 What's wrong?

- `SimplePrinter` is forced to implement `scan()` and `fax()`
- It throws exceptions
- It lies about capabilities

This is a **clear ISP violation**.

---

# ✅ Correct Design (Segregated Interfaces)

Split responsibilities:

```cpp
class IPrinter {
public:
    virtual ~IPrinter() = default;
    virtual void print() = 0;
};

class IScanner {
public:
    virtual ~IScanner() = default;
    virtual void scan() = 0;
};

class IFax {
public:
    virtual ~IFax() = default;
    virtual void fax() = 0;
};
```

Now:

```cpp
class SimplePrinter : public IPrinter {
public:
    void print() override {
        std::cout << "Printing...\n";
    }
};
```

Multi-function device:

```cpp
class MultiFunctionPrinter : public IPrinter, public IScanner, public IFax {
public:
    void print() override { std::cout << "Printing...\n"; }
    void scan() override { std::cout << "Scanning...\n"; }
    void fax() override { std::cout << "Faxing...\n"; }
};
```

✔ No forced implementation

✔ No fake methods

✔ Clean design

---

# 🎯 PlantUML Diagram

```
@startuml

interface IPrinter {
    +print()
}

interface IScanner {
    +scan()
}

interface IFax {
    +fax()
}

class SimplePrinter
class MultiFunctionPrinter

IPrinter <|.. SimplePrinter
IPrinter <|.. MultiFunctionPrinter
IScanner <|.. MultiFunctionPrinter
IFax <|.. MultiFunctionPrinter

@enduml
```

![image.png](SOLID%20PRINCIPLES/image%203.png)

---

# 🔥 Real-World Practical C++ Example (Worker System)

## ❌ Bad Design

```cpp
class Worker {//not Person , so it can be anything can work and eat
public:
    virtual void work() = 0;
    virtual void eat() = 0;
};
```

Now:

```cpp
class Robot : public Worker {
public:
    void work() override {
        std::cout << "Working...\n";
    }

    void eat() override {
        // robots don't eat
    }
};
```

Robots don’t eat — but forced to implement it.

---

## ✅ Correct ISP Design

```cpp
class IWorkable {
public:
    virtual void work() = 0;
};

class IFeedable {
public:
    virtual void eat() = 0;
};
```

Now:

```cpp
class Human : public IWorkable, public IFeedable {
public:
    void work() override {}
    void eat() override {}
};

class Robot : public IWorkable {
public:
    void work() override {}
};
```

✔ Robot is not forced to eat

✔ Interfaces reflect real capabilities

---

# 🧠 Deep C++ Insight

ISP is extremely important when:

- Designing public APIs
- Creating SDKs
- Building plugin systems
- Designing microservices contracts

### Why?

Large interfaces cause:

- Tight coupling
- Recompilation cascades
- Fragile base classes
- Massive mocking in tests

---

# ⚠️ Common ISP Violations in C++

### ❌ God interfaces (100+ methods)

### ❌ "Manager" classes with everything

### ❌ Base classes with optional virtual methods

### ❌ Empty overrides

### ❌ Throwing "Not implemented."

If you see this:

```cpp
void someMethod() override {}
```

That’s usually ISP screaming.

---

# 🧩 Mental Model

Ask:

> "Does this class truly need all these functions?"
> 

If not:

- Split the interface
- Create capability-based abstractions

Think in terms of **roles**, not objects.

---

# Dependency Inversion Principle (DIP)

> **High-level modules should not depend on low-level modules.Both should depend on abstractions.**
> 

> **Abstractions should not depend on details.Details should depend on abstractions.**
> 

This is the principle that makes real architectures scalable.

---

# 🎯 The Core Idea

❌ Bad:

```
BusinessLogic → MySQLDatabase
```

✅ Good:

```
BusinessLogic → IDatabase ← MySQLDatabase
```

High-level policy should not depend on implementation details.

---

# ❌ Bad Example (Tight Coupling)

```cpp
class MySQLDatabase {//low-level class
public:
    void save(const std::string& data) {
        std::cout << "Saving to MySQL\n";
    }
};

class UserService {//high-level class
    MySQLDatabase db;  // concrete dependency
public:
    void registerUser(const std::string& name) {
        db.save(name);
    }
};
```

### Problem

- `UserService` depends directly on `MySQLDatabase`
- Switching to PostgreSQL requires modifying `UserService`
- Hard to unit test

❌ Violates DIP

❌ Violates OCP

❌ Hard to mock

---

# ✅ Correct Design (Dependency Inversion Applied)

## Step 1: Create an abstraction

```cpp
class IDatabase {
public:
    virtual ~IDatabase() = default;
    virtual void save(const std::string& data) = 0;
};
```

## Step 2: Concrete implementation depends on abstraction

```cpp
class MySQLDatabase : public IDatabase {
public:
    void save(const std::string& data) override {
        std::cout << "Saving to MySQL\n";
    }
};
```

## Step 3: The high-level module depends on the abstraction

```cpp
class UserService {
    IDatabase& db;  // abstraction
public:
    UserService(IDatabase& database) : db(database) {}

    void registerUser(const std::string& name) {
        db.save(name);
    }
};
```

---

# 🔥 Now We Can Inject Anything

```cpp
MySQLDatabase mysql;
UserService service(mysql);
```

Or:

```cpp
class MockDatabase : public IDatabase {
public:
    void save(const std::string&) override {
        std::cout << "Mock save\n";
    }
};
```

Perfect for unit testing.

✔ High-level independent

✔ Low-level replaceable

✔ Open for extension

---

# 📐 PlantUML Diagram

```
@startuml

interface IDatabase {
    +save(data : string)
}

class MySQLDatabase
class UserService {
    -db : IDatabase
    +registerUser(name : string)
}

IDatabase <|.. MySQLDatabase
UserService --> IDatabase

@enduml
```

![image.png](SOLID%20PRINCIPLES/image%204.png)

---

# 🧠 The Inversion Explained

Normally:

```
Low-level → High-level
```

With DIP:

```
High-level → Abstraction ← Low-level
```

The direction of dependency is inverted.

---

# 🏗 Real Production Example (Logger System)

## ❌ Without DIP

```cpp
class FileLogger {//low-level class
public:
    void log(const std::string& msg) {
        std::cout << "Writing to file\n";
    }
};

class OrderService {//high-level class
    FileLogger logger;
};
```

Switch to cloud logging → modify `OrderService`.

---

## ✅ With DIP

```cpp
class ILogger {
public:
    virtual ~ILogger() = default;
    virtual void log(const std::string& msg) = 0;
};

class FileLogger : public ILogger {
public:
    void log(const std::string& msg) override {
        std::cout << "File log\n";
    }
};

class CloudLogger : public ILogger {
public:
    void log(const std::string& msg) override {
        std::cout << "Cloud log\n";
    }
};
```

Now:

```cpp
class OrderService {
    ILogger& logger;
public:
    OrderService(ILogger& log) : logger(log) {}
};
```

Add new logger → no modification needed.

---

**🚀DIP is usually implemented via:**

- Constructor Injection (most common)
- Setter Injection
- Factory pattern
- Service Locator (careful)
- Dependency Injection containers

In modern C++:

- `std::unique_ptr<IDatabase>`
- `std::shared_ptr<IDatabase>`
- Template-based policy injection (compile-time DIP)

---

# ⚠️ Common Mistakes

### ❌ Creating interfaces for everything

Only abstract when:

- There is variation
- You expect a replacement
- You need testability

### ❌ Owning concrete objects inside high-level classes

Avoid this:

```cpp
IDatabase db = MySQLDatabase(); // slicing risk
```

Use references or smart pointers.

---

# 🧩 Mental Model

Ask:

> “Does my business logic know about infrastructure details?”
> 

If yes → DIP violation.

Business logic should not care whether:

- It’s MySQL
- PostgreSQL
- MongoDB
- In-memory mock

---

# 🎯 SOLID Summary

| Principle | Real Meaning |
| --- | --- |
| **SRP** | One class → one job |
| **OCP** | Add behavior without editing old code |
| **LSP** | sub classes must behave like base class |
| **ISP** | Small focused interfaces |
| **DIP** | Depend on abstractions |

| Principle | Protects Against |
| --- | --- |
| **SRP** | Multiple reasons to change |
| **OCP** | Modifying stable code |
| **LSP** | Broken polymorphism |
| **ISP** | Fat interfaces |
| **DIP** | Tight coupling |

---

# Apply solid principles to a course management system code

Use Case Diagram:

```cpp
@startuml
left to right direction

actor Admin
actor Instructor
actor Student

rectangle "Course_Management_System" {
:Student: ---> (Register)
:Student: ---> (login)
:Student: ---> (Browse Courses)
:Student: ---> (Enroll in course)
:Student: ---> (View Course Content)
:Student: ---> (Submit Assignment)
:Student: ---> (View Grades)
}
@enduml

```

![image.png](SOLID%20PRINCIPLES/image%205.png)

# 1️⃣ Full SOLID Code (with CLI + Registration)

```cpp
#include <iostream>
#include <vector>
#include <memory>
#include <string>
#include <algorithm>

//
// ============================
// 1️⃣ Interfaces (Abstractions)
// ============================
//

class IUser {
public:
    virtual ~IUser() = default;

    virtual bool login(const std::string& password) = 0;
    virtual void logout() = 0;

    virtual int getId() const = 0;
    virtual std::string getName() const = 0;
};

class ICourse {
public:
    virtual ~ICourse() = default;

    virtual std::string getTitle() const = 0;
    virtual void displayDescription() const = 0;
};

class IAssignment {
public:
    virtual ~IAssignment() = default;

    virtual void submit(int grade) = 0;
    virtual int getGrade() const = 0;
    virtual std::string getTitle() const = 0;
};

//
// ============================
// 2️⃣ Base Class: User
// ============================
//

class User : public IUser {
protected:
    int mId{};
    std::string mName;
    std::string mPassword;
    bool mLoggedIn{false};

public:
    User(int id, std::string name, std::string password)
        : mId{id}, mName{std::move(name)}, mPassword{std::move(password)} {}

    bool login(const std::string& password) override {
        if (password == mPassword) {
            mLoggedIn = true;
            return true;
        }
        return false;
    }

    void logout() override {
        mLoggedIn = false;
    }

    int getId() const override { return mId; }

    std::string getName() const override { return mName; }
};

//
// ============================
// 3️⃣ Assignment Implementation
// ============================
//

class Assignment : public IAssignment {
private:
    std::string mTitle;
    std::string mDescription;
    int mGrade{0};

public:
    Assignment(std::string title, std::string description)
        : mTitle(std::move(title)),
          mDescription(std::move(description)) {}

    void submit(int grade) override {
        mGrade = grade;
    }

    int getGrade() const override {
        return mGrade;
    }

    std::string getTitle() const override {
        return mTitle;
    }
};

//
// ============================
// 4️⃣ Course Implementation
// ============================
//

class Course : public ICourse {
private:
    std::string mTitle;
    std::string mDescription;

    std::vector<std::unique_ptr<IAssignment>> mAssignments;

public:
    Course(std::string title, std::string description)
        : mTitle(std::move(title)),
          mDescription(std::move(description)) {}

    void addAssignment(std::unique_ptr<IAssignment> assignment) {
        mAssignments.push_back(std::move(assignment));
    }

    const std::vector<std::unique_ptr<IAssignment>>&
    getAssignments() const {
        return mAssignments;
    }

    std::string getTitle() const override {
        return mTitle;
    }

    void displayDescription() const override {
        std::cout << "Course: " << mTitle << "\n"
                  << "Description: " << mDescription << "\n";
    }
};

//
// ============================
// 5️⃣ Student Implementation
// ============================
//

class Student : public User {
private:
    std::vector<std::shared_ptr<ICourse>> mCourses;

public:
    using User::User;

    void enrollCourse(const std::shared_ptr<ICourse>& course) {
        mCourses.push_back(course);
    }

    void removeCourse(const std::shared_ptr<ICourse>& course) {

        auto it = std::find(mCourses.begin(), mCourses.end(), course);

        if (it != mCourses.end())
            mCourses.erase(it);
    }

    void browseCourses(const std::vector<std::shared_ptr<ICourse>>& courses) const {

        for (const auto& c : courses)
            c->displayDescription();
    }

    void viewGrades() const {

        for (const auto& course : mCourses) {

            auto realCourse = std::dynamic_pointer_cast<Course>(course);

            if (!realCourse)
                continue;

            for (const auto& assignment : realCourse->getAssignments()) {

                std::cout << "Course: "
                          << realCourse->getTitle()
                          << " | Assignment: "
                          << assignment->getTitle()
                          << " | Grade: "
                          << assignment->getGrade()
                          << "\n";
            }
        }
    }
};

//
// ============================
// 6️⃣ CourseSystem (Director)
// ============================
//

class CourseSystem {
private:

    std::vector<std::shared_ptr<IUser>> mStudents;
    std::vector<std::shared_ptr<ICourse>> mCourses;

public:

    void registerStudent(const std::shared_ptr<IUser>& student) {
        mStudents.push_back(student);
    }

    void addCourse(const std::shared_ptr<ICourse>& course) {
        mCourses.push_back(course);
    }

    const std::vector<std::shared_ptr<ICourse>>&
    getAllCourses() const {
        return mCourses;
    }

    void browseCourses() const {

        for (const auto& c : mCourses)
            c->displayDescription();
    }
};

//
// ============================
// 7️⃣ Main
// ============================
//

int main() {

    CourseSystem system;

    auto cppCourse = std::make_shared<Course>(
        "C++ Fundamentals",
        "Learn basics of C++ programming."
    );

    auto oopCourse = std::make_shared<Course>(
        "Object-Oriented Programming",
        "Deep dive into OOP concepts."
    );

    system.addCourse(cppCourse);
    system.addCourse(oopCourse);

    auto a1 = std::make_unique<Assignment>(
        "Variables and Data Types",
        "Practice variables"
    );

    auto a2 = std::make_unique<Assignment>(
        "Classes and Objects",
        "Create a class"
    );

    cppCourse->addAssignment(std::move(a1));
    oopCourse->addAssignment(std::move(a2));

    auto student1 = std::make_shared<Student>(
        1, "Ahmed", "1234"
    );

    system.registerStudent(student1);

    std::cout << "\nLogin attempt\n";

    if (student1->login("1234"))
        std::cout << "Login successful\n";

    std::cout << "\nBrowse courses\n";
    student1->browseCourses(system.getAllCourses());

    student1->enrollCourse(cppCourse);

    cppCourse->getAssignments()[0]->submit(95);

    std::cout << "\nGrades\n";
    student1->viewGrades();

    student1->logout();
}
```

---

# 2️⃣ UML Diagram (PlantUML)

```
@startuml
left to right direction
skinparam classAttributeIconSize 0

'====================
' Interfaces
'====================

interface IUser{
    #mId : int
    #mName : string
    #mPassword : string
    #mLoggedIn : bool
    +login(password:string) : bool
    +logout() : void
    +getName() : string
    +getId() : int
}

interface IAssignment{
    #mTitle : string
    #mDescription : string
    #mGrade : int
    +submit(grade:int) : void
    +getGrade() : int
    +getTitle() : string
    +getDescription() : string
}

interface ICourse{
    +addAssignment(assignment:IAssignment)
    +displayDescription() : void
    +getTitle() : string
    +getAssignments() : vector<IAssignment>
}

'====================
' User hierarchy
'====================

class User{
    +login(password:string) : bool
    +logout() : void
    +getName() : string
    +getId() : int
}

class Student{
    -mCourses : vector<ICourse>
    +enrollCourse(course:ICourse)
    +removeCourse(course:ICourse)
    +browseCourses(allCourses:vector<ICourse>)
    +viewGrades()
    +getCourses() : vector<ICourse>
}

IUser <|.. User
User <|-- Student

'====================
' Assignment
'====================

class Assignment{
    +submit(grade:int)
    +getGrade() : int
    +getTitle() : string
    +getDescription() : string
}

IAssignment <|.. Assignment

'====================
' Course
'====================

class Course{
    -mTitle : string
    -mDescription : string
    -mAssignments : vector<IAssignment>
    +addAssignment(assignment:IAssignment)
    +displayDescription()
    +getTitle() : string
    +getAssignments()
}

ICourse <|.. Course

'====================
' System Director
'====================

class CourseSystem{
    -mStudents : vector<Student>
    -mCourses : vector<ICourse>
    +registerStudent(student:Student)
    +addCourse(course:ICourse)
    +getAllCourses() : vector<ICourse>
    +browseCourses()
    +findStudent(id:int) : Student
    +login(id:int,password:string) : Student
}

'====================
' CLI Layer
'====================

class CourseSystemCLI{
    -mSystem : CourseSystem
    -mCurrentStudent : Student
    +start()
    -registerStudent()
    -login()
    -studentMenu()
    -enrollCourse()
    -submitAssignment()
}

'====================
' Relationships
'====================

Student "1" o-- "*" ICourse : enrolls
Course "1" *-- "*" IAssignment : contains

CourseSystem "1" o-- "*" Student
CourseSystem "1" o-- "*" ICourse

CourseSystemCLI --> CourseSystem
CourseSystemCLI --> Student

@enduml
```

---

![image.png](SOLID%20PRINCIPLES/image%206.png)