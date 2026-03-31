# Factory Method Pattern

👉 *problem → bad design → pain → solution → SOLID → UML*

---

# 🔴 The Problem (Why Factory Method exists)

Imagine you’re building a simple app that creates a parser that reads configuration files:

Supported formats:

- JSON
- XML
- CSV

### ❌ Naive approach (tight coupling)

```cpp
// Client (Bad Design)
class ParserFactory {
public:
    void createParser(string type) {
        if (type == "json")
            // parser = new JSONParser();
            parser = make_unique<JSONParser>();
        else if (type == "xml")
            parser = make_unique<XMLParser>();
        else if (type == "csv")
            parser = make_unique<CSVParser>();
        else
            parser =nullptr;
    }
    

    // Business logic
    void processFile(const std::string& filename) {
        parser->parse(filename);
    }

private:
    unique_ptr<Parser>  parser ;
};

```

### 🚨 Problems here:

1. **Tight coupling**
    - `ParserFactory` knows all concrete classes (`JSONParser`, `XMLParser`, etc.)
2. **Violates Open/Closed Principle**
    - Adding a new type (e.g., **`YAML`**) requires modifying existing code
3. **Hard to maintain & scale**
    - Logic becomes messy with many types
4. **Hard to test/extend**

---

# 💡 The Idea of Factory Method

👉 Instead of creating objects directly, **delegate object creation to subclasses**.

> “Let subclasses decide which object to create.”
> 

---

# 🧠 Structure

- `Product` → interface (Parser)
- `ConcreteProduct` → `JSONParser`, `XMLParser`
- `Creator` → declares factory method
- `ConcreteCreator` → implements factory method

---

# 🧠 New Example: File Parsing System

# 🧱 Full C++ Example

---

## Step 1: Product Interface

```cpp
#include <iostream>
#include <memory>

// Product
class Parser {
public:
    virtual ~Parser() = default;
    virtual void parse(const std::string& file) = 0;
};
```

---

## Step 2: Concrete Products

```cpp
class JSONParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing JSON file: " << file << std::endl;
    }
};

class XMLParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing XML file: " << file << std::endl;
    }
};

class CSVParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing CSV file: " << file << std::endl;
    }
};
```

---

## Step 3: Creator (Factory Base)

```cpp
class ParserFactory {
public:
    virtual ~ParserFactory() = default;

    // Factory Method
    virtual std::unique_ptr<Parser> createParser() const = 0;

    // Business logic
    void processFile(const std::string& filename) {
        auto parser = createParser();
        parser->parse(filename);
    }
};
```

---

## Step 4: Concrete Factories

```cpp
class JSONFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<JSONParser>();
    }
};

class XMLFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<XMLParser>();
    }
};

class CSVFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<CSVParser>();
    }
};
```

---

## Step 5: Main Function

```cpp
int main() {
    std::unique_ptr<ParserFactory> factory;

    int choice;
    std::cout << "Choose parser:\n";
    std::cout << "1 - JSON\n2 - XML\n3 - CSV\n";
    std::cin >> choice;

    switch (choice) {
        case 1:
            factory = std::make_unique<JSONFactory>();
            break;
        case 2:
            factory = std::make_unique<XMLFactory>();
            break;
        case 3:
            factory = std::make_unique<CSVFactory>();
            break;
        default:
            std::cout << "Invalid choice\n";
            return 1;
    }

    factory->processFile("data.txt");

    return 0;
}
```

---

# 🧩 PlantUML Diagram

```
@startuml

abstract class Parser {
    +parse(file)
}

class JSONParser
class XMLParser
class CSVParser

Parser <|-- JSONParser
Parser <|-- XMLParser
Parser <|-- CSVParser

abstract class ParserFactory {
    +createParser()
    +processFile()
}

class JSONFactory
class XMLFactory
class CSVFactory

ParserFactory <|-- JSONFactory
ParserFactory <|-- XMLFactory
ParserFactory <|-- CSVFactory

ParserFactory --> Parser : creates

@enduml
```

![image.png](Factory%20Method%20Pattern/image.png)

---

# 🎯 Key Benefits

### ✅ 1. Loose Coupling

`ParserFactory` doesn't know concrete classes anymore.

### ✅ 2. Open/Closed Principle

Add new parser types **without modifying existing code**.

### ✅ 3. Single Responsibility

Creation logic is separated.

---

# 🧩 Mental Model

Instead of:

> “I will create objects myself.”
> 

You say:

> “I will **ask someone else to create it for me.”**
> 

---

# Embedded Example:

### Real Embedded Scenario ⚙️

You’re building firmware that runs on different boards:

- Board A → uses **UART**
- Board B → uses **SPI**
- Board C → uses **CAN**

If you do:

```cpp
if (board == A) driver = new UARTDriver();
else if (board == B) driver = new SPIDriver();
```

👉 This logic spreads everywhere → messy, fragile, not scalable.

---

# 💡 2. The Idea of Factory Method

👉 **“Move object creation into a dedicated method (factory), and let subclasses decide what to create.”**

Instead of:

```cpp
new ConcreteProduct()
```

You do:

```cpp
createProduct()
```

And defer the decision to subclasses.

---

# 🧱 3. Structure

- **Product** → interface (abstract class)
- **ConcreteProduct** → actual implementations
- **Creator** → declares factory method
- **ConcreteCreator** → implements factory method

---

# 🔧 4. Full C++ Example (Embedded-style)

## Step 1: Product Interface

```cpp
#include <iostream>
#include <memory>

// Product
class Communication {
public:
    virtual ~Communication() = default;
    virtual void send(const std::string& data) = 0;
};
```

---

## Step 2: Concrete Products

```cpp
class UART : public Communication {
public:
    void send(const std::string& data) override {
        std::cout << "[UART] Sending: " << data << std::endl;
    }
};

class SPI : public Communication {
public:
    void send(const std::string& data) override {
        std::cout << "[SPI] Sending: " << data << std::endl;
    }
};

class CAN : public Communication {
public:
    void send(const std::string& data) override {
        std::cout << "[CAN] Sending: " << data << std::endl;
    }
};
```

---

## Step 3: Creator (Factory Base Class)

```cpp
class CommFactory {
public:
    virtual ~CommFactory() = default;

    // Factory Method
    virtual std::unique_ptr<Communication> create() const = 0;

    // Business logic using product
    void transmit(const std::string& msg) {
        auto comm = create();   // no knowledge of concrete type
        comm->send(msg);
    }
};
```

---

## Step 4: Concrete Factories

```cpp
class UARTFactory : public CommFactory {
public:
    std::unique_ptr<Communication> create() const override {
        return std::make_unique<UART>();
    }
};

class SPIFactory : public CommFactory {
public:
    std::unique_ptr<Communication> create() const override {
        return std::make_unique<SPI>();
    }
};

class CANFactory : public CommFactory {
public:
    std::unique_ptr<Communication> create() const override {
        return std::make_unique<CAN>();
    }
};
```

---

## Step 5: Main Function

```cpp
int main() {
    std::unique_ptr<CommFactory> factory;

    int choice;
    std::cout << "Select communication type:\n";
    std::cout << "1 - UART\n2 - SPI\n3 - CAN\n";
    std::cin >> choice;

    switch (choice) {
        case 1:
            factory = std::make_unique<UARTFactory>();
            break;
        case 2:
            factory = std::make_unique<SPIFactory>();
            break;
        case 3:
            factory = std::make_unique<CANFactory>();
            break;
        default:
            std::cout << "Invalid choice\n";
            return 1;
    }

    factory->transmit("Hello Embedded World");

    return 0;
}
```

---

# 🔍 5. What Did We Achieve?

### ✅ Before:

- Code depends on concrete classes
- Hard-coded object creation

### ✅ After:

- Decoupled creation from usage
- Easy to extend:

```cpp
class I2C : public Communication { ... };

class I2CFactory : public CommFactory {
    std::unique_ptr<Communication> create() const override {
        return std::make_unique<I2C>();
    }
};
```

👉 No existing code modified!

---

---

# 🧱 6. PlantUML Diagram

```
@startuml

abstract class Communication {
    +send(data)
}

class UART
class SPI
class CAN

Communication <|-- UART
Communication <|-- SPI
Communication <|-- CAN

abstract class CommFactory {
    +create()
    +transmit()
}

class UARTFactory
class SPIFactory
class CANFactory

CommFactory <|-- UARTFactory
CommFactory <|-- SPIFactory
CommFactory <|-- CANFactory

CommFactory --> Communication : creates

@enduml
```

![image.png](Factory%20Method%20Pattern/image%201.png)

---

# 🧠 Final Intuition

👉 Factory Method is about **who decides what object gets created**.

- ❌ Bad: "I decide inside my logic.”
- ✅ Good: "I delegate creation to a factory."

---

If you want, next step I can:

- Show **embedded-safe version (no heap, no RTTI)**
- Compare with **Abstract Factory vs Factory Method**
- Give **real AUTOSAR-like example** 🚗

# 🧠 When to Use Factory Method

Use it when:

- You don’t know **which class** you’ll need beforehand
- You want to **delegate creation to subclasses**
- You want to follow the o**pen/closed principle.**
- You have **many `if-else` or `switch` in object creation**

---

# ⚠️ Important Insight (Very Common Confusion)

Factory Method is **NOT about creating objects**.

👉 It’s about **moving the responsibility for object creation to subclasses**.

---

# the code

```cpp
using namespace std;
#include <iostream>
#include <memory>

// Product
class Parser {
public:
    virtual ~Parser() = default;
    virtual void parse(const std::string& file) = 0;
};

//Concrete Products
class JSONParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing JSON file: " << file << std::endl;
    }
};

class XMLParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing XML file: " << file << std::endl;
    }
};

class CSVParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing CSV file: " << file << std::endl;
    }
};

class TXTParser : public Parser {
public:
    void parse(const std::string& file) override {
        std::cout << "Parsing TXT file: " << file << std::endl;
    }
};

// Client (Bad Design)
class ParserFactory {
public:
    // void createParser(string type) {
    //     if (type == "json")
    //         // parser = new JSONParser();
    //         parser = make_unique<JSONParser>();
    //     else if (type == "xml")
    //         parser = make_unique<XMLParser>();
    //     else if (type == "csv")
    //         parser = make_unique<CSVParser>();
    //     else if (type == "txt")
    //         parser = make_unique<TXTParser>();
    //     else
    //         parser =nullptr;
    // }

    //method1: using object injection from main function 
    //but we need to create object at ParserFactory class
    // void createParser(unique_ptr<Parser>  p) {
    //     parser= std::move(p);
    // }
    //method2: factory method runtime polymorphism
    virtual unique_ptr<Parser> createParser()const=0;
    
    virtual ~ParserFactory() = default;

    // Business logic
    void processFile(const std::string& filename) {
        parser=createParser();//here we invoke the factory method
        parser->parse(filename);
    }

private:
    unique_ptr<Parser>  parser ;
};


class JSONFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<JSONParser>();
    }
};

class XMLFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<XMLParser>();
    }
};

class CSVFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<CSVParser>();
    }
};

class TXTFactory : public ParserFactory {
public:
    std::unique_ptr<Parser> createParser() const override {
        return std::make_unique<TXTParser>();
    }
};


int main() {
    // std::unique_ptr<ParserFactory> factory= make_unique<ParserFactory>();

    // factory->createParser("json");
    // factory->createParser(make_unique<JSONParser>());
    // factory->processFile("config.file");

    std::unique_ptr<ParserFactory> factory;

    int choice=4;
    std::cout << "Choose parser:\n";
    std::cout << "1 - JSON\n2 - XML\n3 - CSV\n";
    // std::cin >> choice;

    switch (choice) {
        case 1:
            factory = std::make_unique<JSONFactory>();
            break;
        case 2:
            factory = std::make_unique<XMLFactory>();
            break;
        case 3:
            factory = std::make_unique<CSVFactory>();
            break;
        case 4:
            factory = std::make_unique<TXTFactory>();
            break;
        default:
            std::cout << "Invalid choice\n";
            return 1;
    }
    std::cout << "---------------\n";
    factory->processFile("data.txt");

    return 0;
}

```