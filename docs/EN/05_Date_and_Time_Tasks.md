# 📅 Tasks Related to Date and Time in C++

Working with dates and time is a common task in programming. The C++ language offers standard libraries and functions for handling temporal data.

## 🔧 Basic Functions for Working with Dates and Time

C++ uses the `<ctime>` library for processing dates and time. This library provides functions and structures for representing, manipulating, and outputting temporal values.

### 📚 Key Structures and Types
- `time_t`  
  Represents time as the number of seconds since January 1, 1970 (Epoch Time).
  
- `tm`  
  A structure that contains detailed information about a date and time:
  - `tm_sec`: seconds (0-59)
  - `tm_min`: minutes (0-59)
  - `tm_hour`: hours (0-23)
  - `tm_mday`: day of the month (1-31)
  - `tm_mon`: month (0-11, where 0 is January)
  - `tm_year`: year (the year minus 1900)
  - `tm_wday`: day of the week (0-6, where 0 is Sunday)
  - `tm_yday`: day of the year (0-365)


## 🕒 Retrieving the Current Date and Time

### Code for the current time:
```cpp
#include <iostream>
#include <ctime>
using namespace std;

int main() {
    time_t now = time(0); // Getting the current time
    tm *localTime = localtime(&now); // Converting to local time

    cout << "Current date and time: "
         << localTime->tm_mday << "/"
         << (localTime->tm_mon + 1) << "/"
         << (1900 + localTime->tm_year) << " "
         << localTime->tm_hour << ":"
         << localTime->tm_min << ":"
         << localTime->tm_sec << endl;

    return 0;
}
```

## ⏳ Converting Time to Different Formats

C++ allows for time formatting using the `strftime` function.

Example:
```cpp
#include <iostream>
#include <ctime>
using namespace std;

int main() {
    time_t now = time(0);
    tm *localTime = localtime(&now);

    char buffer[80];
    strftime(buffer, sizeof(buffer), "%d-%m-%Y %H:%M:%S", localTime);

    cout << "Formatted time: " << buffer << endl;

    return 0;
}
```

## 📆 Difference Between Dates

You can calculate the difference between two dates using `difftime`.

Example:
```cpp
#include <iostream>
#include <ctime>
using namespace std;

int main() {
    time_t now = time(0);
    time_t future = now + (7 * 24 * 60 * 60); // After 7 days

    double diff = difftime(future, now);
    cout << "Difference in seconds: " << diff << endl;

    return 0;
}
```

## 🔄 Working with System Time and Timers

You can use the `clock()` function to measure elapsed processor time.

Example of a timer:
```cpp
#include <iostream>
#include <ctime>
using namespace std;

int main() {
    clock_t start = clock();

    // Simulating a calculation
    for (int i = 0; i < 1e7; ++i);

    clock_t end = clock();
    double elapsed = double(end - start) / CLOCKS_PER_SEC;

    cout << "Elapsed time: " << elapsed << " seconds." << endl;

    return 0;
}
```

## 🗓️ Notes on Working with Dates and Time

- **UTC and Local Time**
  - `gmtime()` returns the time in UTC.
  - `localtime()` returns the local time.

- **Time Formatting**
  - Use templates like `%d`, `%m`, `%Y`, `%H`, `%M`, `%S` in `strftime`.

- **Errors in Time Handling**
  - Ensure you correctly handle values for months (0-11) and years (minus 1900).

## 📊 Typical Algorithmic Tasks with Dates

### 1. Leap Year Check

A leap year is:
- Divisible by 4 **and** not divisible by 100, **or**
- Divisible by 400

```cpp
bool isLeapYear(int year) {
    return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
}
```

Examples:
- 2000 is a leap year (divisible by 400)
- 1900 is not a leap year (divisible by 100, but not by 400)
- 2024 is a leap year (divisible by 4, but not by 100)

### 2. Number of Days in a Month

```cpp
int daysInMonth(int month, int year) {
    if (month == 2) {
        return isLeapYear(year) ? 29 : 28;
    }
    // April, June, September, November have 30 days
    if (month == 4 || month == 6 || month == 9 || month == 11) {
        return 30;
    }
    return 31; // The rest have 31
}
```

### 3. Day of the Week (Zeller's Congruence)

Zeller's congruence allows for the calculation of the day of the week for an arbitrary date:

```cpp
int dayOfWeek(int day, int month, int year) {
    if (month < 3) {
        month += 12;
        year--;
    }
    int q = day;
    int m = month;
    int k = year % 100;
    int j = year / 100;
    
    int h = (q + ((13 * (m + 1)) / 5) + k + (k / 4) + (j / 4) - 2 * j) % 7;
    
    // h = 0: Saturday, 1: Sunday, ..., 6: Friday
    return h;
}
```

### 4. Difference in Days Between Two Dates

To find the number of days between two dates, we can use the Julian Day formula:

```cpp
int julianDay(int day, int month, int year) {
    int a = (14 - month) / 12;
    int y = year + 4800 - a;
    int m = month + 12 * a - 3;
    
    return day + (153 * m + 2) / 5 + 365 * y + y / 4 - y / 100 + y / 400 - 32045;
}

int daysBetween(int d1, int m1, int y1, int d2, int m2, int y2) {
    return abs(julianDay(d2, m2, y2) - julianDay(d1, m1, y1));
}
```

### 5. Adding Days to a Date

```cpp
void addDays(int &day, int &month, int &year, int daysToAdd) {
    while (daysToAdd > 0) {
        int daysInCurrentMonth = daysInMonth(month, year);
        if (day + daysToAdd <= daysInCurrentMonth) {
            day += daysToAdd;
            break;
        }
        daysToAdd -= (daysInCurrentMonth - day + 1);
        day = 1;
        month++;
        if (month > 12) {
            month = 1;
            year++;
        }
    }
}
```

## 🎯 Practical Tasks

### Task 1: Calculating Age
```cpp
struct Date {
    int day, month, year;
};

int calculateAge(Date birth, Date current) {
    int age = current.year - birth.year;
    
    // If the current month is before the month of birth
    // or it is the same month but the day is before the day of birth
    if (current.month < birth.month || 
        (current.month == birth.month && current.day < birth.day)) {
        age--;
    }
    
    return age;
}
```

### Task 2: Finding Friday the 13th
```cpp
void findFriday13(int year) {
    for (int month = 1; month <= 12; month++) {
        if (dayOfWeek(13, month, year) == 6) { // 6 = Friday in Zeller's
            cout << "Friday the 13th: " << month << "/" << year << endl;
        }
    }
}
```

### Task 3: Number of Working Days Between Two Dates
```cpp
int workingDays(int d1, int m1, int y1, int d2, int m2, int y2) {
    int count = 0;
    int day = d1, month = m1, year = y1;
    
    while (day != d2 || month != m2 || year != y2) {
        int dow = dayOfWeek(day, month, year);
        // Monday (1) to Friday (5) are working days
        if (dow >= 1 && dow <= 5) {
            count++;
        }
        
        // Move to the next day
        day++;
        if (day > daysInMonth(month, year)) {
            day = 1;
            month++;
            if (month > 12) {
                month = 1;
                year++;
            }
        }
    }
    
    return count;
}
```

## 🔬 Modern C++ (C++20 chrono)

C++20 introduced a more powerful `<chrono>` library for working with dates and time:

```cpp
#include <iostream>
#include <chrono>
using namespace std;
using namespace std::chrono;

int main() {
    // Current date
    auto now = system_clock::now();
    auto today = floor<days>(now);
    
    year_month_day ymd{today};
    
    cout << "Year: " << (int)ymd.year() << endl;
    cout << "Month: " << (unsigned)ymd.month() << endl;
    cout << "Day: " << (unsigned)ymd.day() << endl;
    
    // Operations with dates
    auto tomorrow = ymd + days{1};
    auto nextWeek = ymd + weeks{1};
    
    return 0;
}
```

## ⚠️ Important Notes

1. **Time Zones**: When working with global systems, always use UTC instead of local time.
2. **Leap Seconds**: For high-precision calculations, keep in mind that leap seconds can affect results.
3. **Overflow**: When calculating dates far in the future or past, be careful of `time_t` overflow.
4. **Validation**: Always check whether the entered date is valid (e.g., February 31 does not exist).

## 🧩 Olympiad Tasks

1. **Finding the n-th day of the year**: Given a date (day, month, year), find which day of the year it is (1-365/366).
2. **Next Palindromic Day**: Find the next date that is a palindrome in DDMMYYYY format.
3. **Number of Weekends in an Interval**: Given two dates, find the number of Saturdays and Sundays between them.
4. **Nearest Monday**: Given an arbitrary date, find the next Monday.

## 🏁 Conclusion

Working with dates and time in C++ requires an understanding of both libraries and mathematical algorithms. Leap years, varying number of days in months, and days of the week make the tasks challenging, but with the right functions and formulas, you can solve complex problems effectively. Practice with different tasks to develop intuition when working with temporal data! 🚀