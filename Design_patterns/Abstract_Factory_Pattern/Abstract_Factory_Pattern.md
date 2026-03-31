# Abstract Factory Pattern

If the factory method is about **delegating creation to subclasses**, then:

> **Abstract Factory is about creating *families of related objects***.
> 

---

# 🔴 The Problem (Why Abstract Factory exists)

Let’s extend the previous idea.

Now you’re building a **cross-platform UI system**:

You support:

- Windows
- macOS

Each platform has its own UI components:

- Button
- Checkbox

---

## ❌ Naive approach (disaster incoming)

```cpp
class Button { /* ... */ };
class Checkbox { /* ... */ };

void renderUI(string os) {
    Button* btn;
    Checkbox* chk;

    if (os == "Windows") {
        btn = new WindowsButton();
        chk = new WindowsCheckbox();
    } else if (os == "Mac") {
        btn = new MacButton();
        chk = new MacCheckbox();
    }

    // use btn & chk
}
```

---

## 🚨 Problems

1. **Mixing families is easy (BUG!)**
    - You could accidentally do:
        
        ```cpp
        btn = new WindowsButton();
        chk = new MacCheckbox(); // 💥 inconsistent UI
        ```
        
2. **Tight coupling**
    - Code depends on all concrete classes
3. **Hard to extend**
    - Add Linux, → modify everything

---

# 💡 The Idea of Abstract Factory

👉 Provide an **interface for creating families of related objects**

👉 Ensure objects are **compatible with each other**

---

# 🧠 Structure

- `AbstractFactory` → creates multiple products
- `ConcreteFactory` → Windows, Mac
- `AbstractProduct` → Button, Checkbox
- `ConcreteProduct` → WindowsButton, MacButton, etc.

---

# ✅ Full C++ Example (with `main()`)

---

## 1. Abstract Products

```cpp
#include <iostream>
#include <memory>
using namespace std;

class Button {
public:
    virtual void paint() = 0;
    virtual ~Button() = default;
};

class Checkbox {
public:
    virtual void paint() = 0;
    virtual ~Checkbox() = default;
};
```

---

## 2. Concrete Products (Windows)

```cpp
class WindowsButton : public Button {
public:
    void paint() override {
        cout << "Rendering Windows Button\n";
    }
};

class WindowsCheckbox : public Checkbox {
public:
    void paint() override {
        cout << "Rendering Windows Checkbox\n";
    }
};
```

---

## 3. Concrete Products (Mac)

```cpp
class MacButton : public Button {
public:
    void paint() override {
        cout << "Rendering Mac Button\n";
    }
};

class MacCheckbox : public Checkbox {
public:
    void paint() override {
        cout << "Rendering Mac Checkbox\n";
    }
};
```

---

## 4. Abstract Factory

```cpp
class GUIFactory {
public:
    virtual unique_ptr<Button> createButton() = 0;
    virtual unique_ptr<Checkbox> createCheckbox() = 0;
    virtual ~GUIFactory() = default;
};
```

---

## 5. Concrete Factories

```cpp
class WindowsFactory : public GUIFactory {
public:
    unique_ptr<Button> createButton() override {
        return make_unique<WindowsButton>();
    }

    unique_ptr<Checkbox> createCheckbox() override {
        return make_unique<WindowsCheckbox>();
    }
};

class MacFactory : public GUIFactory {
public:
    unique_ptr<Button> createButton() override {
        return make_unique<MacButton>();
    }

    unique_ptr<Checkbox> createCheckbox() override {
        return make_unique<MacCheckbox>();
    }
};
```

---

## 6. Client Code

```cpp
class Application {
private:
    unique_ptr<Button> button;
    unique_ptr<Checkbox> checkbox;

public:
    Application(GUIFactory& factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    void render() {
        button->paint();
        checkbox->paint();
    }
};
```

---

## 7. Main Function

```cpp
int main() {
    unique_ptr<GUIFactory> factory;

    int choice;
    cout << "Choose OS:\n1. Windows\n2. Mac\n";
    cin >> choice;

    if (choice == 1) {
        factory = make_unique<WindowsFactory>();
    } else if (choice == 2) {
        factory = make_unique<MacFactory>();
    } else {
        cout << "Invalid choice\n";
        return 1;
    }

    Application app(*factory);
    app.render();

    return 0;
}
```

---

# 🎯 Key Benefits

### ✅ 1. Guarantees Compatibility

You’ll never mix Windows + Mac components.

### ✅ 2. Scalable

Add new platform → just add a new factory.

### ✅ 3. Clean Separation

Client code is independent of concrete classes.

---

# 🧠 Mental Model

Factory Method:

> “Let subclasses decide which object to create.”
> 

Abstract Factory:

> “Give me a **factory that creates a whole family of related objects**.”
> 

---

# ⚠️ VERY Important Difference

| Pattern | Focus |
| --- | --- |
| Factory Method | Create **one product** |
| Abstract Factory | Create **multiple related products** |

---

# 📊 PlantUML Diagram

```
@startuml

interface Button
interface Checkbox

class WindowsButton
class MacButton
class WindowsCheckbox
class MacCheckbox

Button <|-- WindowsButton
Button <|-- MacButton
Checkbox <|-- WindowsCheckbox
Checkbox <|-- MacCheckbox

interface GUIFactory {
    +createButton()
    +createCheckbox()
}

class WindowsFactory
class MacFactory

GUIFactory <|-- WindowsFactory
GUIFactory <|-- MacFactory

WindowsFactory --> WindowsButton
WindowsFactory --> WindowsCheckbox

MacFactory --> MacButton
MacFactory --> MacCheckbox

@enduml
```

![image.png](Abstract%20Factory%20Pattern/image.png)

---

# 🔥 When to Use Abstract Factory

Use it when:

- You need **families of related objects**
- You must enforce **consistency between objects**
- You want to switch entire systems (themes, OS, DB drivers)

---

# 💣 Common Mistake

If you only need **one object → use Factory Method**

If you need **a group of related objects → use Abstract Factory**

---

# online Codes:
## the code with Abstract Factory Pattern:

```cpp
#include <iostream>
#include <memory>
#include <string>
using namespace std;

// =====================
// 1. Abstract Products
// =====================

class Button {
public:
    virtual void paint() = 0;
    virtual ~Button() = default;
};

class Checkbox {
public:
    virtual void paint() = 0;
    virtual ~Checkbox() = default;
};

// =====================
// 2. Windows Products
// =====================

class WindowsButton : public Button {
public:
    void paint() override {
        cout << "Rendering Windows Button\n";
    }
};

class WindowsCheckbox : public Checkbox {
public:
    void paint() override {
        cout << "Rendering Windows Checkbox\n";
    }
};

// =====================
// 3. Mac Products
// =====================

class MacButton : public Button {
public:
    void paint() override {
        cout << "Rendering Mac Button\n";
    }
};

class MacCheckbox : public Checkbox {
public:
    void paint() override {
        cout << "Rendering Mac Checkbox\n";
    }
};
//add UI components for linux 
// =====================
// 4. Linux Products
// =====================

class LinuxButton : public Button {
public:
    void paint() override {
        cout << "Rendering Linux Button\n";
    }
};

class LinuxCheckbox : public Checkbox {
public:
    void paint() override {
        cout << "Rendering Linux Checkbox\n";
    }
};

// =====================
// 4. Abstract Factory
// =====================

class GUIFactory {
public:
    virtual unique_ptr<Button> createButton() = 0;
    virtual unique_ptr<Checkbox> createCheckbox() = 0;
    virtual ~GUIFactory() = default;
};

// =====================
// 5. Concrete Factories
// =====================

class WindowsFactory : public GUIFactory {
public:
    unique_ptr<Button> createButton() override {
        return make_unique<WindowsButton>();
    }

    unique_ptr<Checkbox> createCheckbox() override {
        return make_unique<WindowsCheckbox>();
    }
};

class MacFactory : public GUIFactory {
public:
    unique_ptr<Button> createButton() override {
        return make_unique<MacButton>();
    }

    unique_ptr<Checkbox> createCheckbox() override {
        return make_unique<MacCheckbox>();
    }
};

class LinuxFactory : public GUIFactory {
public:
    unique_ptr<Button> createButton() override {
        return make_unique<LinuxButton>();
    }

    unique_ptr<Checkbox> createCheckbox() override {
        return make_unique<LinuxCheckbox>();
    }
};
// =====================
// 6. Client
// =====================

class Application {
private:
    unique_ptr<Button> button;
    unique_ptr<Checkbox> checkbox;

public:
    // Dependency Injection of factory
    Application(GUIFactory& factory) {
        button = factory.createButton();
        checkbox = factory.createCheckbox();
    }

    void renderUI() {
        button->paint();
        checkbox->paint();
    }
};

// =====================
// 7. Main
// =====================

int main() {
    unique_ptr<GUIFactory> factory;
    string os;

    cout << "Enter OS (Windows / Mac): \n";
    // cin >> os;
    os="Linux";

    if (os == "Windows") {
        factory = make_unique<WindowsFactory>();
    } 
    else if (os == "Mac") {
        factory = make_unique<MacFactory>();
    } 
    else if (os == "Linux") {
        factory = make_unique<LinuxFactory>();
    } 
    else {
        cout << "Invalid OS\n";
        return 1;
    }
    cout << "-------------------\n";

    Application app(*factory);
    app.renderUI();

    return 0;
}
```