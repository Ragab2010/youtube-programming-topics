
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

## the Code That written in Video:
```cpp

class PaymentProcessOld {
public:
    void processPayment(const std::string& type) {
        if (type == "credit") {
            // credit logic
        }
        else if (type == "paypal") {
            // paypal logic
        }
        else if (type == "cash") {
            // paypal logic
        }

    }
};
solution:
class IPaymentMethod{
public :
    virtual ~IPaymentMethod()=default;
    virtual void pay()=0;
    //vptr-->vtable
};


class CreditProcess: public IPaymentMethod{
public:
    void pay() override{
        std::cout<<"credit logic"<<std::endl;
    }
};

class PaypalProcess: public IPaymentMethod{
public:
    void pay() override{
        std::cout<<"paypal logic"<<std::endl;
    }
};
//here open to extend
class CashProcess: public IPaymentMethod{
public:
    void pay() override{
        std::cout<<"Cash logic"<<std::endl;
    }
};

// runtime polymorphism , dynamic/runtime binding 
//there is an overhead on the processor (increase execution time )
// provide flexible interface from dynamic binding
class PaymentProcessRuntimePoly {
public:
    void process(IPaymentMethod &ref ) {
        // if (type == "credit") {
        //     // credit logic
        // }
        // else if (type == "paypal") {
        //     // paypal logic
        // }
        // else if (type == "cash") {
        //     // paypal logic
        // }
        ref.pay();

    }
};
// compile-time/static polymorphism , static/compile-time binding 
// no overhead   , increase the code size , 
//provide flexible come from template 
class PaymentProcessCompileTimePoly {
public:
template<typename T >
    void process(T &ref ) {
        // if (type == "credit") {
        //     // credit logic
        // }
        // else if (type == "paypal") {
        //     // paypal logic
        // }
        // else if (type == "cash") {
        //     // paypal logic
        // }
        ref.pay();

    }
};

int main(){
    // PaymentProcessor pp;
    // pp.processPayment("credit");
    PaymentProcessRuntimePoly runtimePayment;
    CreditProcess cp;
    PaypalProcess pp;
    std::cout<<"runtime-poly"<<std::endl;
    runtimePayment.process(cp);
    runtimePayment.process(pp);
    CashProcess cashp;
    runtimePayment.process(cashp);
    std::cout<<"compiletime-poly"<<std::endl;
    PaymentProcessCompileTimePoly compiletimePayment;
    compiletimePayment.process(cp);
    compiletimePayment.process(pp);
    compiletimePayment.process(cashp);


    return 0;
}
```