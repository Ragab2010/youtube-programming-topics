
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

## the Code That written in Video:
```cpp
class IDatabase{
public:
    virtual ~IDatabase()=default;
    virtual void save(const std::string& data)=0;
};
class MySQLDatabase :public IDatabase{
public:
    void save(const std::string& data) {
        std::cout << "Saving to MySQL\n";
        std::cout << data;
    }
};

class UserService {
    // MySQLDatabase db;  // concrete dependency
    IDatabase * db;
public:
    UserService(IDatabase* ptr_db):db{ptr_db}{}
    void setDb(IDatabase* ptr_db){
        db=ptr_db;
    }
    void registerUser(const std::string& name) {
        db->save(name);
    }
};

class RagabDatabase :public IDatabase{
public:
    void save(const std::string& data) {
        std::cout << "Saving to RagabDatabase\n";
        std::cout << data;
    }
};


int main(){
    MySQLDatabase mysql;//lowlevel //logic
    UserService us(&mysql); // high-level, inject mysql into us object
    us.registerUser("hellllllo\n");
    RagabDatabase rgbase;//lowlevel //logic
    us.setDb(&rgbase); // high-level, inject rgbase into us object
    us.registerUser("world\n");

    return 0;
}
```