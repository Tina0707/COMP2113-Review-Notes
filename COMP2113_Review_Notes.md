# COMP2113 / ENGG1340 完整复习笔记

> 涵盖 Module 6-10 全部知识点，包含所有课件细节、代码示例和注意事项

---

## 目录

1. [Module 6: Arrays & Strings](#module-6-arrays--strings)
2. [Module 7: Structs, File I/O & Recursion](#module-7-structs-file-io--recursion)
3. [Module 8: Pointers, Dynamic Memory & Linked Lists](#module-8-pointers-dynamic-memory--linked-lists)
4. [Module 9: STL Containers & Iterators](#module-9-stl-containers--iterators)
5. [Module 10: C Programming](#module-10-c-programming)

---

## Module 6: Arrays & Strings

> **编译：** `g++ -pedantic-errors -std=c++11 program.cpp -o program`
> **预估用时：** 3 小时

### Part I: Arrays

#### 为什么需要 Array？

处理大量相同类型的数据时，如果用单独变量会非常繁琐：

```cpp
// ❌ 80 个独立变量 — 不现实
int score_01, score_02, score_03, ..., score_80;
cin >> score_01 >> score_02 >> ... >> score_80;

// ✅ 用 Array — 简洁高效
int score[80];
for (int i = 0; i < 80; i++) cin >> score[i];
```

#### 什么是 Array？

- 一组**连续**的 memory locations，所有元素类型相同
- 通过统一的名称加 `[index]` 访问

```
Main memory:
  score[0]  score[1]  score[2]  score[3]  ...
  ┌──────┬──────┬──────┬──────┬──────┬──────┐
  │      │      │      │      │      │      │ ...
  └──────┴──────┴──────┴──────┴──────┴──────┘
  ^
  score (数组名是指向第一个元素的常量指针)
```

#### 声明和初始化

```cpp
int arr[5] = {1, 2, 3, 4, 5};         // 静态初始化
int arr[] = {1, 2, 3, 4, 5};          // 编译器自动推断大小
int arr[5] = {0};                      // 全部初始化为 0
int arr[5] = {1, 2};                   // {1, 2, 0, 0, 0}，不足的补 0
int arr[5];                            // 未初始化，包含垃圾值
```

#### 传递数组给函数

数组传递给函数时**退化为指针**（decay to pointer），表现为 **pass-by-reference**：

```cpp
// 方式 1：指针语法
void printArray(int *arr, int size) {
    for (int i = 0; i < size; i++)
        cout << arr[i] << " ";
}

// 方式 2：数组语法（等价）
void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++)
        cout << arr[i] << " ";
}

int main() {
    int arr[5] = {1, 2, 3, 4, 5};
    printArray(arr, 5);   // 数组名本身就是地址，不用 & 符号
}
```

> **关键：** `sizeof(arr)` 在 main() 中返回整个数组字节数，但在函数参数中退化为指针，`sizeof(arr)` 只返回指针大小！

#### 搜索算法

**Linear Search（线性搜索）** — O(n)
```cpp
int linearSearch(int arr[], int size, int target) {
    for (int i = 0; i < size; i++)
        if (arr[i] == target) return i;   // 返回索引
    return -1;                             // 未找到
}
```

**Binary Search（二分搜索）** — O(log n)，**要求数组已排序**
```cpp
int binarySearch(int arr[], int size, int target) {
    int left = 0, right = size - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;   // 防止溢出
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

#### 排序算法

**Bubble Sort（冒泡排序）** — O(n²)
```cpp
void bubbleSort(int arr[], int size) {
    for (int i = 0; i < size - 1; i++)           // 轮次
        for (int j = 0; j < size - i - 1; j++)   // 每轮比较
            if (arr[j] > arr[j + 1])
                swap(arr[j], arr[j + 1]);
}
```

#### 二维数组

```cpp
// 声明和初始化
int matrix[3][4] = {
    {1, 2, 3, 4},
    {5, 6, 7, 8},
    {9, 10, 11, 12}
};

// 访问
matrix[row][col]
```

**传递二维数组给函数：**

必须指定**第二维（列）** 的大小：

```cpp
void printMatrix(int mat[][4], int rows) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < 4; j++)
            cout << setw(4) << mat[i][j];
        cout << endl;
    }
}
```

> 也可以传递 `int (*mat)[4]`（指向数组的指针），但上面的语法更常见。

### Part II: Char & Char Arrays (C-Strings)

#### Char 类型基础

```cpp
char ch = 'A';
char ch = 65;            // ASCII 65 = 'A'
cout << ch;              // 输出 'A'
cout << (int)ch;         // 输出 65
```

- `'A'` vs `"A"`：`'A'` 是 char 字面量，`"A"` 是字符串字面量（包含 'A' 和 '\0'）

#### C-String（字符数组）

- C-string 是以 **`\0` (null character)** 结尾的 char 数组
- `\0` 表示字符串结束
- 使用 `<cstring>` 或 `<string.h>` 库

```cpp
char str1[] = "hello";    // 自动加 \0，大小 = 6 字节
// str1 = {'h','e','l','l','o','\0'}

char str2[6] = "hello";   // 显式指定大小
char str3[] = {'h', 'e', 'l', 'l', 'o', '\0'};  // 手动加 \0

// 错误示范！忘记加 \0
char str4[] = {'h', 'e', 'l', 'l', 'o'};  // 不是合法字符串！
```

> **重要：** `char name[] = "Alan";` 会分配 5 个字符（包括末尾的 `\0`）。

#### C-String 操作函数 (`<cstring>`)

| 函数 | 说明 | 注意事项 |
|------|------|---------|
| `strlen(str)` | 返回字符串长度（不含 \0） | O(n) 遍历 |
| `strcpy(dest, src)` | 复制 src 到 dest | **必须确保 dest 空间足够！** |
| `strcat(dest, src)` | 将 src 拼接到 dest 末尾 | 覆盖 dest 原来的 \0，**确保空间足够** |
| `strcmp(s1, s2)` | 比较字典序 | 相等返回 0，s1<s2 返回负值，s1>s2 返回正值 |
| `strchr(str, ch)` | 查找字符首次出现位置 | |

```cpp
#include <cstring>

char a[100], b[100];
strcpy(a, "Kit");
strcpy(b, "Kit");

if (strcmp(a, b) == 0)      // 比较
    cout << "same" << endl;

strcpy(b, "David");          // 复制（确保 b 足够大！）
strcat(a, b);                // a = "KitDavid"（确保 a 足够大！）
int len = strlen(a);          // len = 8
```

> **⚠️ strcpy/strcat 的危险：** 如果 dest 空间不够，会导致 **buffer overflow（缓冲区溢出）**，造成运行时错误。务必确保目标数组足够大！

### Part III: C++ Strings

- 使用 `<string>` 库
- 更安全、更方便，无需手动管理 \0

```cpp
#include <string>
string s1 = "hello";
string s2 = "world";

// 常用操作
s1.length()                    // 长度
s1.size()                      // 同 length()
s1 + s2                        // 拼接
s1 == s2, s1 != s2, s1 < s2   // 比较（字典序）
s1.find("el")                  // 查找子串，返回首次出现的 index
s1.find("notfound")            // 返回 string::npos（超大值）
s1.substr(pos, len)            // 取子串
s1.c_str()                     // 转成 C-string（const char*）
getline(cin, s1)               // 读取一整行（含空格）
```

#### string vs C-string 选择

| C++ string | C-string (char array) |
|------------|----------------------|
| 自动管理内存 | 手动确保空间足够 |
| 支持 `+`, `==`, `<` 等运算符 | 只能用函数 (strcat, strcmp 等) |
| 动态增长 | 固定大小 |
| 更安全 | 更容易出 buffer overflow |

---

## Module 7: Structs, File I/O & Recursion

> 注意：Module 7 只使用 **C++**（不是 C）。编译：`g++ -pedantic-errors -std=c++11`

### Part I: Structs

#### 什么是 Struct？

- 将多个不同类型的变量组合成一个单一类型
- 成员默认 **public**
- 可以像基本类型一样：赋值、传参、函数返回

```cpp
struct Point {
    double x;
    double y;
};  // ← 别忘了分号！
```

#### 声明和初始化

```cpp
Point p1;                     // 未初始化（垃圾值）
Point p2 = {1.0, 2.0};        // 初始化列表
Student s = {3035123456, "Sze Ka Ka", 'F'};  // 顺序必须与定义一致
Student s2 = s1;              // 可以用另一个 struct 变量初始化
```

**⚠️ 初始化规则：**
- 值太多 → 编译错误
- 值太少 → 剩余成员设为 0（该类型的默认值）
  ```cpp
  Point p = {1.0};   // p.x = 1.0, p.y = 0.0
  ```

#### 访问成员 — 点运算符 `.`

```cpp
Point pt1 = {1.0, 2.0};
pt1.x *= 2.0;     // pt1.x = 2.0
pt1.y /= 2.0;     // pt1.y = 1.0

Student s1 = {3035123456, "Sze Ka Ka", 'F'};
int l = s1.name.length();   // l = 9 （string 的 length()）
```

#### 运算符限制

struct 变量**默认不支持** `+`, `-`, `>`, `<`, `==`, `&&`, `||` 等运算符！

```cpp
Point pt1 = {1.0, 2.0}, pt2 = {3.0, 5.0};
Point pt3 = pt1 + pt2;   // ❌ 编译错误
bool b = pt1 > pt2;      // ❌ 编译错误
bool c = pt1 == pt2;     // ❌ 编译错误
```

**唯一可用的是赋值运算符 `=`：**

```cpp
Point p1 = {1.0, 2.0}, p2;
p2 = p1;   // ✅ 逐个成员复制
```

#### 嵌套结构（Nested Structures）

```cpp
struct Point { double x, y; };

struct Triangle {
    Point p1, p2, p3;
};

// 初始化方式 1：嵌套大括号
Triangle tr1 = {{1.0, 2.0}, {3.0, 4.0}, {5.0, 6.0}};

// 初始化方式 2：扁平大括号
Triangle tr2 = {1.0, 2.0, 3.0, 4.0, 5.0, 6.0};

tr2.p1.x += tr1.p2.x;    // 嵌套访问
```

#### 结构体大小与内存对齐（Reference Only）

struct 的内存大小**不一定**等于成员大小之和，且成员顺序影响大小：

```cpp
struct structA {           // 24 bytes（有 padding）
    char c;
    double d;
    int s;
};

struct structB {           // 16 bytes（优化后）
    double d;
    int s;
    char c;
};
```

原因：**64-bit 机器按 8-byte 对齐**，数据对齐和填充（padding）导致差异。

#### 结构体数组（Array of Structures）

**Parallel Arrays（平行数组）的缺点：**

```cpp
string name[200];
int subclass[200];
int year[200], month[200], day[200];
double mark[200];
// 一个学生的信息分散在多个数组中：name[i], subclass[i], year[i]...
```

**Array of Structures（结构体数组）— 更优：**

```cpp
struct Student_rec {
    string name;
    int subclass;
    int year, month, day;
    double mark;
};

Student_rec student[200];
// 一个学生的信息全在 student[i] 中：student[i].name, student[i].mark...
```

#### Struct 与函数

**传值（Pass by Value）** — 复制整个 struct，开销大：
```cpp
double point_distance(Point p, Point q) {
    double dx = p.x - q.x;
    double dy = p.y - q.y;
    return sqrt(dx*dx + dy*dy);
}
```

**传引用（Pass by Reference）** — 推荐：
```cpp
void swap(Point &p, Point &q) {
    Point temp = p;
    p = q;
    q = temp;
}
```

**返回结构体：**
```cpp
Point input_point() {
    double x, y;
    cin >> x >> y;
    return {x, y};   // C++11 起可这样写
}
```

#### Struct 的成员函数（C++ 特有）

C 的 struct 只能有成员变量，C++ 的 struct **还可以有成员函数**：

```cpp
struct Circle {
    double x, y;
    double r;

    // 方式 1：直接在 struct 内定义
    double Area() {
        const double PI = 3.14159265;
        return PI * r * r;
    }

    void EnlargeCircle(double radius_to_add) {
        r += radius_to_add;
    }

    bool IsOverlap(Circle c);  // 只声明，外部定义
};

// 方式 2：使用 :: 在外部定义
bool Circle::IsOverlap(Circle c) {
    double dx = x - c.x;
    double dy = y - c.y;
    double centre_dist = sqrt(dx*dx + dy*dy);
    return (centre_dist <= (r + c.r));
}

// 使用
Circle p = {1, 1, 2};
p.EnlargeCircle(5);
cout << p.Area();
cout << (p.IsOverlap(q) ? "Yes" : "No");
```

#### 关于 Class（Optional）

**ADT（抽象数据类型）** 封装数据和操作，隐藏实现细节：

```cpp
class Point {
public:      // 公开接口
    double getX();
    double getY();
    void setCoord(double s, double t);
    double distance(Point &p);
private:     // 私有实现
    double x;
    double y;
};

// 成员函数定义
double Point::distance(Point &p) {
    double dx = p.x - x;    // x 是 this->x
    double dy = p.y - y;
    return sqrt(dx*dx + dy*dy);
}
```

**class vs struct 默认访问权限：**
- `struct`：成员默认 **public**
- `class`：成员默认 **private**

**多文件编译：**
- `.h` 文件 — 类接口（声明）
- `.cpp` 文件 — 类实现
- 其他程序只需 `#include "point.h"` 即可使用

### Part II: File I/O

#### 文件流三大类

```cpp
#include <fstream>

ifstream fin;     // 输入文件流（读）
ofstream fout;    // 输出文件流（写）
fstream fio;      // 双向文件流
```

#### 写文件（Output File Stream）

**步骤：**
1. 声明 `ofstream` 对象
2. 打开文件（`open()` 或构造函数）
3. 检查是否成功打开（`fail()`）
4. 用 `<<` 写入
5. 关闭文件（`close()`）

```cpp
#include <fstream>
#include <cstdlib>   // for exit()

string name = "Peter";
int age = 30;
double weight = 130.5;

// 方式 1：先声明再 open
ofstream fout;
fout.open("data1.txt");

// 方式 2：构造时直接打开（等价）
// ofstream fout("data1.txt");

if (fout.fail()) {          // 检查是否成功打开
    cout << "Error in file opening!" << endl;
    exit(1);                 // 立即终止程序
}

fout << name << " " << age << " " << weight << endl;
fout.close();               // 断开与文件的连接
```

> **注意：** 如果文件名存在 `string` 中，需要用 `.c_str()` 转换：
> ```cpp
> string filename = "data.txt";
> fout.open(filename.c_str());
> ```

**追加模式 `ios::app`：**
```cpp
ofstream fout("data2.txt", ios::app);   // 追加而非覆盖
```

#### 读文件（Input File Stream）

**步骤：**
1. 声明 `ifstream` 对象
2. 打开文件
3. 检查 `fail()`
4. 用 `>>` 读取
5. 关闭文件

```cpp
#include <fstream>
#include <cstdlib>

ifstream fin;
fin.open("data3.txt");
// 或 ifstream fin("data3.txt");

if (fin.fail()) {
    cout << "Error in file opening!" << endl;
    exit(1);
}

string name;
int age;
double weight;
fin >> name >> age >> weight;   // 用 >> 提取
fin.close();

cout << name << ", " << age << ", " << weight << endl;
```

#### 读取直到 EOF

**`while (fin >> x)` 模式：**
```cpp
double x, sum = 0;
while (fin >> x) {     // 读到 EOF 返回 false
    sum += x;
}
```

> `fin >> x` 的返回值：成功读取为 `true`，读到 EOF 为 `false`。

#### 按行读取

**`getline(fin, str)` 函数：**
```cpp
string line;
while (getline(fin, line)) {
    cout << line << endl;
}
```

`getline()` 的参数是**传引用**，成功返回 `true`，EOF 返回 `false`。

#### 字符串流（String Stream）

```cpp
#include <sstream>

// 从字符串读取
string line = "apple orange banana";
istringstream line_in(line);
string word;
while (line_in >> word) {
    cout << "\"" << word << "\"" << endl;
}
// 输出："apple" "orange" "banana"

// 也可以用来拼接字符串
ostringstream oss;
oss << "Hello " << 123;
string result = oss.str();   // "Hello 123"
```

#### 输出格式控制

**默认浮点数输出：**
```cpp
double a = 1.2345678;
double b = 0.00012345678;
double c = 1234567.8;

cout << a << endl;    // 1.23457（默认 6 位有效数字）
cout << b << endl;    // 0.000123457
cout << c << endl;    // 1.23456e+06（大的数用科学计数法）
```

**常用操纵器（Manipulators）：**

| 操纵器 | 作用 | 头文件 |
|--------|------|--------|
| `endl` | 换行并刷新 | `<iostream>` |
| `showpoint` / `noshowpoint` | 显示/隐藏小数点后尾随零 | `<iostream>` |
| `fixed` | 固定小数位数 | `<iostream>` |
| `scientific` | 科学计数法 | `<iostream>` |
| `setprecision(n)` | 设置精度（fixed 模式 = 小数位数） | `<iomanip>` |
| `setw(n)` | 设置列宽 | `<iomanip>` |
| `setfill(c)` | 设置填充字符 | `<iomanip>` |
| `left` / `right` | 左/右对齐 | `<iostream>` |

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double a = 1.2345678;
    double b = 12.0;

    cout << showpoint << b << endl;    // 12.0000
    cout << noshowpoint << b << endl;  // 12

    cout << fixed << setprecision(2);
    cout << a << endl;                 // 1.23

    cout << scientific;
    cout << a << endl;                 // 1.234568e+00

    // 表格输出
    cout << setfill('-');
    cout << left;
    cout << setw(10) << "Name" << setw(10) << "Age" << endl;
    cout << right;
    cout << setw(10) << "Peter" << setw(10) << 30 << endl;
    return 0;
}
```

> **setprecision 的行为：**
> - 默认模式：有效数字位数
> - `fixed` 模式：小数点后的位数
> - `scientific` 模式：小数点后的位数

### Part III: Recursion

#### 递归定义

递归就是**函数调用自身**。递归定义包含：
1. **Base Case（基本情况）** — 直接给出答案，不递归
2. **General/Recursive Case（递归情况）** — 用更小规模的自身来定义

**Factorial 的两种定义：**
```
迭代定义：n! = n × (n-1) × (n-2) × ... × 2 × 1
递归定义：n! = n × (n-1)!,  0! = 1
```

**递归三要素：**
1. 至少一个 base case
2. general case 必须最终能化简为 base case
3. base case 终止递归

#### 经典例子

**Factorial（阶乘）：**
```cpp
int factorial(int num) {
    if (num == 0)          // base case
        return 1;
    else                   // general case
        return num * factorial(num - 1);
}
```

**执行流程（factorial(3)）：**
```
factorial(3) = 3 * factorial(2)
                    = 2 * factorial(1)
                        = 1 * factorial(0)
                            = 1
                        = 1 * 1 = 1
                    = 2 * 1 = 2
                = 3 * 2 = 6
```

**Fibonacci 数列：**
```cpp
int fib(int num) {
    if (num < 2)            // base cases: F0 = 0, F1 = 1
        return num;
    else
        return fib(num-1) + fib(num-2);
}
```

> 注意：Fibonacci 递归有大量重复计算！例如 `fib(4)` 中 `fib(2)` 被计算了两次。时间复杂度 O(2ⁿ)。

**GCD（最大公约数）— 欧几里得算法：**
```cpp
int gcd(int x, int y) {
    if (y == 0)
        return x;
    else
        return gcd(y, x % y);
}
```

**Palindrome（回文检测）：**
```cpp
bool is_palindrome(string s) {
    if (s.length() < 2)                       // base case
        return true;
    else
        return (s[0] == s[s.length()-1]) &&
               is_palindrome(s.substr(1, s.length()-2));
}
```

**Tower of Hanoi（汉诺塔）：**
```cpp
void move(int n, char src, char des, char tmp) {
    if (n == 1) {
        cout << "Move disk from " << src << " to " << des << endl;
    } else {
        move(n-1, src, tmp, des);    // 将 n-1 个盘子从 src 移到 tmp
        move(1, src, des, tmp);       // 将最底下的盘子从 src 移到 des
        move(n-1, tmp, des, src);    // 将 n-1 个盘子从 tmp 移到 des
    }
}
```

**汉诺塔步数计算：**
```
T(n) = 2*T(n-1) + 1
T(1) = 1
解得：T(n) = 2ⁿ - 1
```

- n=64 时，需要 2⁶⁴-1 ≈ 1.6×10¹⁹ 步
- 手工每秒 1 步 → 需 5×10¹¹ 年
- 计算机每秒 10⁹ 步 → 仍需 500 年

#### 栈溢出（Stack Overflow）

- 每次函数调用需要额外内存（函数调用栈）
- 递归层数过多会导致**栈溢出**
- 无限递归肯定会栈溢出

#### 递归 vs 迭代

| 方面 | 递归 | 迭代 |
|------|------|------|
| 效率 | 较慢（函数调用开销） | 较快 |
| 内存 | 可能栈溢出 | 通常 O(1) |
| 代码简洁性 | 对分治问题更简洁 | 较冗长 |
| 适用范围 | 树、分治、回溯 | 线性问题 |

> 任何递归都可以用迭代实现，但递归有时让代码更易理解。

#### 教程问题

**1. Sum of Natural Numbers：**
```cpp
// 递归版
int sum(int n) {
    if (n == 1) return 1;
    return sum(n-1) + n;
}
```

**2. Largest Element in Array：**
```cpp
// 递归版
int largest(int arr[], int n) {
    if (n == 1) return arr[0];
    return max(largest(arr, n-1), arr[n-1]);
}
```

**3. Reversing a String：**
```cpp
// 递归版
string reverse(string s) {
    if (s.length() == 0) return "";
    return s[s.length()-1] + reverse(s.substr(0, s.length()-1));
}
```

---

## Module 8: Pointers, Dynamic Memory & Linked Lists

### Part I: Pointers

#### 内存地址

- 主存是一系列连续编号的内存单元
- 每个内存单元有唯一的**地址**
- 变量声明时会分配内存空间

```cpp
int i;    // 分配 4 字节
char c;   // 分配 1 字节

cout << &i << ' ' << &c;   // 输出地址（默认为十六进制）
```

#### `&` 运算符的两种含义

```cpp
// 1. 在表达式中 — address-of 运算符
int i;
cout << &i;   // 取 i 的地址

// 2. 在声明中 — 引用运算符
void swap(int &x, int &y);   // 传引用
```

#### 指针变量

- 指针是**存储地址的变量**
- 声明：`type * ptrName;`

```cpp
int  i = 10;
int *iPtr = &i;    // iPtr 存储 i 的地址

char  c = 'Q';
char *cPtr = &c;   // 每种类型对应自己的指针类型
```

```
内存示意图：
   i          iPtr
┌──────┐    ┌──────┐
│  10  │    │  ●───┼───► points to i
└──────┘    └──────┘
```

**指针类型必须匹配！**
```cpp
int *iPtr;
char c;
iPtr = &c;     // ❌ 编译错误！类型不匹配
```

**声明多个指针：**
```cpp
int *iPtr1, *iPtr2, *iPtr3;   // 每个变量前都要加 *
// int *iPtr1, iPtr2, iPtr3;  // iPtr2, iPtr3 是 int，不是指针！
```

#### 解引用运算符 `*`

```cpp
int x = 10, y;
int *ptr = &x;

y = *ptr;       // y = 10（读取 ptr 指向的值）
*ptr = 20;      // x = 20（修改 ptr 指向的值）

cout << x << ' ' << y;   // 20 10

ptr = &y;
(*ptr)++;        // 注意括号！(*ptr)++ 等价于 y++
// *ptr++ 等价于 *(ptr++)，移动指针而非增加值！
```

#### 成员访问运算符

```cpp
struct Date { int day, month, year; };
Date today;
Date *dPtr = &today;

// 三种等价方式访问成员：
today.year = 2015;          // 1. 点运算符
(*dPtr).year = 2015;        // 2. 解引用 + 点（括号不可少！）
dPtr->year = 2015;          // 3. 箭头运算符（推荐）

// 成员函数也类似
string s = "good day!";
string *sPtr = &s;
cout << sPtr->substr(0, 4) << endl;   // "good"
```

> `->` 是 `(*).` 的简写，专门用于指针访问成员。

#### 悬空指针（Dangling Pointer）

**未初始化的指针：**
```cpp
int *dangling_ptr;
cout << *dangling_ptr << endl;   // ❌ 未知结果！指向垃圾地址
```

#### 空指针（Null Pointer）

```cpp
int *ptr = nullptr;     // C++11 推荐用 nullptr
// int *ptr = 0;        // 或者 0
// int *ptr = NULL;     // C 风格，C++ 也支持

// 解引用空指针会崩溃！
// cout << *ptr << endl;   // ❌ 崩溃

// 使用前先检查
if (ptr != nullptr) {
    cout << *ptr << endl;
}
```

**常见错误检查：**
```cpp
Date today;
Date *dPtr;

cout << dPtr->month;    // ❌ 悬空指针

Date *dPtr = 0;
cout << dPtr->month;    // ❌ 空指针解引用 → 崩溃

Date *dPtr = 0;
if (dPtr != 0)
    cout << dPtr->month; // ✅ 安全
```

#### 指针与数组

**数组名是指向第一个元素的常量指针：**

```cpp
int a[10];
for (int i = 0; i < 10; ++i) a[i] = 2 * i;

int *p = a;               // p = &a[0]
// int *p = &a[0];        // 等价

for (int i = 0; i < 10; ++i)
    cout << p[i] << ' ';  // 可以用 p[i] 访问

int *q = &a[0];           // 等价于 q = a
for (int i = 0; i < 10; ++i)
    cout << *(q + i) << ' ';  // 指针算术

// a = p;                  // ❌ 数组名是常量，不可赋值！
```

**指针算术：**
```cpp
int a[5] = {0, 2, 4, 6, 8};
int *p = a;

p++;          // 现在指向 a[1]（地址 += 4 字节，int 大小）
p += 2;       // 现在指向 a[3]
```

#### 传引用方式对比

```cpp
// 方法 1：引用参数（C++ only）
void swap1(int &x, int &y) {
    int temp = x;
    x = y;
    y = temp;
}

// 方法 2：指针参数（C 和 C++）
void swap2(int *x, int *y) {
    int temp = *x;
    *x = *y;
    *y = temp;
}

int a = 2, b = 3;
swap1(a, b);       // 直接传变量
swap2(&a, &b);     // 传地址
```

### Part II: Dynamic Memory Management

#### 静态变量 vs 动态变量

**静态变量：**
- 数量固定
- 生命周期由作用域决定
- 有名字

**动态变量：**
- 运行时分配
- **没有名字**，必须通过指针访问
- 生命周期由程序员控制

#### `new` 和 `delete`

```cpp
// 单个变量
int *p = new int;       // 分配未初始化
*p = 42;
int *q = new int(42);   // 分配并初始化

delete p;               // 释放
delete q;
p = nullptr;            // 好习惯：置空防止悬空
```

**两个指针指向同一内存：**
```cpp
int *p1, *p2;
p1 = new int;
*p1 = 42;
p2 = p1;                // p2 与 p1 指向同一内存

*p2 = 53;               // 现在 *p1 也是 53
```

#### 内存泄漏（Memory Leak）

```cpp
int *p = new int(42);
// 忘记 delete p → 内存泄漏！程序退出前无法回收
```

**良好习惯：**
```cpp
int *p = new int(42);
// ... 使用 ...
delete p;
p = nullptr;    // 避免悬空指针
```

#### 常见指针错误

```cpp
// 1. 解引用未初始化指针
int *p;
*p = 88;        // ❌

// 2. 解引用悬空指针
int *p = new int;
*p = 88;
delete p;
cout << *p;     // ❌ 已释放

// 3. 重复删除
int *p = new int;
delete p;
delete p;       // ❌ 未定义行为

// 4. 内存泄漏
int *p = new int;
p = new int;    // ❌ 第一次分配的内存泄漏（无法再释放）
delete p;
```

#### 动态数组

```cpp
int n;
cin >> n;
int *a = new int[n];    // 运行时确定大小

for (int i = 0; i < n; ++i)
    a[i] = i;           // 像普通数组一样使用

delete[] a;             // 释放整个数组（注意 []）
```

#### 指针运算

- 指针加减的单位是**所指向类型的大小**
- 两个指针可以比较是否相等

```cpp
int a[5] = {0, 1, 2, 3, 4};
int *p = a, *q = a + 5;

cout << *(p + 1);       // a[1] = 1
// p + 1 的地址比 p 大 sizeof(int) = 4 字节

// 遍历指针
while (p != q) {
    cout << *p << ' ';
    ++p;
}
```

#### 教程：Phonebook Manager

`grow_phonebook()` 函数：
```cpp
void grow_phonebook(PhoneRec *&pb, int &pb_size, int n) {
    // Step 1: 创建新数组
    PhoneRec *pb_new = new PhoneRec[pb_size + n];

    // Step 2: 复制数据
    for (int i = 0; i < pb_size; i++)
        pb_new[i] = pb[i];

    // Step 3: 释放旧数组
    delete[] pb;

    // Step 4: 更新指针和大小
    pb = pb_new;           // pb 是引用，会更新 main 中的 phonebook
    pb_size = pb_size + n;
}
```

**策略讨论：**
- n 太大 → 浪费空间
- n 太小 → 频繁 grow，效率低（涉及数组拷贝）
- **常用策略：每次翻倍**

### Part III: Linked Lists

#### 数据访问模式

| 特性 | Array | Linked List |
|------|-------|-------------|
| 访问方式 | **随机访问**（直接 `arr[i]`） | **顺序访问**（必须从头遍历） |
| 插入/删除 | 需要移动大量元素（O(n)） | 只需修改指针（O(1)） |
| 内存 | 连续 | 分散 |

**插入到排序数组需要移动元素：**
```
data: [10, 12, 23, 35, 38, 56, 62, 76, 89, 92]
插入 15：
1. 增加容量（如需）
2. 将 23 及之后全部右移
3. 将 15 放入空位
→ 大量数据移动！
```

#### 结点定义

```cpp
struct Node {
    int data;
    Node *next;
};
```

#### 基本操作

**遍历：**
```cpp
void printList(Node *head) {
    Node *cur = head;
    while (cur != nullptr) {
        cout << cur->data << " ";
        cur = cur->next;
    }
}
```

**头部插入：**
```cpp
Node *insertHead(Node *head, int value) {
    Node *newNode = new Node{value, head};
    return newNode;   // 新结点成为 head
}
```

**尾部插入：**
```cpp
Node *insertTail(Node *head, int value) {
    Node *newNode = new Node{value, nullptr};

    if (head == nullptr) return newNode;

    Node *cur = head;
    while (cur->next != nullptr)
        cur = cur->next;
    cur->next = newNode;
    return head;
}
```

**删除结点：**
```cpp
Node *deleteNode(Node *head, int value) {
    if (head == nullptr) return nullptr;

    if (head->data == value) {
        Node *temp = head->next;
        delete head;
        return temp;
    }

    Node *cur = head;
    while (cur->next != nullptr && cur->next->data != value)
        cur = cur->next;

    if (cur->next != nullptr) {
        Node *temp = cur->next;
        cur->next = cur->next->next;
        delete temp;
    }
    return head;
}
```

---

## Module 9: STL Containers & Iterators

### STL 三大组件

| 组件 | 作用 | 例子 |
|------|------|------|
| **Containers（容器）** | 存储数据 | vector, list, map |
| **Iterators（迭代器）** | 访问容器中的元素 | `begin()`, `end()` |
| **Algorithms（算法）** | 操作容器中的数据 | sort, find, reverse |

### Part I: Containers

#### 为什么用 STL？

以电话本应用为例：
```cpp
class Record {
public:
    string name;
    int number;
};
```

- 数组大小固定 → 需要动态增长
- 自己实现链表 → 麻烦
- **STL 提供了高效且可复用的实现**

**class vs struct 回顾：**
```cpp
class Record {      // 成员默认 private
public:
    string name;
    int number;
};

struct Record {     // 成员默认 public
    string name;
    int number;
};
```

#### Vector（动态数组）

**特性：**
- 连续内存
- 随机访问 O(1)
- 尾部插入 O(1)（平均）
- 中间插入 O(n)

**基础用法：**
```cpp
#include <vector>
vector<string> names;     // 创建空 vector（模板类）
vector<int> numbers;
```

| 操作 | 用法 | 时间复杂度 |
|------|------|-----------|
| 尾部插入 | `v.push_back(item)` | O(1)* |
| 尾删除 | `v.pop_back()` | O(1) |
| 访问 | `v[i]` | O(1) |
| 大小 | `v.size()` | O(1) |
| 赋值 | `v1 = v2` | O(n) |

```cpp
// 电话本示例
vector<string> names;
vector<int> numbers;

names.push_back(name);
numbers.push_back(number);

for (int i = 0; i < names.size(); i++)
    if (names[i] == name)
        cout << numbers[i];
```

**Vector of user-defined types：**
```cpp
vector<Record> book;          // 创建 vector<Record>
book.push_back(r);            // 插入副本（复制 r）
// 修改 r 不影响 vector 内的元素
```

> `push_back()` 创建的是副本，之后修改原对象不影响容器内的元素。

**性能总结（已学知识点）：**

| 操作 | Vector |
|------|--------|
| push_back | O(1) 平均 |
| pop_back | O(1) |
| operator[] | O(1) |
| size() | O(1) |
| 中间插入/删除 | O(n) |

#### List（双向链表）

**特性：**
- 双向链表
- 头部/尾部插入 O(1)
- 不支持随机访问（没有 `[]`）

| 操作 | 用法 | 时间复杂度 |
|------|------|-----------|
| 尾部插入 | `l.push_back(item)` | O(1) |
| 尾部删除 | `l.pop_back()` | O(1) |
| 头部插入 | `l.push_front(item)` | O(1) |
| 头部删除 | `l.pop_front()` | O(1) |
| 访问头部 | `l.front()` | O(1) |
| 访问尾部 | `l.back()` | O(1) |
| 大小 | `l.size()` | O(1) |

**List 实现电话本搜索（环形遍历技巧）：**
```cpp
list<string> names;
list<int> numbers;

// 搜索
for (int i = 0; i < names.size(); i++) {
    if (names.front() == name) {
        cout << numbers.front();
        break;
    } else {
        // 将 front 移到 back，继续搜索
        names.push_back(names.front());
        names.pop_front();
        numbers.push_back(numbers.front());
        numbers.pop_front();
    }
}
```

#### Map（映射）

**特性：**
- 存储 `<key, value>` 键值对
- 基于**平衡二叉搜索树（红黑树）**
- 按键排序存储
- 搜索 O(log n)

**基础知识：平衡二叉搜索树**
- 每个结点最多两个子结点（二叉树）
- 左子树所有 key < 根 < 右子树所有 key（搜索树）
- 保持平衡以避免偏斜（平衡树）

**基础用法：**
```cpp
#include <map>
map<string, int> book;    // key: string, value: int
```

| 操作 | 用法 | 时间复杂度 |
|------|------|-----------|
| 插入/访问 | `m[key] = value` | O(log n) |
| 查找 | `m.count(key)` | O(log n) |
| 大小 | `m.size()` | O(1) |

```cpp
map<string, int> book;

// 插入
book[name] = number;       // 如果 key 存在则更新，不存在则创建

// 查找
if (book.count(name) > 0)  // 1 存在，0 不存在
    cout << book[name];     // 访问
else
    cout << "Not found" << endl;
```

> **⚠️ `m[key]` 的特性：** 如果 key 不存在，`m[key]` 会**自动创建**一个键值对并赋默认值！
> 因此检查是否存在应使用 `count()` 而不是 `m[key]`。

**Map of user-defined classes：**

需要重载 `<` 运算符！
```cpp
class Record {
public:
    string name;
    int number;
};

// 定义 Record 的 < 比较
bool operator<(const Record& a, const Record& b) {
    return a.name < b.name;
}

// 现在可以用作 map 的 key
map<Record, int> book;   // ✅
```

**为什么需要 `operator<`？**
- map 内部按 key 排序存储
- 需要知道如何比较 key
- 重载 `operator<` 时注意：传 const 引用、返回 bool、比较逻辑明确

#### Vector vs List vs Map 对比

| 操作 | Vector | List | Map |
|------|--------|------|-----|
| 尾部插入 | O(1) | O(1) | / |
| 尾部删除 | O(1) | O(1) | / |
| 头部插入 | / | O(1) | / |
| 头部删除 | / | O(1) | / |
| 按序插入 | / | / | O(log n) |
| 搜索 | O(n) | O(n) | **O(log n)** |
| 大小 | O(1) | O(1) | O(1) |

### Part II: Iterators

#### 基本概念

- Iterator 是 STL 用于索引容器中元素的**类**
- 类似于指针：可以用 `*` 解引用，可以用 `++` 移动
- 不同类型容器有不同类型的 iterator

**现实类比：iterator 像学生证号，解引用像叫这个学生出来。**

#### 声明 Iterator

```cpp
vector<string>::iterator itr;   // 用于 vector<string>
list<int>::iterator itr2;       // 用于 list<int>
```

> 不同类型的 iterator 不能混用！

#### 使用 Iterator

```cpp
vector<int> v;
v.push_back(3);
v.push_back(4);
v.push_back(5);

vector<int>::iterator itr = v.begin();
*itr = 100;              // 修改第一个元素为 100
cout << *itr << endl;    // 输出 100

itr++;                   // 移到下一个元素
```

- `c.begin()` — 返回指向**第一个元素**的 iterator
- `c.end()` — 返回指向**最后一个元素之后**的 iterator（不指向实际元素，用于判断终点）

```cpp
// 用 iterator 遍历
for (vector<int>::iterator itr = v.begin(); itr != v.end(); itr++) {
    cout << *itr << " ";
}
```

**Map 的 iterator 遍历：**
```cpp
map<int, double> input;
// ... 插入数据 ...

map<int, double>::iterator itr;
for (itr = input.begin(); itr != input.end(); itr++) {
    cout << (*itr).first << ' ' << (*itr).second << endl;
    // 或用 itr->first, itr->second
}
```

> Map 按 key 的升序遍历。

#### Iterator 的类型

| 类型 | 支持的操作 | 对应容器 |
|------|-----------|---------|
| **Forward Iterator** | 赋值 `a=b`，递增 `a++`，解引用 `*a`，判等 `a==b` | — |
| **Bidirectional Iterator** | Forward + 递减 `a--` | **list, map** |
| **Random Access Iterator** | Bidirectional + 算术 `a+5`, `a-b`, `a[5]`，不等 `a<b` | **vector** |

```cpp
// Random Access Iterator 额外支持：
vector<int>::iterator it = v.begin();
it + 5;              // 向后移动 5 个位置
it - 3;              // 向前移动 3 个位置
it += 2;             // 复合赋值
it[3];               // 等价于 *(it + 3)
v.end() - v.begin(); // 等于 v.size()
```

> 所有 iterator 操作都是 O(1)。

#### Iterator vs 指针

| 方面 | 指针 | Iterator |
|------|------|----------|
| 本质 | 内存地址 | 一个对象，存储位置信息 |
| ++ 操作 | 地址增加 sizeof(type) | 移到容器中的下一个元素（位置无关） |
| 设计目标 | 低级内存操作 | 高级抽象，避免直接操作内存 |

### Part III: Algorithms

使用算法需要 `#include <algorithm>`。

#### sort()

```cpp
#include <algorithm>

// 排序 vector
sort(v.begin(), v.end());                     // 升序

// 排序部分范围（第二个到倒数第二个）
sort(v.begin() + 1, v.end() - 1);

// 排序原生数组
int arr[10] = {/* ... */};
sort(arr, arr + 10);     // 指针被转为 random access iterator
```

> **⚠️ sort() 要求 Random Access Iterator！** 因此不能用于 list 或 map（只有 bidirectional iterator）。

**时间复杂度：** O(n log n) 平均

**排序自定义类型：**
```cpp
class Email {
public:
    string sender;
    string subject;
};

// 必须重载 <
bool operator<(const Email& a, const Email& b) {
    if (a.sender < b.sender) return true;
    if (a.sender > b.sender) return false;
    return a.subject.size() < b.subject.size();
}

sort(v.begin(), v.end());   // ✅ 现在可以了
```

**其他常用算法：**
```cpp
reverse(v.begin(), v.end());              // 反转
find(v.begin(), v.end(), target);         // 查找（返回 iterator）
binary_search(v.begin(), v.end(), val);   // 二分查找（需已排序）
min_element(v.begin(), v.end());          // 最小值位置
max_element(v.begin(), v.end());          // 最大值位置
count(v.begin(), v.end(), val);           // 计数
```

---

## Module 10: C Programming

### Part 1: printf() and scanf()

#### C 语言背景

| 方面 | 详情 |
|------|------|
| 设计者 | Dennis Ritchie（贝尔实验室，1970s） |
| 标准化 | ANSI C (1989) → ISO C89 → C95 → C99 → C11 |
| 与 C++ 关系 | C++ 是 C 的超集（**有效 C 程序基本都是有效 C++ 程序**，反之不然） |
| 编译 | `gcc -pedantic-errors -std=c11 program.c -o program` |

**为什么学 C？**
1. C 更底层，功能更少（无 class/STL），但通常**更快**
2. Linux/Unix 系统大量使用 C，操作系统和网络库很多只有 C 版本

#### Hello World in C

```c
#include <stdio.h>      // 标准 I/O（不是 <iostream>）

int main(void) {
    printf("Hello World!\n");   // 用 printf 而不是 cout
    return 0;
}
```

**与 C++ 的关键区别：**
- 没有 `namespace std`（C 没有 namespace）
- 没有 `cout`/`cin`/`endl`，用 `printf()`/`scanf()`/`\n`
- 没有 `string` 类，用 `char` 数组
- 头文件是 `.h` 后缀（如 `stdio.h`）

#### printf() 输出

**转换说明符（Conversion Specifiers）：**

| 类型 | 说明符 |
|------|--------|
| int | `%d` |
| float | `%f` |
| double | `%f`（注意！不是 `%lf`） |
| char | `%c` |
| string (char array) | `%s` |
| 去尾零 | `%g` |

```c
int a = 1, b = 2;
double c = -10.5;
printf("Input %d %d %f\n", a, b, c);
// 输出：Input 1 2 -10.500000

printf("Their sum = %g\n", a + b + c);
// 输出：Their sum = -7.5（%g 去掉尾零）
```

**%g 的作用：** 去掉浮点数末尾无意义的零。

> **⚠️ 错误说明符的后果：**
> ```c
> // 如果 c 是 double，却用 %d 显示：
> printf("%d %d %d\n", a, b, c);
> // → "1 2 0"（第三个值乱码/错误）
> ```
> C 是 primitive 的语言，说明符必须与类型严格匹配！

**宽度控制：**
```c
printf("%8s %8s %8s\n", "Name", "Age", "Weight");
//        Name      Age   Weight（右对齐，宽度 8）

printf("%8s %8d %8f\n", "Alan", 30, 40.25);
//      Alan       30 40.250000
```

#### scanf() 输入

**基本用法：**
```c
int a;
float b;
scanf("%d%f", &a, &b);    // 传入地址（用 &）
```

> **⚠️ 必须用 `&` 取地址！** 这是 C 风格的传引用方式，通过传递地址让 scanf() 能修改变量的值。

**读字符串到 char 数组：**
```c
char name[100];
scanf("%s", name);         // 不用 &！name 本身是地址
```

> 数组名本身是指向第一个元素的指针，所以不用 `&`。但 `int`、`double` 等普通变量必须用 `&`。

**局限性：** `scanf("%s")` 遇到空格或换行就停止。要读含空格的整行，用 `gets()` 或 `fgets()`。

#### 完整例子

```c
#include <stdio.h>

int main() {
    int a, b;
    float c;

    printf("Enter an int and a float: ");
    scanf("%d%f", &a, &c);

    printf("Input %d %d %g\n", a, b, c);
    printf("Product = %g\n", a * c);

    return 0;
}
```

### Part 2: C Basics

#### 变量和流程控制

**与 C++ 相同：**
- 数据类型：int, double, char
- 控制语句：if-else, for, while
- 语法基本一致

**C89 的限制：**
```c
// ❌ C89 不允许在 for 循环内声明变量
for (int i = 0; i < 10; i++)  // C89 编译错误

// ✅ C99 起允许
gcc -std=c99 program.c -o program
```

#### 函数 — 传值（Pass by Value）

```c
double sum(double a, double b) {
    return a + b;
}
// a, b 是调用者传入值的副本
```

#### 函数 — 传引用（Pass by Reference）

**C 只支持通过指针传引用：**
```c
// ❌ C 不支持引用参数
void swap2(double &a, double &b) { }   // C++ only

// ✅ C 用指针实现
void swap3(double *a, double *b) {
    double temp = *a;
    *a = *b;
    *b = temp;
}

// 调用时传地址
int main() {
    double x = 5.5, y = 10.5;
    swap3(&x, &y);    // & 取地址
    return 0;
}
```

**练习：GetSinCos**
```c
#include <stdio.h>
#include <math.h>
#define PI 3.14159265

// dSin 和 dCos 是通过指针传引用
void GetSinCos(int degree, double *dSin, double *dCos) {
    *dSin = sin(degree * PI / 180);
    *dCos = cos(degree * PI / 180);
}

int main() {
    int degree;
    double dSin, dCos;

    scanf("%d", &degree);
    GetSinCos(degree, &dSin, &dCos);

    printf("sin = %g\n", dSin);
    printf("cos = %g\n", dCos);
    return 0;
}
// 编译：gcc sincos.c -o sincos -lm（-lm 链接数学库！）
```

#### Array 与指针

```c
// 数组参数等价于指针参数
void salary_increase(int sal[]) {    /* ... */ }
// 等价于：
void salary_increase(int *sal) {
    for (int i = 0; i < 4; i++) {
        (*sal) = (*sal) * 1.1;
        sal++;    // 移到下一个元素
    }
}
```

> 数组传参的实质是传**指向第一个元素的指针**，所以表现为传引用。

#### C-String 函数

```c
#include <string.h>

char a[100], b[100];
strcpy(a, "Kit");       // 复制
strcmp(a, b) == 0       // 比较（相等返回 0）
strcat(a, b);           // 拼接（确保 a 有足够空间！）
strlen(a);              // 长度
```

**⚠️ 缓冲区溢出风险：**
```c
char a[4], b[4];        // 太小了！
strcpy(b, "David");     // 需要 6 个字符（含 \0）但只有 4 个 → 运行时错误
```

**大写转小写技巧：**
```c
if (a[i] >= 'A' && a[i] <= 'Z')
    a[i] = a[i] + 'a' - 'A';
```

### Part 3: Memory Allocation and Struct

#### malloc() 和 free()

**C 使用 `malloc()` 替代 C++ 的 `new`：**

| 功能 | C++ | C |
|------|-----|---|
| 分配 | `new int(42)` | `malloc(sizeof(int))` |
| 释放 | `delete p` | `free(p)` |
| 类型转换 | 不需要 | 需要 `(int*)` 强制转换 |
| 头文件 | — | `<stdlib.h>` |

```c
#include <stdlib.h>

int size;
scanf("%d", &size);

// 分配内存（标准写法）
int *a = (int*)malloc(size * sizeof(int));

if (a == NULL) {        // 分配失败检查
    printf("Allocation failed!\n");
    return 1;
}

// 使用...
a[0] = 42;

free(a);                // 释放
a = NULL;               // 好习惯
```

> `malloc()` 返回 `void*`，需要强制转换为需要的指针类型。

**释放规则：**
- 只能 `free()` 由 `malloc()` 返回的指针
- 释放非 `malloc()` 分配的内存会导致运行时错误

#### Struct in C

**C 的 struct 限制：**
- ❌ 没有成员函数
- ❌ 没有访问控制（全部 public）
- ❌ 没有构造/析构函数
- ✅ 只是数据的集合

```c
struct student {
    int uid;
    char name[50];
    double gpa;
};

// 必须写 struct 关键字
struct student s1;
s1.uid = 12345;

// 或使用 typedef 简写
typedef struct student Student;
Student s2;
```

**typedef 的好处：**
```c
// 定义时直接 typedef
typedef struct {
    int x;
    int y;
} Point;

Point p = {10, 20};     // 不用写 struct Point
```

**完整示例：Employee 管理**
```c
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

typedef struct {
    char name[100];
    char position[100];
    int salary;
} Employee;

// 设置函数（用指针）
void setEmployee(char n[], char p[], int sal, Employee *e) {
    strcpy(e->name, n);
    strcpy(e->position, p);
    e->salary = sal;
}

// 显示函数（传值）
void showInfo(Employee e) {
    printf("Name: %s\n", e.name);
    printf("Position: %s\n", e.position);
    printf("Salary: %d\n", e.salary);
}

int main() {
    int num;
    scanf("%d", &num);

    // 动态分配 Employee 数组
    Employee *employees = (Employee*)malloc(num * sizeof(Employee));

    char n[100], p[100];
    int sal;
    for (int i = 0; i < num; i++) {
        scanf("%s%s%d", n, p, &sal);
        setEmployee(n, p, sal, &employees[i]);  // 或 employees + i
    }

    for (int i = 0; i < num; i++)
        showInfo(employees[i]);

    free(employees);
    return 0;
}
```

**`->` vs `.`：**
- `e->name` — e 是指针时用箭头
- `e.name` — e 是普通变量时用点

#### Struct 构造函数（C++ only，非 C）

C++ 的 struct 可以有构造函数：

```cpp
struct Student {
    char* name;
    char* id;
    int age;

    // 构造函数
    Student(char* nm, char* d, int a) {
        name = nm;
        id = d;
        age = a;
    }
};

// 使用方式
Student s("Tim", "3035123456", 20);
Student* p = new Student("Tim", "3035123456", 20);
```

可以重载多个构造函数（函数重载），详情将在 COMP2396 学习。

---

## 编译命令速查

```bash
# C++11 程序
g++ -pedantic-errors -std=c++11 program.cpp -o program

# C11 程序
gcc -pedantic-errors -std=c11 program.c -o program

# C99 程序（允许 for 循环内声明变量）
gcc -std=c99 program.c -o program

# 需要链接数学库
gcc program.c -o program -lm
```

---

## 常见错误和陷阱总结

| 错误 | 说明 | 解决方法 |
|------|------|---------|
| 数组越界 | C/C++ 不检查边界 | 手动确保索引在 [0, size-1] |
| 内存泄漏 | `new`/`malloc` 后忘记释放 | 匹配每个 `new` 一个 `delete` |
| 悬空指针 | 释放后继续使用 | 释放后置 `nullptr`/`NULL` |
| 未初始化变量 | 局部变量含垃圾值 | 总是初始化 |
| scanf 忘记 & | `scanf("%d", a)` 而非 `&a` | 普通变量加 `&`，数组名不用 |
| C-String 溢出 | `strcpy`/`strcat` 到过小的数组 | 确保目标数组足够大 |
| 类型不匹配 | printf/scanf 用了错误的转换说明符 | 检查 `%d`/`%f`/`%s` 是否匹配 |
| 忘记结尾分号 | struct/class 定义后没加分号 | 加 `;` |
| 数组名赋值 | `arr = otherArr` | 数组名是常量，用循环或 `memcpy` |
| 指针加减混淆 | `*ptr++` vs `(*ptr)++` | `*ptr++` 移动指针，`(*ptr)++` 增加值 |
| 递归无限循环 | 没有 base case | 确保 base case 最终可达 |

---

*Made with ❤️ for COMP2113 / ENGG1340 — 涵盖所有课件知识点*
