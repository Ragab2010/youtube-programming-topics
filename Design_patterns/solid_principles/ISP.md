
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