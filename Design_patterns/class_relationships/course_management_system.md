
Example:

## Design a course management system:

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

![image.png](Class_Relationships/image%2011.png)

---

# ✅ UML Mapping Explanation

```
@startuml
title  Course Management System

' =========================
' Base Class
' =========================
class User {
    - mId : int
    - mName : string
    - mPassword : string
    - mLoggedIn : bool
    + login(password : string) : bool
    + logout() : void
    + getName() : string
    + getId() : int
}

' =========================
' Student Class
' =========================
class Student {
    - mCourses : vector<Course*>
    + enrollCourse(course : Course) : void
    + removeCourse(course : Course) : void
    + browseCourses(courses : vector<Course>) : void
    + viewGrades() : void
}

' =========================
' Course Class
' =========================
class Course {
    - mTitle : string
    - mDescription : string
    - mAssignments : vector<Assignment>
    + addAssignment(a : Assignment) : void
    + displayDescription() : void
    + getTitle() : string
    + getAssignments() : vector<Assignment>
}

' =========================
' Assignment Class
' =========================
class Assignment {
    - mTitle : string
    - mDescription : string
    - mGrade : int
    + submit(grade : int) : void
    + getGrade() : int
    + getTitle() : string
}

' =========================
' CourseSystem Class
' =========================
class CourseSystem {
    - mStudents : vector<Student*>
    - mCourses : vector<Course*>
    + registerStudent(s : Student) : void
    + addCourse(c : Course) : void
    + getAllCourses() : vector<Course>
    + browseCourses() : void
}

' =========================
' Relationships
' =========================

' Inheritance
User <|-- Student

' Aggregation (CourseSystem manages but does not own strictly)
CourseSystem o-- Student
CourseSystem o-- Course

' Composition (Course owns Assignments)
Course *-- Assignment
Student o--Course

' Association (Student enrolls in Course)

@enduml
```

![image.png](Class_Relationships/image%2012.png)

### 🔹 Inheritance

```
User <|--- Student
```

### 🔹 Aggregation

```
CourseSystem o---- Student
CourseSystem o---- Course
```

### 🔹 Composition

```
Course *---- Assignment
```

---

# ✅ Code Implementation

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

Design :
static design:class digram
class--->noun , operation--->verbs
classes:Student , Course , Assignment , Grade , CourseSystem(director)
Relationships:
    --Student has-a course (Aggregation) one to many
    --Course has-a  Assignment (Compositon) one to many
    --Assignment has-a Grade  (Compositon) one to one 
    --CourseSystem has-a Student , Course (Aggregation)
Student:
    --attributes(id:int , name:string , password:string ,courses : vector<course*>:(Aggregation) )
    --methods(login(password:string ) , logout ,EnrollInCourse , removeCourse , ViewCourseContent/getDe , 
                attribute(setter/getter) )
    ---IUser
        ----attributes/properties (id:int , name:string , password:string , LoggedIn:Bool)
        ----Methods/operations    (login , logout , attribute(setter/getter) )
    ---Student : User  <|-- inheritance
        ----attributes/properties (courses:vector<*Course>:aggregation)
        ----Methods/operations    ( EnrollCourse , removeCourse  ,BrowseCourses , ViewCourseContent , attribute(setter/getter) )
-Course
    --attributes/properties (title:string , description:string , assignment:vector<Assignment>:composition)
    --Methods/operations    (addAssignment , SubmitAssignment, attribute(setter/getter))
    ---ICourse
        ----attributes/properties ()
        ----Methods/operations    ( )
    ---VideosCourse: ICourse<|-- inheritance
        ----attributes/properties ()
        ----Methods/operations    ( )
-Assignment
    --attributes/properties (title:string , description:string, grades:vector<Grade>:composition/or grade:int)
    --Methods/operations    (submitGrade, getGrade , getTitle , attribute(setter/getter))
-Grade
    --attributes/properties (title:string , description:string , value:int )
    --Methods/operations    (SubmitGrades , attribute(setter/getter))
-courseSystem(director class)
    --attributes/properties (students:vector<Student*>:aggregation ,  avaiablesCourses:vector<Course*>:aggregation)
    --Methods/operations    (Register , login , logout , addCourse , removeCourse, BrowseCourses , attribute(setter/getter) )    
*/

// ============================
// Base Class: User
// ============================
class User {
protected:
    int mId{};
    std::string mName;
    std::string mPassword;
    bool mLoggedIn{false};

public:
    User() = default;

    User(int id, std::string name, std::string password)
        : mId{id}, mName{std::move(name)}, mPassword{std::move(password)} {}

    //virtual ~User() = default;

    bool login(const std::string& password) {
        if (password == mPassword) {
            mLoggedIn = true;
            return true;
        }
        return false;
    }

    void logout() {
        mLoggedIn = false;
    }

    std::string getName() const { return mName; }
    int getId() const { return mId; }
};

// ============================
// Class: Assignment
// ============================
class Assignment {
private:
    std::string mTitle;
    std::string mDescription;
    int mGrade{0};

public:
    Assignment(std::string title, std::string description)
        : mTitle(std::move(title)), mDescription(std::move(description)) {}

    void submit(int grade) {
        mGrade = grade;
    }

    int getGrade() const { return mGrade; }

    std::string getTitle() const { return mTitle; }
    std::string getDescription() const { return mDescription; }
};

// ============================
// Class: Course
// ============================
class Course {
private:
    std::string mTitle;
    std::string mDescription;
    std::vector<std::unique_ptr<Assignment>> mAssignments;

public:
    Course(std::string title, std::string description)
        : mTitle(std::move(title)), mDescription(std::move(description)) {}

    void addAssignment(std::unique_ptr<Assignment> assignment) {
        mAssignments.push_back(std::move(assignment));
    }

    void displayDescription() const {
        std::cout << "Course: " << mTitle << "\n"
                  << "Description: " << mDescription << "\n";
    }

    std::string getTitle() const { return mTitle; }

    const std::vector<std::unique_ptr<Assignment>>& getAssignments() const {
        return mAssignments;
    }
};
// ============================
// Class: Student (inherits User)
// ============================
class Student : public User {
private:
    std::vector<std::shared_ptr<Course>> mCourses;

public:
    using User::User; // inherit constructor

    void enrollCourse(const std::shared_ptr<Course>& course) {
        mCourses.push_back(course);
    }

    void removeCourse(const std::shared_ptr<Course>& course) {
        auto it = std::find(mCourses.begin(), mCourses.end(), course);
        if (it != mCourses.end()) {
            mCourses.erase(it);
        }
    }

    void browseCourses(const std::vector<std::shared_ptr<Course>>& allCourses) const {
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
};

// ============================
// Class: CourseSystem (Director)
// Aggregation of Students & Courses
// ============================
class CourseSystem {
private:
    std::vector<std::shared_ptr<Student>> mStudents;
    std::vector<std::shared_ptr<Course>> mCourses;

public:
    void registerStudent(const std::shared_ptr<Student>& student) {
        mStudents.push_back(student);
    }

    void addCourse(const std::shared_ptr<Course>& course) {
        mCourses.push_back(course);
    }

    const std::vector<std::shared_ptr<Course>>& getAllCourses() const {
        return mCourses;
    }

    void browseCourses() const {
        for (const auto& course : mCourses) {
            course->displayDescription();
        }
    }
};
int main() {
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

    system.addCourse(cppCourse);
    system.addCourse(oopCourse);

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

    cppCourse->addAssignment(std::move(a1));
    oopCourse->addAssignment(std::move(a2));

    // ============================
    // Register Student
    // ============================
    auto student1 = std::make_shared<Student>(
        1, "Ahmed", "1234"
    );

    system.registerStudent(student1);

    // ============================
    // Case 1: Failed Login
    // ============================
    std::cout << "\n--- Attempt Login (Wrong Password) ---\n";
    if (!student1->login("wrong_pass")) {
        std::cout << "Login failed!\n";
    }

    // ============================
    // Case 2: Successful Login
    // ============================
    std::cout << "\n--- Attempt Login (Correct Password) ---\n";
    if (student1->login("1234")) {
        std::cout << "Login successful!\n";
    }

    // ============================
    // Case 3: Browse All Courses
    // ============================
    std::cout << "\n--- Browse All Courses ---\n";
    student1->browseCourses(system.getAllCourses());

    // ============================
    // Case 4: Enroll in Course
    // ============================
    std::cout << "\n--- Enroll in C++ Course ---\n";
    student1->enrollCourse(cppCourse);

    // ============================
    // Case 5: Submit Assignment
    // ============================
    std::cout << "\n--- Submit Assignment ---\n";
    // a1->submit(95);  // student gets grade 95
    cppCourse->getAssignments()[0]->submit(95);

    // ============================
    // Case 6: View Grades
    // ============================
    std::cout << "\n--- View Grades ---\n";
    student1->viewGrades();

    // ============================
    // Case 7: Enroll in Another Course
    // ============================
    std::cout << "\n--- Enroll in OOP Course ---\n";
    student1->enrollCourse(oopCourse);

    // a2->submit(88);  // grade for second course
    oopCourse->getAssignments()[0]->submit(88);

    std::cout << "\n--- View Grades Again ---\n";
    student1->viewGrades();

    // ============================
    // Case 8: Logout
    // ============================
    std::cout << "\n--- Logout ---\n";
    student1->logout();
    std::cout << "Student logged out.\n";

    return 0;

}

```
