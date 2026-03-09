
# Inheritance vs Composition

```cpp

//inheritance vs composition 

//composition
//composed member variable without mName 
// strong coupling , mName
class PersonComp{
private:
//std::string is class
    std::string mName;//composition
public:
    PersonComp(std::string name):mName{name}{}

    void showName(){
        std::cout<<mName<<std::endl;
    }

};

// //inheritance
// //composed member variable without name 
// //very strong coupling , no name for mName
class PersonInhir:public std::string{
private:
//std::string is class

public:
    PersonInhir(std::string name):std::string{name}{}

    void showName(){
        // std::cout<<(*this)<<std::endl;
        std::cout<<static_cast<std::string>(*this)<<std::endl;
    }

};
int main() {
    PersonComp ahmed{"Ahmed"};
    ahmed.showName();
    PersonInhir ragab{"ragab"};
    ragab.showName();
    return 0;
  }

```