# 🧮 Обикновени Дроби (Common Fractions)

В много задачи (особено геометрични или вероятностни) се изисква абсолютна точност. Числата с плаваща запетая (`double`, `float`) имат ограничена прецизност и натрупват грешки.
Решението е да използваме **Обикновени дроби** (числител и знаменател).

## 1. Структура `Fraction`

Дробта се представя като двойка цели числа $\frac{a}{b}$.
Задължително условие: $b 
eq 0$.
Добър навик е винаги да поддържаме дробта в **несъкратим вид** и знаменателят да е положителен.

```cpp
#include <iostream>
#include <numeric> // за std::gcd (C++17) или напишете своя

using namespace std;

// Помощна функция за НОД
long long gcd(long long a, long long b) {
    return b == 0 ? a : gcd(b, a % b);
}

struct Fraction {
    long long num, den; // Numerator, Denominator

    Fraction(long long n = 0, long long d = 1) {
        if (d == 0) {
            cerr << "Error: Division by zero!" << endl;
            d = 1; 
        }
        long long common = gcd(abs(n), abs(d));
        num = n / common;
        den = d / common;
        if (den < 0) { // Знакът винаги в числителя
            num = -num;
            den = -den;
        }
    }

    // Събиране: a/b + c/d = (ad + bc) / bd
    Fraction operator+(const Fraction& other) const {
        return Fraction(num * other.den + other.num * den, den * other.den);
    }

    // Изваждане
    Fraction operator-(const Fraction& other) const {
        return Fraction(num * other.den - other.num * den, den * other.den);
    }

    // Умножение
    Fraction operator*(const Fraction& other) const {
        return Fraction(num * other.num, den * other.den);
    }

    // Деление
    Fraction operator/(const Fraction& other) const {
        return Fraction(num * other.den, den * other.num);
    }

    // Сравнение (Крос-умножение за избягване на деление)
    bool operator<(const Fraction& other) const {
        return num * other.den < other.num * den;
    }
    bool operator==(const Fraction& other) const {
        return num == other.num && den == other.den; // Защото са съкратени
    }
    bool operator<=(const Fraction& other) const {
        return *this < other || *this == other;
    }
    bool operator>(const Fraction& other) const {
        return !(*this <= other);
    }
    bool operator>=(const Fraction& other) const {
        return !(*this < other);
    }
    bool operator!=(const Fraction& other) const {
        return !(*this == other);
    }
};

// Изход
ostream& operator<<(ostream& os, const Fraction& f) {
    if (f.den == 1) os << f.num;
    else os << f.num << "/" << f.den;
    return os;
}

// Вход
istream& operator>>(istream& is, Fraction& f) {
    long long n, d = 1;
    char slash;
    is >> n;
    if (is.peek() == '/') {
        is >> slash >> d;
    }
    f = Fraction(n, d);
    return is;
}
```

## 2. Оптимизации

Горната имплементация използва `num * other.den`, което може да препълни `long long` ако числата са големи ($> 10^9$). 
*   Вместо `Fraction(num * other.den + ...)` е по-добре да се ползва НОК на знаменателите:
    $\text{lcm} = (den \cdot other.den) / gcd(den, other.den)$.
    Това държи числата по-малки преди съкращаването.

### Подобрена версия със защита от препълване:

```cpp
// НОК функция
long long lcm(long long a, long long b) {
    return a / gcd(a, b) * b;
}

// Подобрено събиране
Fraction addSafe(const Fraction& a, const Fraction& b) {
    long long l = lcm(a.den, b.den);
    long long num1 = a.num * (l / a.den);
    long long num2 = b.num * (l / b.den);
    return Fraction(num1 + num2, l);
}
```

### Работа с много големи числа:

Ако числата са наистина огромни, можете да използвате `__int128` или да имплементирате big integer класа:

```cpp
typedef __int128 lll;

lll gcd128(lll a, lll b) {
    return b == 0 ? a : gcd128(b, a % b);
}

struct BigFraction {
    lll num, den;
    
    BigFraction(lll n = 0, lll d = 1) {
        if (d == 0) d = 1;
        lll g = gcd128(abs(n), abs(d));
        num = n / g;
        den = d / g;
        if (den < 0) {
            num = -num;
            den = -den;
        }
    }
    
    BigFraction operator+(const BigFraction& other) const {
        return BigFraction(num * other.den + other.num * den, den * other.den);
    }
    
    // Печатане за __int128
    void print() {
        if (num < 0) {
            cout << "-";
            print128(-num);
        } else {
            print128(num);
        }
        if (den != 1) {
            cout << "/";
            print128(den);
        }
    }
    
private:
    void print128(lll x) {
        if (x > 9) print128(x / 10);
        cout << (char)('0' + x % 10);
    }
};
```

## 3. Смесени числа

Ако трябва да изведете резултата като смесено число (напр. $7/3 = 2 \frac{1}{3}$):
```cpp
void printMixed(Fraction f) {
    long long whole = f.num / f.den;
    long long rem = abs(f.num % f.den); // Остатъкът е винаги положителен за дробната част
    if (rem == 0) {
        cout << whole << endl;
    } else {
        if (whole != 0) cout << whole << " ";
        else if (f.num < 0) cout << "-"; // Само знак минус ако няма цяла част
        cout << rem << "/" << f.den << endl;
    }
}
```

## 4. Разширен алгоритъм на Евклид

НОД е основата на дробите, но разширеният алгоритъм на Евклид предлага повече:

### 4.1. Базов алгоритъм

```cpp
// Намира НОД и коефициенти x, y такива че ax + by = gcd(a, b)
long long extgcd(long long a, long long b, long long& x, long long& y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    long long x1, y1;
    long long d = extgcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - (a / b) * y1;
    return d;
}
```

### 4.2. Модулно обратно

Намиране на $a^{-1} \pmod{m}$ (необходимо за някои операции с дроби в модулна аритметика):

```cpp
long long modinv(long long a, long long m) {
    long long x, y;
    long long g = extgcd(a, m, x, y);
    if (g != 1) return -1; // Обратното не съществува
    return (x % m + m) % m;
}
```

### 4.3. Решаване на линейно диофантово уравнение

Уравнение от вида $ax + by = c$:

```cpp
bool solveDiophantine(long long a, long long b, long long c,
                      long long& x, long long& y) {
    long long g = extgcd(a, b, x, y);
    if (c % g != 0) return false; // Няма решение
    
    x *= c / g;
    y *= c / g;
    return true;
}
```

### 4.4. Китайска теорема за остатъците (CRT)

Решаване на система от конгруенции:
```
x ≡ a1 (mod m1)
x ≡ a2 (mod m2)
```

```cpp
pair<long long, long long> crt(long long a1, long long m1,
                               long long a2, long long m2) {
    long long x, y;
    long long g = extgcd(m1, m2, x, y);
    
    if ((a2 - a1) % g != 0) return {-1, -1}; // Няма решение
    
    long long lcm_val = m1 / g * m2;
    long long solution = (a1 + x * m1 * ((a2 - a1) / g)) % lcm_val;
    if (solution < 0) solution += lcm_val;
    
    return {solution, lcm_val};
}
```

## 5. Медиант на дроби

Медиантът на $\frac{a}{b}$ и $\frac{c}{d}$ е $\frac{a+c}{b+d}$. Използва се в алгоритъма на Stern-Brocot за намиране на дроби с малък знаменател:

```cpp
// Stern-Brocot дърво за намиране на дроб най-близък до target
Fraction sternBrocot(double target, int maxDen) {
    Fraction left(0, 1), right(1, 0); // Граници
    
    while (true) {
        // Медиант
        Fraction mid(left.num + right.num, left.den + right.den);
        
        if (mid.den > maxDen) break;
        
        double midVal = (double)mid.num / mid.den;
        if (abs(midVal - target) < 1e-9) return mid;
        
        if (midVal < target) {
            left = mid;
        } else {
            right = mid;
        }
    }
    
    // Връщаме по-близкия от left и right
    double dLeft = abs((double)left.num / left.den - target);
    double dRight = abs((double)right.num / right.den - target);
    return dLeft < dRight ? left : right;
}
```

## 6. Практически задачи

1.  **Codeforces 834D**: Египетски дроби - представяне като сума $1/a + 1/b + ...$
2.  **Codeforces 702A**: Максимална растяща подредица (понякога дробите помагат при сравнение на наклони).
3.  **Геометрия**: Намиране на пресечна точка на две прави ($Ax + By + C = 0$). Резултатът често е дроб.
4.  **Вероятности**: Задачи, изискващи отговор като несъкратима дроб "P/Q".
5.  **CSES Exponentiation II**: Модулна експоненциация с разширен Евклид.
6.  **Project Euler 71**: Ordered Fractions - Farey последователности.

## 7. Farey последователности

Farey последователността $F_n$ съдържа всички несъкратими дроби между 0 и 1 със знаменател $\leq n$, подредени във възходящ ред.

Свойство: Ако $\frac{a}{b}$ и $\frac{c}{d}$ са съседни дроби в $F_n$, то $bc - ad = 1$.

```cpp
void generateFarey(int n) {
    Fraction a(0, 1), b(1, n);
    cout << a << " ";
    
    while (b.den != 1 || b.num != 1) {
        cout << b << " ";
        
        int k = (n + a.den) / b.den;
        Fraction next(k * b.num - a.num, k * b.den - a.den);
        a = b;
        b = next;
    }
    cout << b << endl;
}
```

## 🏁 Заключение

Класът `Fraction` е част от "бойния арсенал" на всеки състезател. Напишете го веднъж, тествайте го и го запазете в библиотеката си с кодове (Template). Разширеният алгоритъм на Евклид разкрива множество приложения - от модулно обратно до решаване на диофантови уравнения и китайската теорема за остатъците.