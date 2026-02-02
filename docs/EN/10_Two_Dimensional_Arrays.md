# 📊 Two-Dimensional Arrays and Processing of Tabular Information

## 📊 Two-Dimensional Arrays
A two-dimensional array is a data structure that allows for the storage of values in a tabular format (rows and columns). They are often used to represent matrices, tables, game boards, and other similar structures.

### Declaring and Initializing a Two-Dimensional Array in C++
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

### Accessing Elements in a Two-Dimensional Array
Accessing elements is done via the row and column indices:
```cpp
int x = matrix[1][2]; // Gets the value from the second row, third column
matrix[0][1] = 42;   // Changes the value of the first row, second column
```

### Input and Output of a Two-Dimensional Array
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
Two-dimensional arrays are useful for storing and processing tabular data such as grades, results, statistics, and others.

**Example: Finding the sum of all elements:**
```cpp
int sum = 0;
for (int i = 0; i < rows; i++) {
    for (int j = 0; j < cols; j++) {
        sum += matrix[i][j];
    }
}
cout << "The sum of all elements is: " << sum << endl;
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

## 🎮 Special Types of Matrices

### Square Matrix
A matrix with an equal number of rows and columns (N x N).
```cpp
const int N = 4;
int square[N][N];
```

### Main and Secondary Diagonal
```cpp
// Main diagonal (elements where i == j)
for (int i = 0; i < N; i++) {
    cout << square[i][i] << " ";
}

// Secondary diagonal (elements where i + j == N - 1)
for (int i = 0; i < N; i++) {
    cout << square[i][N - 1 - i] << " ";
}
```

### Transposing a Matrix
Swapping rows and columns.
```cpp
void transpose(int matrix[][100], int rows, int cols) {
    int temp[100][100];
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            temp[j][i] = matrix[i][j];
        }
    }
    // Copy back
    for (int i = 0; i < cols; i++) {
        for (int j = 0; j < rows; j++) {
            matrix[i][j] = temp[i][j];
        }
    }
}
```

### Matrix Multiplication
```cpp
void multiply(int A[][100], int B[][100], int C[][100], int n, int m, int p) {
    // A is n x m, B is m x p, C will be n x p
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < p; j++) {
            C[i][j] = 0;
            for (int k = 0; k < m; k++) {
                C[i][j] += A[i][k] * B[k][j];
            }
        }
    }
}
```

## 🗺️ Two-Dimensional Arrays in Labyrinth and Graph Tasks

### Neighbor Traversal (4 directions)
```cpp
int dx[] = {-1, 1, 0, 0}; // Up, down, left, right
int dy[] = {0, 0, -1, 1};

for (int dir = 0; dir < 4; dir++) {
    int newX = x + dx[dir];
    int newY = y + dy[dir];
    
    if (newX >= 0 && newX < rows && newY >= 0 && newY < cols) {
        // Valid position
    }
}
```

### Neighbor Traversal (8 directions)
```cpp
int dx[] = {-1, -1, -1, 0, 0, 1, 1, 1};
int dy[] = {-1, 0, 1, -1, 1, -1, 0, 1};

for (int dir = 0; dir < 8; dir++) {
    int newX = x + dx[dir];
    int newY = y + dy[dir];
    
    if (newX >= 0 && newX < rows && newY >= 0 && newY < cols) {
        // Valid position
    }
}
```

### Flood Fill Algorithm
Filling an area with a new color (similar to the "bucket fill" tool in graphic editors).
```cpp
void floodFill(int grid[][100], int x, int y, int rows, int cols, int oldColor, int newColor) {
    if (x < 0 || x >= rows || y < 0 || y >= cols) return;
    if (grid[x][y] != oldColor) return;
    
    grid[x][y] = newColor;
    
    floodFill(grid, x-1, y, rows, cols, oldColor, newColor);
    floodFill(grid, x+1, y, rows, cols, oldColor, newColor);
    floodFill(grid, x, y-1, rows, cols, oldColor, newColor);
    floodFill(grid, x, y+1, rows, cols, oldColor, newColor);
}
```

## 💾 Dynamic Allocation of Two-Dimensional Arrays

### Using `vector<vector<int>>`
```cpp
#include <vector>
using namespace std;

int main() {
    int rows = 5, cols = 4;
    vector<vector<int>> matrix(rows, vector<int>(cols, 0));
    
    // Accessing elements
    matrix[2][3] = 10;
    
    // Traversal
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < cols; j++) {
            cout << matrix[i][j] << " ";
        }
        cout << endl;
    }
    
    return 0;
}
```

## 📐 Prefix Sums in 2D

Prefix sums in 2D allow for the fast calculation of the sum of elements in a rectangular area.

```cpp
void buildPrefix2D(int matrix[][100], int prefix[][101], int rows, int cols) {
    for (int i = 1; i <= rows; i++) {
        for (int j = 1; j <= cols; j++) {
            prefix[i][j] = matrix[i-1][j-1] + 
                          prefix[i-1][j] + 
                          prefix[i][j-1] - 
                          prefix[i-1][j-1];
        }
    }
}

int rangeSum2D(int prefix[][101], int r1, int c1, int r2, int c2) {
    // Sum of the rectangle from (r1, c1) to (r2, c2) inclusive
    // We use 1-based indexing for prefix
    return prefix[r2+1][c2+1] - 
           prefix[r1][c2+1] - 
           prefix[r2+1][c1] + 
           prefix[r1][c1];
}
```

# 🧱 Structure Type in C++. Arrays of Structures.

## 🧱 Structures in C++
Structures (`struct`) are user-defined data types that allow for grouping various related values into a single logical unit.

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
    {"Georgi", 19, 4.90}
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

    cout << "Enter information for the students:" << endl;
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

**Example: Average success of the group:**
```cpp
double totalGrade = 0;
for (int i = 0; i < size; i++) {
    totalGrade += students[i].grade;
}
double averageGrade = totalGrade / size;
cout << "Average success of the group: " << averageGrade << endl;
```

## 🎯 Sorting Structures

### Using `sort` with a Comparator
```cpp
#include <algorithm>
#include <vector>

bool compareByGrade(const Student& a, const Student& b) {
    return a.grade > b.grade; // Descending by grade
}

int main() {
    vector<Student> students = {
        {"Ivan", 20, 5.50},
        {"Maria", 21, 5.80},
        {"Georgi", 19, 4.90}
    };
    
    sort(students.begin(), students.end(), compareByGrade);
    
    for (const auto& s : students) {
        cout << s.name << ": " << s.grade << endl;
    }
    
    return 0;
}
```

### Multiple Criteria Sorting
Sorting by several criteria (first by grade, then by name).
```cpp
bool compareMultiple(const Student& a, const Student& b) {
    if (a.grade != b.grade) {
        return a.grade > b.grade; // Higher grade first
    }
    return a.name < b.name; // For equal grades - in alphabetical order
}
```

## 📊 Practice Tasks

1. **Matrix Rotation**: Rotate a square matrix 90 degrees clockwise.
2. **Spiral**: Traverse a matrix in a spiral order (from outer elements to the center).
3. **Saddle Point**: Find an element that is minimal in its row and maximal in its column.
4. **Zigzag Traversal**: Traverse a matrix in a zigzag order.
5. **Island**: Count "islands" in a binary matrix (connected ones).
6. **Student Database**: Create a program for student management with search, sort, and filtering.
7. **Pascal's Triangle**: Generate the first N rows of Pascal's Triangle in a matrix.

# 🏁 Conclusion
Understanding two-dimensional arrays and structures in C++ is key to the efficient processing of tabular information and real-world objects. Two-dimensional arrays allow for working with large amounts of data in tabular format, while structures provide a convenient way to group related information. Arrays of structures combine both concepts, providing a powerful programming tool. Prefix sums in 2D are an important optimization for tasks with queries on rectangular areas. Ensure you understand indexing, boundaries, and basic traversal algorithms for two-dimensional arrays. 🚀