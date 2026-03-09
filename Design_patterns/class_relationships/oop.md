# 1. What is Object-Oriented Programming (OOP)?

**OOP** is a programming style where we represent **real-world objects** as **classes and objects** in code.

Examples in real life:

- Person
- Car
- Book
- BankAccount

Each real-world thing has:

- **Properties (data)** → age, name
- **Behaviors (actions)** → walk, speak

In programming we represent them using **classes and objects**.

---

# 2. What is a Class?

A **class** is a **blueprint** (template) for creating objects.

Example:

Imagine a **Person blueprint**.

It defines:

- what a person **has** (name, age)
- what a person **can do** (talk, walk)

### C++ Example

```cpp
class Person {
public:
    string name;
    int age;

    void speak() {
        cout << "Hello!" << endl;
    }
};
```

So the **class defines structure**, but it is **not a real person yet**.

---

# 3. What is an Object?

An **object** is a **real instance of a class**.

Example:

```cpp
Person p1;
```

Now `p1` is a **real object created from the Person class**.

We can assign values:

```cpp
p1.name = "Ahmed";
p1.age = 25;

p1.speak();
```

---

# 4. Simple Visualization

Reality:

```
Person
  ├─ name
  ├─ age
  └─ speak()
```

Programming:

```
Class → Person
Object → p1 , p2 , p3
```

Example objects:

```
p1 → Ahmed, 25
p2 → Sara, 30
```

---

# 5. Simple Full C++ Example

```cpp
#include <iostream>
using namespace std;

class Person {
public:
    string name;
    int age;

    void introduce() {
        cout << "My name is " << name << " and I am " << age << " years old." << endl;
    }
};

int main() {

    Person p1;
    p1.name = "Ali";
    p1.age = 22;

    Person p2;
    p2.name = "Mona";
    p2.age = 30;

    p1.introduce();
    p2.introduce();

    return 0;
}
```

Output

```
My name is Ali and I am 22 years old.
My name is Mona and I am 30 years old.
```

---

# 6. What does a Class Consist Of?

A class usually contains:

### 1️⃣ Attributes (Data / Properties)

```
name
age
height
```

In code:

```cpp
string name;
int age;
```

---

### 2️⃣ Methods (Functions / Behavior)

Actions the object can perform.

Example:

```
speak()
walk()
eat()
```

In code:

```cpp
void speak() {
    cout << "Hello" << endl;
}
```

---

# 7. The 4 Main Principles of OOP

## 1️⃣ Encapsulation

Group **data + functions** together in a class.

Example:

```cpp
class Person {
private:
    int age;

public:
    void setAge(int a) {
        age = a;
    }
};
```

Why?

To **protect data** 🔒

---

## 2️⃣ Abstraction

Show **important things**, hide **complex details**.

Example:

When you drive a car 🚗

You **use the steering wheel**, but you don't see the engine complexity.

In programming:

```
Person.speak()
```

You just call it.

---

## 3️⃣ Inheritance

A class can **inherit properties from another class**.

Example:

```
Person
   ↑
Student
```

Code:

```cpp
class Student : public Person {
public:
    int studentID;
};
```

Student **reuses Person features**.

---

## 4️⃣ Polymorphism

Same function **behaves differently**.

Example:

```
Person speak()
Robot speak()
```

Different behavior.

---

# 8. How We Convert Reality to Programming

Step-by-step method:

### Step 1: Identify objects

Example: **School system**

Objects:

```
Person
Student
Teacher
Course
```

---

### Step 2: Define attributes

Person:

```
name
age
address
```

---

### Step 3: Define behavior

```
talk()
walk()
sleep()
```

---

### Step 4: Build class

```cpp
class Person {
public:
    string name;
    int age;

    void walk();
};
```

---

# 9. Simple PlantUML Diagram

PlantUML class diagram:

```
@startuml

class Person {
  name : string
  age : int
  speak()
}

@enduml
```

Visualization:

```
+----------------+
|     Person     |
+----------------+
| name : string  |
| age  : int     |
+----------------+
| speak()        |
+----------------+
```

---

# 10. Simple Mental Model

Think like this:

```
Class = Blueprint 🧾
Object = Real thing 🧍
Attributes = Data 📊
Methods = Actions ⚙️
```

Example:

```
Class: Person

Object:
   person1
   person2
   person3
```

---

✅ **Summary**

| Concept | Meaning |
| --- | --- |
| Class | Blueprint |
| Object | Instance of class |
| Attribute | Data |
| Method | Function |
| Encapsulation | Protect data |
| Abstraction | Hide complexity |
| Inheritance | Reuse code |
| Polymorphism | Same action different behavior |

---