
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

## the Code That written in Video:
```cpp

class IShape{
protected:
    int width;
    int height;
public:
    IShape(int w , int h):width{w} , height{h}{}
    virtual ~IShape()=default;
    virtual int area()const =0;
};
//base class
class Rectangle :public IShape {
public:
    Rectangle(int w , int h):IShape{w , h}{}
    // void setWidth(int w) { width = w; }
    // void setHeight(int h) { height = h; }

    int area() const { return width * height; }
};

class Square : public IShape {
public:
    Square(int w ):IShape{w , w}{}

    // void setWidth(int w) override {
    //     width = height = w;
    // }

    // void setHeight(int h) override {
    //     width = height = h;
    // }
    int area() const { return width * height; }

};

void processArea(IShape& rect) {
    // rect.setWidth(5);
    // rect.setHeight(4);
    // std::cout << rect.getArea()<<std::endl;
    std::cout << rect.area()<<std::endl;
}


int main(){
    Rectangle rect(5,4);
    Square    square(4);
    processArea(rect);//5*4=20
    processArea(square);//4*4=16

    return 0;
}
```