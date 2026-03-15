
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
## the Code That written in Video:
```cpp

class Person {//represent person in real-world
protected:
    std::string name;
    std::string address;

public:
    Person(std::string n, std::string a)
        : name(n), address(a) {}

    /***********/
    void run() {
        std::cout << name << " is running\n";
    }
    void speack() {
        std::cout << name << " is speack\n";
    }
    /*************/
    /*************/


};

class DatabasePerson:public Person{
public:
    void saveInfoToDatabase(){
        
    }
};

solution:
relationship between Database and Person (dependency)
class Person {//represent person in real-world
protected:
    std::string name;
    std::string address;

public:
    Person(std::string n, std::string a)
        : name(n), address(a) {}

    void run() {
        std::cout << name << " is running\n";
    }
    void speack() {
        std::cout << name << " is speack\n";
    }

};

class Database{
public:
    
    /*************/
    static void saveInfo(Person & ref){
        ///
    }
    /*************/
};


int main(){
    Person ragab("ragab" , "Mansoura");
    Database::saveInfo(ragab);
    return 0;
}

```