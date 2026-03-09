# Three approaches to polymorphism

# 1️⃣ Function Overloading Approach

## Full Code

```cpp
#include <iostream>
#include <string>

class Teacher{
public:
    std::string mName;

    Teacher(std::string name) : mName{name} {}

    void work(){
        std::cout << "Teacher: " << mName << std::endl;
    }
};

class Engineer{
public:
    std::string mName;

    Engineer(std::string name) : mName{name} {}

    void work(){
        std::cout << "Engineer: " << mName << std::endl;
    }
};

void do_work(Teacher& ref){
    ref.work();
}

void do_work(Engineer& ref){
    ref.work();
}

int main(){

    Teacher ahmed{"Ahmed"};
    Engineer ramy{"Ramy"};

    do_work(ahmed);
    do_work(ramy);

    return 0;
}
```

## Pros

- ⚡ **Very fast** because the function is resolved at **compile time**.
- No **virtual table (vtable)** or **virtual pointer (vptr)**.
- No **runtime overhead**.
- Simple and easy to understand.

## Cons

- You must write a **new function for every new class**.
- **Code duplication** increases as the number of classes grows.
- Poor **scalability**.
- Cannot easily store different types in a single container.

Example problem:

```cpp
std::vector<?> people;
```

---

# 2️⃣ Runtime Polymorphism (Virtual Functions)

## Full Code

```cpp
#include <iostream>
#include <string>

class Person{
public:
    std::string mName;

    Person(std::string name) : mName{name} {}

    virtual void work(){
        std::cout << "Person: " << mName << std::endl;
    }

    virtual ~Person() = default;
};

class Teacher : public Person{
public:
    Teacher(std::string name) : Person{name} {}

    void work() override{
        std::cout << "Teacher: " << mName << std::endl;
    }
};

class Engineer : public Person{
public:
    Engineer(std::string name) : Person{name} {}

    void work() override{
        std::cout << "Engineer: " << mName << std::endl;
    }
};

void do_work(Person& ref){
    ref.work();
}

int main(){

    Teacher ahmed{"Ahmed"};
    Engineer ramy{"Ramy"};

    do_work(ahmed);
    do_work(ramy);

    return 0;
}
```

## Pros

- 🧠 **Very flexible**.
- Allows using a **base class pointer/reference**.
- You can store different types in the same container.

Example:

```cpp
std::vector<Person*> people;
```

- Easy to **extend the system** with new classes.
- Commonly used in **large software architectures** and **design patterns**.

## Cons

- 🐢 **Slower than compile-time polymorphism** because of:
    - Virtual dispatch
    - vtable lookup
- Each object contains a **vptr**, which slightly increases memory usage.
- Some compiler optimizations like **inlining** may not be applied.

---

# 3️⃣ Static Polymorphism (Template-Based)

## Full Code

```cpp
#include <iostream>
#include <string>

template<typename T>
class Person : public T{
public:

    using T::T;

    void work(){
        T::work();
    }
};

class Teacher{
public:
    std::string mName;

    Teacher(std::string name) : mName{name} {}

    void work(){
        std::cout << "Teacher: " << mName << std::endl;
    }
};

class Engineer{
public:
    std::string mName;

    Engineer(std::string name) : mName{name} {}

    void work(){
        std::cout << "Engineer: " << mName << std::endl;
    }
};

template<typename T>
void do_work(T& ref){
    ref.work();
}

int main(){

    Person<Teacher> ahmed{"Ahmed"};
    Person<Engineer> ramy{"Ramy"};

    do_work(ahmed);
    do_work(ramy);

    return 0;
}
```

---

## Pros

- ⚡ **Very fast** because everything happens at **compile time**.
- No **virtual function overhead**.
- Allows **compiler optimizations** like inlining.
- No **vptr** or **vtable**.
- Excellent for **high-performance systems**.

---

## Cons

- 📈 **Code bloat** (the compiler generates a new version for each type).
- Template error messages can be **hard to read**.
- Less flexible than runtime polymorphism.
- Difficult to store different types in the same container.

Example issue:

```cpp
std::vector<Person<?>>
```

---

# 📊 Quick Comparison

| Feature | Function Overloading | Runtime Polymorphism | Static Polymorphism |
| --- | --- | --- | --- |
| Resolution Time | Compile Time | Runtime | Compile Time |
| Speed | ⚡ Fast | 🐢 Slower | ⚡ Fast |
| Flexibility | Low | ⭐ High | Medium |
| Memory Overhead | None | vptr | None |
| Code Size | Medium | Small | Large |

---

✅ **Practical rule used by many engineers:**

- **Small programs** → Function Overloading
- **Large object-oriented systems** → Runtime Polymorphism
- **Performance-critical systems (game engines, HPC)** → Static Polymorphism

---

If you want, I can also show you the **4th and most advanced technique in C++ polymorphism: CRTP (Curiously Recurring Template Pattern)**, which is heavily used in **STL, Boost, and game engines**.

Great question — adding **CRTP** will complete the picture because it’s one of the most powerful **compile-time polymorphism** techniques in C++. 🚀

I'll add it as a **4th approach** consistent with your examples.

---

# 4️⃣ CRTP (Curiously Recurring Template Pattern)

CRTP is a **static polymorphism technique** where a class **inherits from a template that takes the derived class as a parameter**.

Pattern:

```cpp
template<typename Derived>
class Base {};
```

and the derived class passes **itself**:

```cpp
class Derived : public Base<Derived> {};
```

This allows the base class to **call functions from the derived class without virtual functions**.

---

# Full Code

```cpp
#include <iostream>
#include <string>

template<typename Derived>
class Person{
public:

    void work(){
        // static_cast converts this to the real derived type
        static_cast<Derived*>(this)->work_impl();
    }

    std::string getName(){
        return static_cast<Derived*>(this)->mName;
    }
};

class Teacher : public Person<Teacher>{
public:
    std::string mName;

    Teacher(std::string name) : mName{name} {}

    void work_impl(){
        std::cout << "Teacher: " << mName << std::endl;
    }
};

class Engineer : public Person<Engineer>{
public:
    std::string mName;

    Engineer(std::string name) : mName{name} {}

    void work_impl(){
        std::cout << "Engineer: " << mName << std::endl;
    }
};

template<typename T>
void do_work(Person<T>& ref){
    ref.work();
}

int main(){

    Teacher ahmed{"Ahmed"};
    Engineer ramy{"Ramy"};

    do_work(ahmed);
    do_work(ramy);

    return 0;
}
```

---

# How CRTP Works

Key line:

```cpp
static_cast<Derived*>(this)->work_impl();
```

Explanation:

1. `Person<Derived>` **does not know the real type**
2. The **Derived type is passed as a template parameter**
3. We **cast `this` to the derived type**
4. Then call a **derived implementation**

So the compiler generates:

```
Person<Teacher>
Person<Engineer>
```

Each version **directly calls the correct function**.

No virtual dispatch. ⚡

---

# CRTP Memory Layout

CRTP has **no runtime cost**.

Object layout:

```
Teacher
 └─ Person<Teacher>  (empty)
 └─ mName
```

No:

- ❌ vtable
- ❌ vptr
- ❌ dynamic dispatch

---

# Pros

⚡ **Zero runtime overhead**

- No virtual calls
- No vtable
- No vptr

🧠 **Compile-time polymorphism**

🚀 **Very fast**

- Fully inlineable
- Used in performance libraries

📦 Used in major libraries like:

- STL iterators
- Boost
- Eigen
- game engines

Example real pattern:

```cpp
template<class Derived>
class VectorBase
```

(Eigen library)

---

# Cons

❌ **More complex syntax**

CRTP is harder to understand than normal inheritance.

---

❌ **Error messages can be ugly**

Template errors can become very long.

---

❌ **No runtime polymorphism**

You cannot do this:

```cpp
std::vector<Person<?>>
```

because each `Person<Derived>` is a different type.

---

# 📊 Updated Comparison Table

| Feature | Overloading | Runtime Polymorphism | Templates | CRTP |
| --- | --- | --- | --- | --- |
| Resolution | Compile Time | Runtime | Compile Time | Compile Time |
| Speed | ⚡ Fast | 🐢 Slower | ⚡ Fast | ⚡ Fastest |
| Flexibility | Low | ⭐ Very High | Medium | Medium |
| Memory Overhead | None | vptr | None | None |
| Code Size | Medium | Small | Large | Large |
| Complexity | Easy | Easy | Medium | Hard |

---

# When Engineers Use CRTP

CRTP is used when you want:

- **polymorphism without virtual overhead**
- **compile-time guarantees**
- **maximum performance**

Typical domains:

🎮 Game Engines

📊 Linear Algebra libraries (Eigen)

⚡ High-performance frameworks

🧩 Template libraries

---

💡 **Senior C++ insight**

A common rule:

```
Runtime polymorphism → flexibility
CRTP → performance
```

Large C++ codebases often **mix both**.

Example:

```
High level API → virtual
Low level math → CRTP
```

---

```cpp

//Polymorphism

/*
approch 1: classes with overloaded functions
## Pros
understand.
- simple and easy to understand 
## Cons

- You must write a **new function for every new class**.
- **Code duplication** increases as the number of classes grows.
- Poor **scalability**.
- Cannot easily store different types in a single container.
- increase code size 

*/
/*
class Teacher{
    std::string mName;
public:
    Teacher(std::string name):mName{name}{}
    void work(){
        std::cout<<"Teacher: "<<mName<<std::endl;
    }
};

class Engineer{
    std::string mName;
public:
    Engineer(std::string name):mName{name}{}
    void work(){
        std::cout<<"Engineer: "<<mName<<std::endl;
    }
};
/***********************************************/
/*
class Student{
    std::string mName;
public:
    Student(std::string name):mName{name}{}
    void work(){
        std::cout<<"Student: "<<mName<<std::endl;
    }
};

void do_work(Teacher& ref){
    ref.work();
}

void do_work(Engineer& ref){
    ref.work();
}
void do_work(Student& ref){
    ref.work();
}
template
template<typename T>
void do_work(T && ref){
    ref.work();
}
*/
/**************************************************/

/**************************************************/

/*
approch 2: runtime polymorphism , dynamic/runtime binding
## Pros
-very flexible 
-can behave diff

## Cons:
-slow than approch 1(virtual dispatch )
- Some compiler optimizations like inlining may not be applied.

*/

/*
class Person{
protected:
    std::string mName;
public:
    Person(std::string name):mName{name}{}
    virtual ~Person()=default;
    virtual void work(){
        std::cout<<"Person: "<<mName<<std::endl;
    }
    //vptr --> vtable
};

class Teacher:public Person{
public:
using Person::Person;
    void work(){
        std::cout<<"Teacher: "<<mName<<std::endl;
    }
};

class Engineer:public Person{
public:
using Person::Person;
    void work(){
        std::cout<<"Engineer: "<<mName<<std::endl;
    }
};

class Student:public Person{
public:
using Person::Person;
    void work(){
        std::cout<<"Student: "<<mName<<std::endl;
    }
};

void do_work(Person& ref){
    ref.work();
}
/**************************************************/

/**************************************************/

/*
approch 3: static polymorphism ,CRTP (Curiously Recurring Template Pattern) , static/compile-time binding
## Pros
⚡ **Zero runtime overhead**

- No virtual calls
- No vtable
- No vptr
-more flexible than approch 1

🧠 **Compile-time polymorphism**
## Cons:
-increase code size
-complex syntax

*/
/*
template<typename T>
class Person{
protected:
    std::string mName;
public:
    Person(std::string name):mName{name}{}
    void work(){
        // std::cout<<"Person: "<<mName<<std::endl;
        static_cast<T*>(this)->work();
        /*for Teacher class:
            static_cast<Teacher*>(this)->work();
        */
    }

};

class Teacher:public Person<Teacher>{
public:
using Person::Person;
    void work(){
        std::cout<<"Teacher: "<<mName<<std::endl;
    }
};

class Engineer:public Person<Engineer>{
public:
using Person::Person;
    void work(){
        std::cout<<"Engineer: "<<mName<<std::endl;
    }
};

class Student:public Person<Student>{
public:
using Person::Person;
    void work(){
        std::cout<<"Student: "<<mName<<std::endl;
    }
};

template<typename T>
void do_work(Person<T>& ref){
    ref.work();
}
*/
/**************************************************/
/**************************************************/

class Person{
protected:
    std::string mName;
public:
    Person(std::string name):mName{name}{}
    virtual ~Person()=default;
    virtual void work(){
        std::cout<<"Person: "<<mName<<std::endl;
    }
    //vptr --> vtable
};

class Teacher:public Person{
public:
using Person::Person;
    void work(){
        std::cout<<"Teacher: "<<mName<<std::endl;
    }
};

class Engineer:public Person{
public:
using Person::Person;
    void work(){
        std::cout<<"Engineer: "<<mName<<std::endl;
    }
};

class Student:public Person{
public:
using Person::Person;
    void work(){
        std::cout<<"Student: "<<mName<<std::endl;
    }
};

void do_work(Person& ref){
    ref.work();
}

*/

int main() {

        Teacher ahmed{"Ahmed"};
        Engineer ramy{"Ramy"};
        do_work(ahmed);
        do_work(ramy);
        
        /*
        std::vector<Person*> men;
        Teacher ahmed{"Ahmed"};
        Engineer ramy{"Ramy"};
        Student samy{"samy"};

        //store
        men.push_back(&ahmed);
        men.push_back(&ramy);
        men.push_back(&samy);

        for(auto & person : men){
            person->work();
        }
        */

    return 0;
}
```