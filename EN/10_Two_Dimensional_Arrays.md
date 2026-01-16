# 📊 Two-dimensional Arrays and Tabular Data Processing

## 📊 Two-dimensional Arrays
A two-dimensional array is a data structure that allows storing values in a tabular format (rows and columns). They are often used to represent matrices, tables, game boards, and other similar structures.

### Declaration and Initialization of a Two-dimensional Array in C++
In C++, two-dimensional arrays are declared using two sets of square brackets:
```cpp
int matrix[3][4]; // Declares a two-dimensional array with 3 rows and 4 columns
```
We can also initialize it with values:
```cpp
int matrix[2][3] = {
    {1, 2, 3},
    {4, 5, 6}
};
```
If we let C++ determine the number of rows:
```cpp
int matrix[][3] = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};
```

### Accessing Elements in a Two-dimensional Array
Access to elements is done via row and column indices:
```cpp
int x = matrix[1][2]; // Gets the value from the second row, third column
matrix[0][1] = 42;   // Changes the value of the first row, second column
```

### Input and Output of a Two-dimensional Array
```cpp
#include <iostream>
using namespace std;

int main() {
    const int rows = 2, cols = 3;
    int matrix[rows][cols];

    cout << "Enter values for the two-dimensional array:" << endl;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            cin >> matrix[i][j];
        }
    }

    cout << "The entered matrix:" << endl;
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            cout << matrix[i][j] << " ";
        }
        cout << endl;
    }
    return 0;
}
```

### Processing Tabular Information
Two-dimensional arrays are useful for storing and processing tabular data such as grades, results, statistics, and more.

**Example: Finding the sum of all elements:**
```cpp
int sum = 0;
for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
        sum += matrix[i][j];
    }
}
cout << "Sum of all elements is: " << sum << endl;
```

**Example: Finding the maximum element:**
```cpp
int maxVal = matrix[0][0];
for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
        if (matrix[i][j] > maxVal) {
            maxVal = matrix[i][j];
        }
    }
}
cout << "The maximum value is: " << maxVal << endl;
```

# 🧱 Struct Type in C++. Arrays of Structures.

## 🧱 Structures in C++
Structures (`struct`) are user-defined data types that allow grouping different related values into a single logical unit.

### Declaring a Structure
```cpp
struct Student {
    string name;
    int age;
    double grade;
};
```
Here `Student` is a structure containing three fields: name, age, and grade.

### Creating and Using Structures
```cpp
Student s1 = {"Ivan", 20, 5.50};
cout << "Name: " << s1.name << ", Age: " << s1.age << ", Grade: " << s1.grade << endl;
```

## Arrays of Structures
We can create an array of structures to store multiple objects.

```cpp
Student students[3] = {
    {"Ivan", 20, 5.50},
    {"Maria", 21, 5.80},
    {"George", 19, 4.90}
};
```

### Input and Output of an Array of Structures
```cpp
#include <iostream>
using namespace std;

struct Student {
    string name;
    int age;
    double grade;
};

int main() {
    const int size = 3;
    Student students[size];

    cout << "Enter information for students:" << endl;
    for (int i = 0; i < size; i++) {
        cout << "Name: ";
        cin >> students[i].name;
        cout << "Age: ";
        cin >> students[i].age;
        cout << "Grade: ";
        cin >> students[i].grade;
    }

    cout << "List of students:" << endl;
    for (int i = 0; i < size; i++) {
        cout << students[i].name << " - Age: " << students[i].age << ", Grade: " << students[i].grade << endl;
    }

    return 0;
}
```

### Searching and Processing Data in an Array of Structures
**Example: Finding the student with the highest grade:**
```cpp
Student bestStudent = students[0];
for (int i = 1; i < size; i++) {
    if (students[i].grade > bestStudent.grade) {
        bestStudent = students[i];
    }
}
cout << "Student with the highest grade: " << bestStudent.name << " (" << bestStudent.grade << ")" << endl;
```

**Example: Average grade of the group:**
```cpp
double totalGrade = 0;
for (int i = 0; i < size; i++) {
    totalGrade += students[i].grade;
}
double averageGrade = totalGrade / size;
cout << "Average grade of the group: " << averageGrade << endl;
```

# 🏁 Conclusion
Understanding two-dimensional arrays and structures in C++ is key to efficiently processing tabular information and real-world objects. Two-dimensional arrays allow working with large amounts of data, while structures provide a convenient way to group related information. Arrays of structures combine both concepts, providing a powerful programming tool. 🚀
