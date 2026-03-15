
# Apply solid principles to a course management system code

Use Case Diagram:

```cpp
@startuml
left to right direction

actor Admin
actor Instructor
actor Student

rectangle "Course_Management_System" {
:Student: ---> (Register)
:Student: ---> (login)
:Student: ---> (Browse Courses)
:Student: ---> (Enroll in course)
:Student: ---> (View Course Content)
:Student: ---> (Submit Assignment)
:Student: ---> (View Grades)
}
@enduml

```

![image.png](SOLID%20PRINCIPLES/image%205.png)

# 1️⃣ Full SOLID Code (with CLI + Registration)

```cpp
#include <iostream>
#include <vector>
#include <memory>
#include <string>
#include <algorithm>

//
// ============================
// 1️⃣ Interfaces (Abstractions)
// ============================
//

class IUser {
public:
    virtual ~IUser() = default;

    virtual bool login(const std::string& password) = 0;
    virtual void logout() = 0;

    virtual int getId() const = 0;
    virtual std::string getName() const = 0;
};

class ICourse {
public:
    virtual ~ICourse() = default;

    virtual std::string getTitle() const = 0;
    virtual void displayDescription() const = 0;
};

class IAssignment {
public:
    virtual ~IAssignment() = default;

    virtual void submit(int grade) = 0;
    virtual int getGrade() const = 0;
    virtual std::string getTitle() const = 0;
};

//
// ============================
// 2️⃣ Base Class: User
// ============================
//

class User : public IUser {
protected:
    int mId{};
    std::string mName;
    std::string mPassword;
    bool mLoggedIn{false};

public:
    User(int id, std::string name, std::string password)
        : mId{id}, mName{std::move(name)}, mPassword{std::move(password)} {}

    bool login(const std::string& password) override {
        if (password == mPassword) {
            mLoggedIn = true;
            return true;
        }
        return false;
    }

    void logout() override {
        mLoggedIn = false;
    }

    int getId() const override { return mId; }

    std::string getName() const override { return mName; }
};

//
// ============================
// 3️⃣ Assignment Implementation
// ============================
//

class Assignment : public IAssignment {
private:
    std::string mTitle;
    std::string mDescription;
    int mGrade{0};

public:
    Assignment(std::string title, std::string description)
        : mTitle(std::move(title)),
          mDescription(std::move(description)) {}

    void submit(int grade) override {
        mGrade = grade;
    }

    int getGrade() const override {
        return mGrade;
    }

    std::string getTitle() const override {
        return mTitle;
    }
};

//
// ============================
// 4️⃣ Course Implementation
// ============================
//

class Course : public ICourse {
private:
    std::string mTitle;
    std::string mDescription;

    std::vector<std::unique_ptr<IAssignment>> mAssignments;

public:
    Course(std::string title, std::string description)
        : mTitle(std::move(title)),
          mDescription(std::move(description)) {}

    void addAssignment(std::unique_ptr<IAssignment> assignment) {
        mAssignments.push_back(std::move(assignment));
    }

    const std::vector<std::unique_ptr<IAssignment>>&
    getAssignments() const {
        return mAssignments;
    }

    std::string getTitle() const override {
        return mTitle;
    }

    void displayDescription() const override {
        std::cout << "Course: " << mTitle << "\n"
                  << "Description: " << mDescription << "\n";
    }
};

//
// ============================
// 5️⃣ Student Implementation
// ============================
//

class Student : public User {
private:
    std::vector<std::shared_ptr<ICourse>> mCourses;

public:
    using User::User;

    void enrollCourse(const std::shared_ptr<ICourse>& course) {
        mCourses.push_back(course);
    }

    void removeCourse(const std::shared_ptr<ICourse>& course) {

        auto it = std::find(mCourses.begin(), mCourses.end(), course);

        if (it != mCourses.end())
            mCourses.erase(it);
    }

    void browseCourses(const std::vector<std::shared_ptr<ICourse>>& courses) const {

        for (const auto& c : courses)
            c->displayDescription();
    }

    void viewGrades() const {

        for (const auto& course : mCourses) {

            auto realCourse = std::dynamic_pointer_cast<Course>(course);

            if (!realCourse)
                continue;

            for (const auto& assignment : realCourse->getAssignments()) {

                std::cout << "Course: "
                          << realCourse->getTitle()
                          << " | Assignment: "
                          << assignment->getTitle()
                          << " | Grade: "
                          << assignment->getGrade()
                          << "\n";
            }
        }
    }
};

//
// ============================
// 6️⃣ CourseSystem (Director)
// ============================
//

class CourseSystem {
private:

    std::vector<std::shared_ptr<IUser>> mStudents;
    std::vector<std::shared_ptr<ICourse>> mCourses;

public:

    void registerStudent(const std::shared_ptr<IUser>& student) {
        mStudents.push_back(student);
    }

    void addCourse(const std::shared_ptr<ICourse>& course) {
        mCourses.push_back(course);
    }

    const std::vector<std::shared_ptr<ICourse>>&
    getAllCourses() const {
        return mCourses;
    }

    void browseCourses() const {

        for (const auto& c : mCourses)
            c->displayDescription();
    }
};

//
// ============================
// 7️⃣ Main
// ============================
//

int main() {

    CourseSystem system;

    auto cppCourse = std::make_shared<Course>(
        "C++ Fundamentals",
        "Learn basics of C++ programming."
    );

    auto oopCourse = std::make_shared<Course>(
        "Object-Oriented Programming",
        "Deep dive into OOP concepts."
    );

    system.addCourse(cppCourse);
    system.addCourse(oopCourse);

    auto a1 = std::make_unique<Assignment>(
        "Variables and Data Types",
        "Practice variables"
    );

    auto a2 = std::make_unique<Assignment>(
        "Classes and Objects",
        "Create a class"
    );

    cppCourse->addAssignment(std::move(a1));
    oopCourse->addAssignment(std::move(a2));

    auto student1 = std::make_shared<Student>(
        1, "Ahmed", "1234"
    );

    system.registerStudent(student1);

    std::cout << "\nLogin attempt\n";

    if (student1->login("1234"))
        std::cout << "Login successful\n";

    std::cout << "\nBrowse courses\n";
    student1->browseCourses(system.getAllCourses());

    student1->enrollCourse(cppCourse);

    cppCourse->getAssignments()[0]->submit(95);

    std::cout << "\nGrades\n";
    student1->viewGrades();

    student1->logout();
}
```

---

# 2️⃣ UML Diagram (PlantUML)

```
@startuml
left to right direction
skinparam classAttributeIconSize 0

'====================
' Interfaces
'====================

interface IUser{
    #mId : int
    #mName : string
    #mPassword : string
    #mLoggedIn : bool
    +login(password:string) : bool
    +logout() : void
    +getName() : string
    +getId() : int
}

interface IAssignment{
    #mTitle : string
    #mDescription : string
    #mGrade : int
    +submit(grade:int) : void
    +getGrade() : int
    +getTitle() : string
    +getDescription() : string
}

interface ICourse{
    +addAssignment(assignment:IAssignment)
    +displayDescription() : void
    +getTitle() : string
    +getAssignments() : vector<IAssignment>
}

'====================
' User hierarchy
'====================

class User{
    +login(password:string) : bool
    +logout() : void
    +getName() : string
    +getId() : int
}

class Student{
    -mCourses : vector<ICourse>
    +enrollCourse(course:ICourse)
    +removeCourse(course:ICourse)
    +browseCourses(allCourses:vector<ICourse>)
    +viewGrades()
    +getCourses() : vector<ICourse>
}

IUser <|.. User
User <|-- Student

'====================
' Assignment
'====================

class Assignment{
    +submit(grade:int)
    +getGrade() : int
    +getTitle() : string
    +getDescription() : string
}

IAssignment <|.. Assignment

'====================
' Course
'====================

class Course{
    -mTitle : string
    -mDescription : string
    -mAssignments : vector<IAssignment>
    +addAssignment(assignment:IAssignment)
    +displayDescription()
    +getTitle() : string
    +getAssignments()
}

ICourse <|.. Course

'====================
' System Director
'====================

class CourseSystem{
    -mStudents : vector<Student>
    -mCourses : vector<ICourse>
    +registerStudent(student:Student)
    +addCourse(course:ICourse)
    +getAllCourses() : vector<ICourse>
    +browseCourses()
    +findStudent(id:int) : Student
    +login(id:int,password:string) : Student
}

'====================
' CLI Layer
'====================

class CourseSystemCLI{
    -mSystem : CourseSystem
    -mCurrentStudent : Student
    +start()
    -registerStudent()
    -login()
    -studentMenu()
    -enrollCourse()
    -submitAssignment()
}

'====================
' Relationships
'====================

Student "1" o-- "*" ICourse : enrolls
Course "1" *-- "*" IAssignment : contains

CourseSystem "1" o-- "*" Student
CourseSystem "1" o-- "*" ICourse

CourseSystemCLI --> CourseSystem
CourseSystemCLI --> Student

@enduml
```

---

![image.png](SOLID%20PRINCIPLES/image%206.png)

## the Code That written in Video:
```cpp
#include <iostream>
#include <vector>
#include <memory>
#include <string>
#include <optional>
#include <algorithm>

/*
Requirements:
@startuml
left to right direction

actor Admin
actor Instructor
actor Student

rectangle "Course_Management_System" {
:Student: ---> (Register)
:Student: ---> (login)
:Student: ---> (Browse Courses)
:Student: ---> (Enroll in course)
:Student: ---> (View Course Content)
:Student: ---> (Submit Assignment)
:Student: ---> (View Grades)
}
@enduml

Apply SOLID PRINCIPLES:
1-Apply DIP and OCP to the class Relationships. 
2-Apply SRP to the classes. 
3-Apply ISP and LSP to classes.

Design :
static design:class digram
class--->noun , operation--->verbs
classes:Student , Course , Assignment , Grade , CourseSystem(director)
Relationships:
    --Student(high-level) has-a ICourse(abstraction) (Aggregation) one to many(DIP , OCP)
            VideoCourse -->implement--> ICourse
    --Course has-a  IAssignment (Compositon) one to many(DIP , OCP) , Assignment -->implement--> IAssignment
    --Assignment has-a Grade  (Compositon) one to one x
    --CourseSystem(high-level) has-a Student , ICourse(abstraction) (Aggregation)
        , we have one vector for Student only , not for all the actors Instructor ,Admin 
Student:
    --attributes(id:int , name:string , password:string ,courses : vector<course*>:(Aggregation) )
    --methods(login(password:string ) , logout ,EnrollInCourse , removeCourse , ViewCourseContent/getDe , 
                attribute(setter/getter) )
going to be:(SRP)
    ---IUser
    ----attributes/properties (id:int , name:string , password:string , LoggedIn:Bool)
    ----Methods/operations    (login:virtual  , logout:virutal  , attribute(setter/getter) )
    ---User : IUser <|-- inheritance (implement the IUser interface)
        ----attributes/properties (id:int , name:string , password:string , LoggedIn:Bool)
        ----Methods/operations    (login , logout , attribute(setter/getter) )
    ---Student : User  <|-- inheritance
        ----attributes/properties (courses:vector<ICourse*>:aggregation)
        ----Methods/operations    ( EnrollCourse , removeCourse  ,BrowseCourses , ViewCourseContent , attribute(setter/getter) )
-Course
    --attributes/properties (title:string , description:string , assignments:vector<Assignment>:composition)
    --Methods/operations    (addAssignment , SubmitAssignment, attribute(setter/getter))
going to be:
    ---ICourse
        ----attributes/properties (title:string , description:string)
        ----Methods/operations    ( attribute(setter/getter) )
    ---Course: ICourse<|-- inheritance (implement the ICourse interface)
        ----attributes/properties (assignments:vector<IAssignment>:composition)
        ----Methods/operations    ( attribute(setter/getter) )
-Assignment
    --attributes/properties (title:string , description:string, grades:vector<Grade>:composition/or grade:int)
    --Methods/operations    (submitGrade, getGrade , getTitle , attribute(setter/getter))
going to be:
    ---IAssignment
        ----attributes/properties (title:string , description:string)
        ----Methods/operations    ( attribute(setter/getter) )
    ---Assignment: IAssignment<|-- inheritance (implement the IAssignment interface)
        ----attributes/properties (grade:int)
        ----Methods/operations    ( attribute(setter/getter) )

-Grade
    --attributes/properties (title:string , description:string , value:int )
    --Methods/operations    (SubmitGrades , attribute(setter/getter))
-CourseSystem(director class)
    --attributes/properties (students:vector<Student*>:aggregation ,  Courses:vector<ICourse*>:aggregation)
    --Methods/operations    (Register , login , logout , addCourse , removeCourse, BrowseCourses , attribute(setter/getter) )    
going to be:
    ---ICourseSystem
        ----attributes/properties ( Courses:vector<ICourse*>:aggregation)
        ----Methods/operations    ( attribute(setter/getter) )
    ---CourseSystem: ICourseSystem<|-- inheritance (implement the ICourseSystem interface)
        ----attributes/properties (students:vector<Student*>:aggregation , instructors:vector<Instructor*>:aggregation,
                                        admins:vector<Admin*>:aggregation)
        ----Methods/operations    ( attribute(setter/getter) )
*/


// ============================
// Base Class: IUser (interface)
// ============================
class IUser {
protected:
    int mId{};
    std::string mName;
    std::string mPassword;
    bool mLoggedIn{false};

public:

    IUser(int id, std::string name, std::string password)
        : mId{id}, mName{std::move(name)}, mPassword{std::move(password)} {}

    virtual ~IUser() = default;

    virtual bool login(const std::string& password)=0;

    virtual void logout()=0;
    virtual std::string getName() const =0;
    virtual int getId() const =0;
};

// ============================
// Base Class: User
// ============================
class User : public IUser{

public:
using IUser::IUser;


    bool login(const std::string& password) override {
        if (password == mPassword) {
            mLoggedIn = true;
            return true;
        }
        return false;
    }

    void logout() override{
        mLoggedIn = false;
    }

    std::string getName() const override{ return mName; }
    int getId() const override{ return mId; }
};



// ============================
// Class: IAssignment
// ============================
class IAssignment {
protected:
    std::string mTitle;
    std::string mDescription;
    int mGrade{0};//important

public:
    IAssignment(std::string title, std::string description)
        : mTitle(std::move(title)), mDescription(std::move(description)) {}

    virtual ~IAssignment()=default;
    //important 
    virtual void submit(int grade) =0;

    virtual int getGrade() const =0;
    virtual std::string getTitle() const =0;
    virtual std::string getDescription() const =0;
};

// ============================
// Class: Assignment
// ============================
class Assignment: public IAssignment {

public:
using IAssignment::IAssignment;

    void submit(int grade) override {
        mGrade = grade;
    }

    int getGrade() const override { return mGrade; }

    std::string getTitle() const override { return mTitle; }
    std::string getDescription() const override { return mDescription; }
};


// ============================
// Class: ICourse
// ============================
class ICourse {
protected:
    std::string mTitle;
    std::string mDescription;

public:
    virtual ~ICourse()=default;
    ICourse(std::string title, std::string description)
        : mTitle(std::move(title)), mDescription(std::move(description)) {}

    virtual void addAssignment(std::unique_ptr<IAssignment> assignment)=0;
    virtual void displayDescription() const = 0;
    virtual std::string getTitle() const=0;
    virtual const std::vector<std::unique_ptr<IAssignment>>& getAssignments()const =0;
};
// ============================
// Class: Course
// ============================
class Course :public ICourse {
private:
    std::vector<std::unique_ptr<IAssignment>> mAssignments;

public:
using ICourse::ICourse;

    void addAssignment(std::unique_ptr<IAssignment> assignment) override{
        mAssignments.push_back(std::move(assignment));
    }

    void displayDescription() const override{
        std::cout << "Course: " << mTitle << "\n"
                  << "Description: " << mDescription << "\n";
    }

    std::string getTitle() const override{ return mTitle; }

    const std::vector<std::unique_ptr<IAssignment>>& getAssignments() const override{
        return mAssignments;
    }
};
// ============================
// Class: Student (inherits User)
// ============================
class Student : public User {
private:
    //depent on the ICourse
    std::vector<std::shared_ptr<ICourse>> mCourses;

public:
    using User::User; // inherit constructor

    void enrollCourse(const std::shared_ptr<ICourse>& course) {
        mCourses.push_back(course);
    }

    void removeCourse(const std::shared_ptr<ICourse>& course) {
        auto it = std::find(mCourses.begin(), mCourses.end(), course);
        if (it != mCourses.end()) {
            mCourses.erase(it);
        }
    }

    void browseCourses(const std::vector<std::shared_ptr<ICourse>>& allCourses) const {
        for (const auto& course : allCourses) {
            course->displayDescription();
        }
    }

    void viewGrades() const {
        for (const auto& course : mCourses) {
            for (const auto& assignment : course->getAssignments()) {
                std::cout << "Course: " << course->getTitle()
                          << ", Assignment: " << assignment->getTitle()
                          << ", Grade: " << assignment->getGrade() << "\n";
            }
        }
    }
    const std::vector<std::shared_ptr<ICourse>>& getCourses() const {
        return mCourses;
    }

};

// ============================
// Class: CourseSystem (Director)
// Aggregation of Students & Courses
// ============================
class CourseSystem {
private:
    std::vector<std::shared_ptr<Student>> mStudents;
    std::vector<std::shared_ptr<ICourse>> mCourses;//depent on the ICourse

public:
    void registerStudent(const std::shared_ptr<Student>& student) {
        mStudents.push_back(student);
    }

    void addCourse(const std::shared_ptr<ICourse>& course) {
        mCourses.push_back(course);
    }

    const std::vector<std::shared_ptr<ICourse>>& getAllCourses() const {
        return mCourses;
    }

    void browseCourses() const {
        for (const auto& course : mCourses) {
            course->displayDescription();
        }
    }
    std::shared_ptr<Student> findStudent(int id) {

        for (auto& s : mStudents) {
            if (s->getId() == id)
                return s;
        }

        return nullptr;
    }

    std::shared_ptr<Student> login(int id, const std::string& password) {

        auto student = findStudent(id);

        if (student && student->login(password))
            return student;

        return nullptr;
    }
    
    
};

// ============================
// Class: VideoCourse
// ============================
class VideoCourse :public ICourse {
private:
    //inject VideoAssignment instances 
    std::vector<std::unique_ptr<IAssignment>> mAssignments;

public:
using ICourse::ICourse;

    void addAssignment(std::unique_ptr<IAssignment> assignment) override{
        mAssignments.push_back(std::move(assignment));
    }

    void displayDescription() const override{
        std::cout << "Course: " << mTitle << "\n"
                  << "Description: " << mDescription << "\n";
    }

    std::string getTitle() const override{ return mTitle; }

    const std::vector<std::unique_ptr<IAssignment>>& getAssignments() const override{
        return mAssignments;
    }
};


// ============================
// Class: VideoAssignment
// ============================
class VideoAssignment: public IAssignment {

public:
using IAssignment::IAssignment;

    void submit(int grade) override {
        mGrade = grade;
    }

    int getGrade() const override { return mGrade; }

    std::string getTitle() const override { return mTitle; }
    std::string getDescription() const override { return mDescription; }
};

// ============================
// CLI
// ============================
class CourseSystemCLI {

private:
    CourseSystem& mSystem;
    std::shared_ptr<Student> mCurrentStudent{nullptr};

public:

    CourseSystemCLI(CourseSystem& system)
        : mSystem(system) {}

    void start() {

        int choice;

        while (true) {

            std::cout << "\n=== Course System ===\n";
            std::cout << "1 Register\n";
            std::cout << "2 Login\n";
            std::cout << "3 Exit\n";

            std::cin >> choice;

            if (choice == 1)
                registerStudent();
            else if (choice == 2)
                login();
            else
                return;
        }
    }

private:

    void registerStudent() {

        int id;
        std::string name;
        std::string password;

        std::cout << "ID: ";
        std::cin >> id;

        std::cout << "Name: ";
        std::cin >> name;

        std::cout << "Password: ";
        std::cin >> password;

        auto student = std::make_shared<Student>(id, name, password);

        mSystem.registerStudent(student);

        std::cout << "Registration successful\n";
    }

    void login() {

        int id;
        std::string password;

        std::cout << "ID: ";
        std::cin >> id;

        std::cout << "Password: ";
        std::cin >> password;

        mCurrentStudent = mSystem.login(id, password);

        if (!mCurrentStudent) {
            std::cout << "Login failed\n";
            return;
        }

        std::cout << "Login successful\n";

        studentMenu();
    }

    void studentMenu() {

        int choice;

        while (true) {

            std::cout << "\n=== Student Menu ===\n";
            std::cout << "1 Browse Courses\n";
            std::cout << "2 Enroll Course\n";
            std::cout << "3 Submit Assignment\n";
            std::cout << "4 View Grades\n";
            std::cout << "5 Logout\n";

            std::cin >> choice;

            if (choice == 1)
                mCurrentStudent->browseCourses(mSystem.getAllCourses());

            else if (choice == 2)
                enrollCourse();

            else if (choice == 3)
                submitAssignment();

            else if (choice == 4)
                mCurrentStudent->viewGrades();

            else
                return;
        }
    }

    void enrollCourse() {

        auto& courses = mSystem.getAllCourses();

        int index;

        mCurrentStudent->browseCourses(courses);

        std::cout << "Select course: ";
        std::cin >> index;

        if (index > 0 && index <= courses.size()) {

            mCurrentStudent->enrollCourse(courses[index - 1]);

            std::cout << "Course enrolled\n";
        }
    }

    void submitAssignment() {

        auto& courses = mCurrentStudent->getCourses();

        if (courses.empty()) {
            std::cout << "No enrolled courses\n";
            return;
        }

        int cIndex;

        for (size_t i = 0; i < courses.size(); ++i)
            std::cout << i + 1 << ". " << courses[i]->getTitle() << "\n";

        std::cin >> cIndex;

        auto& assignments = courses[cIndex - 1]->getAssignments();

        for (size_t i = 0; i < assignments.size(); ++i)
            std::cout << i + 1 << ". " << assignments[i]->getTitle() << "\n";

        int aIndex;
        std::cin >> aIndex;

        int grade;

        std::cout << "Enter grade: ";
        std::cin >> grade;

        assignments[aIndex - 1]->submit(grade);
    }
};

int main() {
    // // ============================
    // // Create Course System
    // // ============================
    // CourseSystem system;

    // // ============================
    // // Create Courses
    // // ============================
    // auto cppCourse = std::make_shared<Course>(
    //     "C++ Fundamentals",
    //     "Learn basics of C++ programming."
    // );

    // auto oopCourse = std::make_shared<Course>(
    //     "Object-Oriented Programming",
    //     "Deep dive into OOP concepts."
    // );
    // //
    // auto cppVideoCourse = std::make_shared<VideoCourse>(
    //     "Video: C++ Fundamentals",
    //     "Video: Learn basics of C++ programming."
    // );

    // system.addCourse(cppCourse);
    // system.addCourse(oopCourse);
    // system.addCourse(cppVideoCourse);

    // // ============================
    // // Create Assignments
    // // ============================
    // auto a1 = std::make_unique<Assignment>(
    //     "Variables and Data Types",
    //     "Practice variables in C++"
    // );

    // auto a2 = std::make_unique<Assignment>(
    //     "Classes and Objects",
    //     "Create your first class"
    // );
    // auto videoAssignment = std::make_unique<VideoAssignment>(
    //     "Video: Classes and Objects",
    //     "Video: Create your first class"
    // );

    // cppCourse->addAssignment(std::move(a1));
    // oopCourse->addAssignment(std::move(a2));
    // cppVideoCourse->addAssignment(std::move(videoAssignment));

    // // ============================
    // // Register Student
    // // ============================
    // auto student1 = std::make_shared<Student>(
    //     1, "Ahmed", "1234"
    // );

    // system.registerStudent(student1);

    // // ============================
    // // Case 1: Failed Login
    // // ============================
    // std::cout << "\n--- Attempt Login (Wrong Password) ---\n";
    // if (!student1->login("wrong_pass")) {
    //     std::cout << "Login failed!\n";
    // }

    // // ============================
    // // Case 2: Successful Login
    // // ============================
    // std::cout << "\n--- Attempt Login (Correct Password) ---\n";
    // if (student1->login("1234")) {
    //     std::cout << "Login successful!\n";
    // }

    // // ============================
    // // Case 3: Browse All Courses
    // // ============================
    // std::cout << "\n--- Browse All Courses ---\n";
    // student1->browseCourses(system.getAllCourses());

    // // ============================
    // // Case 4: Enroll in Course
    // // ============================
    // std::cout << "\n--- Enroll in C++ Course ---\n";
    // student1->enrollCourse(cppCourse);

    // // ============================
    // // Case 5: Submit Assignment
    // // ============================
    // std::cout << "\n--- Submit Assignment ---\n";
    // // a1->submit(95);  // student gets grade 95
    // cppCourse->getAssignments()[0]->submit(95);

    // // ============================
    // // Case 6: View Grades
    // // ============================
    // std::cout << "\n--- View Grades ---\n";
    // student1->viewGrades();

    // // ============================
    // // Case 7: Enroll in Another Course
    // // ============================
    // std::cout << "\n--- Enroll in OOP Course ---\n";
    // student1->enrollCourse(oopCourse);

    // // a2->submit(88);  // grade for second course
    // oopCourse->getAssignments()[0]->submit(88);

    // std::cout << "\n--- View Grades Again ---\n";
    // student1->viewGrades();

    // // ============================
    // // Case 8: Logout
    // // ============================
    // std::cout << "\n--- Logout ---\n";
    // student1->logout();
    // std::cout << "Student logged out.\n";

    /*******************************/
      // ============================
    // Create Course System
    // ============================
    CourseSystem system;

    // ============================
    // Create Courses
    // ============================
    auto cppCourse = std::make_shared<Course>(
        "C++ Fundamentals",
        "Learn basics of C++ programming."
    );

    auto oopCourse = std::make_shared<Course>(
        "Object-Oriented Programming",
        "Deep dive into OOP concepts."
    );
    //
    auto cppVideoCourse = std::make_shared<VideoCourse>(
        "Video: C++ Fundamentals",
        "Video: Learn basics of C++ programming."
    );

    system.addCourse(cppCourse);
    system.addCourse(oopCourse);
    system.addCourse(cppVideoCourse);

    // ============================
    // Create Assignments
    // ============================
    auto a1 = std::make_unique<Assignment>(
        "Variables and Data Types",
        "Practice variables in C++"
    );

    auto a2 = std::make_unique<Assignment>(
        "Classes and Objects",
        "Create your first class"
    );
    auto videoAssignment = std::make_unique<VideoAssignment>(
        "Video: Classes and Objects",
        "Video: Create your first class"
    );

    cppCourse->addAssignment(std::move(a1));
    oopCourse->addAssignment(std::move(a2));
    cppVideoCourse->addAssignment(std::move(videoAssignment));

    CourseSystemCLI cli(system);
    cli.start();

    return 0;

}

```