# Introduction to Design Patterns

---

## 🧠 Simple Definition

> A **design pattern** is a **general solution to a recurring problem in software design**. 
A **design pattern** is a **reusable solution to a common problem in software design**.
> 

It’s **not code**, and it’s **not a library**.

It’s more like a **template/blueprint** you apply in different situations.

Think of it like this:

- In hardware → standard circuits (e.g., amplifier design)
- In Code → patterns for structuring classes & interactions

---

## 🎯 Why Design Patterns Matter

They help you:

- Write **cleaner, flexible, more maintainable code**
- Follow **SOLID principles**
- Improve **communication** (developers recognize patterns by name)
- Avoid reinventing solutions

---

## 🧩 Main Categories of Design Patterns

### 1. **Creational Patterns** (Object Creation)

Control how objects are created.

Examples:

- **Singleton** → Only one instance exists
- **Factory Method** → Create objects without specifying the exact class
- **Abstract Factory** → Create families of related objects
- **Builder** → Step-by-step object construction

---

### 2. **Structural Patterns** (Object Composition)

How classes/objects are composed.

Examples:

- **Adapter** → Convert one interface into another
- **Decorator** → Add behavior dynamically
- **Facade** → Simplify complex systems
- **Composite** → Tree-like structures

---

### 3. **Behavioral Patterns** (Communication)

How objects interact.

Examples:

- **Observer** → Notify multiple objects of changes
- **Strategy** → Switch algorithms at runtime
- **Command** → Encapsulate requests
- **State** → Change behavior based on state

---

## 💡 Example (C++ – Singleton)

```cpp
class Singleton {
private:
    static Singleton* instance;
    Singleton() {} // private constructor

public:
    static Singleton* getInstance() {
        if (!instance)
            instance = new Singleton();
        return instance;
    }
};

Singleton* Singleton::instance = nullptr;
```

## 🧱 With PlantUML (Example: Singleton Diagram)

```
@startuml
class Singleton {
  -instance : Singleton
  -Singleton()
  +getInstance() : Singleton
}
@enduml
```

![image.png](Introduction%20to%20Design%20Patterns/image.png)

---

# 🧠  Pattern vs Algorithm

## Core Difference (in one sentence)

- **Design Pattern** → *How you structure your code*
- **Algorithm** → *How you solve a specific problem step-by-step*

## 🔹 1. What is a Design Pattern?

A **design pattern** is a **high-level solution (about software architecture and relationships between classes/objects).**

- Focus: **Architecture & design**
- Scope: Classes, objects, relationships
- Goal: Flexibility, maintainability, reuse

👉 Think: *“How should I organize my code?”*

---

## 🔹 2. What is an algorithm?

An **algorithm** is a **step-by-step procedure to solve a specific problem**.

- Focus: **Logic & computation**
- Scope: Functions, data processing
- Goal: Correctness, efficiency (time/space)

👉 Think: *“How do I solve this problem?”*

---

# ⚖️ Key Differences

| Aspect | Design Pattern 🏗️ | Algorithm ⚙️ |
| --- | --- | --- |
| Level | High-level (architecture) | Low-level (logic) |
| Purpose | Code organization | Problem-solving |
| Reusability | Structural reuse | Logic reuse |
| Example | Singleton, Observer | Binary Search, QuickSort |
| Input/Output | Not required | Always defined |
| Focus | Relationships | Steps & operations |

---

# 💡 Simple Analogy

- **Design Pattern** = Blueprint of a house 🏠
- **Algorithm** = Recipe to cook food. 🍳

You can use:

- Same **blueprint** with different materials
- Same **recipe** with different ingredients

---

# 🔧 C++ Example

## 🔹 Algorithm Example (Binary Search)

```cpp
int binarySearch(int arr[], int n, int target) {
    int left = 0, right = n - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

👉 Focus: **Steps to find an element**

---

## 🔹 Design Pattern Example (Strategy Pattern)

```cpp
class Strategy {
public:
    virtual int execute(int a, int b) = 0;
};

class Add : public Strategy {
public:
    int execute(int a, int b) override { return a + b; }
};

class Context {
private:
    Strategy* strategy;
public:
    Context(Strategy* s) : strategy(s) {}
    int run(int a, int b) { return strategy->execute(a, b); }
};
```

👉 Focus: **Switching behavior dynamically**

---

# 🔗 How They Work Together

In real systems, you often use **both**:

- Pattern → organizes code
- Algorithm → does the actual work

💡 Example:

- Strategy Pattern → choose sorting method
- Algorithm → QuickSort / MergeSort

---

# 🚀 Interview-Level Insight

If you say this, you sound senior:

> “Design patterns structure *how objects collaborate*, while algorithms define *how data is processed*. Patterns operate at the architectural level; algorithms operate at the computational level.”
> 

---