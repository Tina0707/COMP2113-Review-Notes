# COMP2113 / ENGG1340 综合复习笔记

## 目录

1. [Module 6: Arrays & Strings](#module-6-arrays--strings)
2. [Module 7: Structs, File I/O & Recursion](#module-7-structs-file-io--recursion)
3. [Module 8: Pointers, Dynamic Memory & Linked Lists](#module-8-pointers-dynamic-memory--linked-lists)
4. [Module 9: STL Containers & Iterators](#module-9-stl-containers--iterators)
5. [Module 10: C Programming](#module-10-c-programming)

---

## Module 6: Arrays & Strings

### Part I: Arrays

#### 什么是 Array？
- Array 是一个连续的 memory locations 组，所有元素共享相同的数据类型
- 用统一的名字加 index 来访问各个元素

```cpp
int score[80]; // 声明一个包含80个整数的数组
score[0] = 85; // 访问第一个元素
```

#### 声明和初始化
```cpp
int arr[5] = {1, 2, 3, 4, 5};  // 静态初始化
int arr[] = {1, 2, 3, 4, 5};   // 编译器自动推断大小
int arr[5] = {0};               // 全部初始化为0
int arr[5];                     // 未初始化，包含垃圾值
```

#### 传递数组给函数
- 数组传递给函数时**退化为指针**（decay to pointer）
- 数组本身是**传地址**（pass-by-reference 效果）

```cpp
// 方式1：指针参数
void printArray(int *arr, int size) {
    for (int i = 0; i < size; i++)
        cout << arr[i] << " ";
}

// 方式2：数组语法
void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++)
        cout << arr[i] << " ";
}
```

#### 搜索算法

**Linear Search（线性搜索）** - O(n)
```cpp
int linearSearch(int arr[], int size, int target) {
    for (int i = 0; i < size; i++)
        if (arr[i] == target) return i;
    return -1;
}
```

**Binary Search（二分搜索）** - O(log n)，要求数组已排序
```cpp
int binarySearch(int arr[], int size, int target) {
    int left = 0, right = size - 1;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (arr[mid] == target) return mid;
        else if (arr[mid] < target) left = mid + 1;
        else right = mid - 1;
    }
    return -1;
}
```

#### 排序算法

**Bubble Sort（冒泡排序）** - O(n²)
```cpp
void bubbleSort(int arr[], int size) {
    for (int i = 0; i < size - 1; i++)
        for (int j = 0; j < size - i - 1; j++)
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
```cpp
// 必须指定第二维的大小
void printMatrix(int mat[][4], int rows) {
    for (int i = 0; i < rows; i++) {
        for (int j = 0; j < 4; j++)
            cout << mat[i][j] << " ";
        cout << endl;
    }
}
```

### Part II: Char & Char Arrays (C-Strings)

#### Char 类型
```cpp
char ch = 'A';           // 单个字符
char ch = 65;            // ASCII 值，也是 'A'
cout << (int)ch;         // 输出 ASCII 值
```

#### C-String (字符数组)
- C-string 是以 `\0` (null character) 结尾的 char 数组
- 使用 `<cstring>` 库

```cpp
char str1[] = "hello";   // 自动加 \0，大小6字节
char str2[6] = "hello";  // 显式指定大小
char str3[] = {'h', 'e', 'l', 'l', 'o', '\0'}; // 手动加 \0
```

**常用 C-String 函数：**
| 函数 | 说明 |
|------|------|
| `strlen(str)` | 返回字符串长度（不含 \0） |
| `strcpy(dest, src)` | 复制字符串 |
| `strcat(dest, src)` | 拼接字符串 |
| `strcmp(str1, str2)` | 比较（相等返回0） |
| `strchr(str, ch)` | 查找字符首次出现位置 |

### Part III: C++ Strings

- 使用 `<string>` 库
- 更安全、更方便

```cpp
#include <string>
string s1 = "hello";
string s2 = "world";

// 常用操作
s1.length()                    // 长度
s1 + s2                        // 拼接
s1 == s2, s1 < s2              // 比较
s1.find("el")                  // 查找子串，返回 index
s1.substr(pos, len)            // 取子串
s1.c_str()                     // 转 C-string
getline(cin, s1)               // 读取一整行
```

---

## Module 7: Structs, File I/O & Recursion

### Part I: Structs

#### 什么是 Struct？
- 将多个不同类型的变量组合成一个单一类型
- 成员默认是 **public** 的

```cpp
struct Student {
    int uid;
    int assign1;
    int assign2;
    int quiz1;
    int quiz2;
    int final_marks;
    double total;
    char grade;
};

// 使用
Student s;
s.uid = 12345;
s.grade = 'A';
```

#### Struct 初始化
```cpp
Student s = {12345, 85, 90, 75, 80, 88, 0.0, 'A'};
```

#### 传递 Struct 给函数

**传值（Pass by Value）：** 复制整个 struct，开销大
```cpp
void printStudent(Student s) {
    cout << s.uid << " " << s.grade;
}
```

**传引用（Pass by Reference）：** 推荐，避免复制
```cpp
void printStudent(const Student &s) {
    cout << s.uid << " " << s.grade;
}
```

**传指针：**
```cpp
void printStudent(const Student *s) {
    cout << s->uid << " " << s->grade; // 用 -> 访问成员
}
```

#### typedef 简化类型名
```cpp
typedef struct {
    int x;
    int y;
} Point;

Point p;
p.x = 10;
p.y = 20;
```

#### Class vs Struct
- **struct**：成员默认 public
- **class**：成员默认 private

### Part II: File I/O

#### 头文件和打开模式
```cpp
#include <fstream>
#include <iostream>

ifstream fin;    // 输入文件流 (读)
ofstream fout;   // 输出文件流 (写)
fstream fio;     // 双向文件流
```

#### 读文件
```cpp
ifstream fin("input.txt");
if (!fin.is_open()) {
    cerr << "File not found!" << endl;
    return 1;
}

int data;
while (fin >> data) {   // 以空格/tab/换行为分隔
    // 处理 data
}

string line;
while (getline(fin, line)) {  // 按行读取
    // 处理 line
}

fin.close();
```

#### 写文件
```cpp
ofstream fout("output.txt");
fout << "Hello World!" << endl;
fout.close();
```

#### 文件打开模式
| 模式 | 说明 |
|------|------|
| `ios::in` | 打开读 |
| `ios::out` | 打开写（覆盖） |
| `ios::app` | 追加 |
| `ios::binary` | 二进制模式 |

#### 文件状态检查
```cpp
fin.good()   // 一切正常
fin.eof()    // 到达文件末尾
fin.fail()   // 操作失败
fin.bad()    // 严重错误
```

#### String Stream
```cpp
#include <sstream>

stringstream ss;
ss << "Hello " << 123;
string result = ss.str();  // "Hello 123"

// 解析字符串
string data = "123 456 789";
stringstream ss2(data);
int a, b, c;
ss2 >> a >> b >> c;  // a=123, b=456, c=789
```

### Part III: Recursion

#### 递归三要素
1. **Base Case（基本情况）** — 递归终止条件
2. **Recursive Case（递归情况）** — 函数调用自身
3. **Progress（进展）** — 每次调用向 base case 靠近

#### 经典例子

**Factorial（阶乘）：**
```cpp
int factorial(int n) {
    if (n <= 1) return 1;          // base case
    return n * factorial(n - 1);    // recursive case
}
```

**Fibonacci：**
```cpp
int fib(int n) {
    if (n <= 1) return n;          // base cases
    return fib(n - 1) + fib(n - 2); // recursive case
}
// 注意：此实现有大量重复计算，O(2^n)
```

**Tower of Hanoi（汉诺塔）：**
```cpp
void hanoi(int n, char src, char dest, char aux) {
    if (n == 1) {
        cout << "Move disk 1 from " << src << " to " << dest << endl;
        return;
    }
    hanoi(n - 1, src, aux, dest);
    cout << "Move disk " << n << " from " << src << " to " << dest << endl;
    hanoi(n - 1, aux, dest, src);
}
```

#### 递归 vs 迭代
| 方面 | 递归 | 迭代 |
|------|------|------|
| 代码简洁性 | 更简洁 | 较冗长 |
| 可读性 | 好（对分治问题） | 好 |
| 性能 | 可能较慢（函数调用开销） | 较快 |
| 内存 | 可能栈溢出 | 通常 O(1) |
| 适用场景 | 树、分治、回溯 | 线性问题 |

#### 尾递归优化
- 当递归调用是函数的最后一个操作时，编译器可以优化为循环

---

## Module 8: Pointers, Dynamic Memory & Linked Lists

### Part I: Pointers

#### 内存地址和指针
- 指针是**存储内存地址的变量**
- `&` = address-of 运算符
- `*` = dereference 运算符

```cpp
int x = 42;
int *p = &x;    // p 存储 x 的地址
cout << *p;     // 输出 42 (dereference)
*p = 100;       // x 变为 100
```

#### 指针和数组
- 数组名是指向第一个元素的指针（常量指针）

```cpp
int arr[5] = {10, 20, 30, 40, 50};
int *p = arr;           // 指向 arr[0]
cout << *(p + 2);       // 输出 30 (arr[2])
cout << p[2];           // 同样的效果
cout << 2[arr];         // 也有效！(不推荐)
```

#### 指针算术
```cpp
int arr[5] = {10, 20, 30, 40, 50};
int *p = arr;

p++;        // 移动到下一个 int (4字节)
p += 2;     // 向前移动 2 个 int
// p 现在指向 arr[3] = 40
```

#### Pass-by-Reference with Pointers
```cpp
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int x = 5, y = 10;
swap(&x, &y);  // x=10, y=5
```

#### 指针 vs 引用
| 指针 | 引用 |
|------|------|
| 可以为 `nullptr` | 必须初始化，不能为空 |
| 可以重新赋值 | 绑定后不能改变 |
| 需要 `*` 解引用 | 直接使用 |

#### 常见的指针错误
```cpp
// 错误1：未初始化指针
int *p;
*p = 42;  // 未定义行为！

// 错误2：空指针解引用
int *p = nullptr;
*p = 42;  // 崩溃！

// 错误3：悬空指针（dangling pointer）
int *p = new int(42);
delete p;
*p = 100;  // 已释放的内存！
```

### Part II: Dynamic Memory Management

#### C++ 的 new/delete
```cpp
// 单个变量
int *p = new int(42);
delete p;

// 数组
int *arr = new int[100];
delete[] arr;
```

#### 内存泄漏（Memory Leak）
- 用 `new` 分配的内存没有用 `delete` 释放
- 程序运行期间内存占用不断增加

```cpp
// 内存泄漏示例
void leak() {
    int *p = new int(42);
    // 忘记 delete p;
}

// 正确做法
void noLeak() {
    int *p = new int(42);
    delete p;
}
```

#### 内存碎片和内存池

**External Fragmentation（外部碎片）：**
- 多次分配和释放后，空闲内存被分割成许多小碎片
- 可能导致即使总空闲内存足够，也无法满足大块内存分配请求

**Memory Pool（内存池）：**
- 预分配一大块内存，然后在其中管理小对象分配
- 减少碎片，提高分配速度

### Part III: Linked Lists

#### 结点定义
```cpp
struct Node {
    int data;
    Node *next;
};

// 或使用 class
class Node {
public:
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

**插入（头部）：**
```cpp
Node *insertHead(Node *head, int value) {
    Node *newNode = new Node{value, head};
    return newNode;  // 新结点成为 head
}
```

**插入（尾部）：**
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

**删除：**
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

#### Array vs Linked List
| 操作 | Array | Linked List |
|------|-------|-------------|
| 随机访问 | O(1) | O(n) |
| 头部插入 | O(n) | O(1) |
| 尾部插入 | O(1)（有空间） | O(n)（无 tail 指针） |
| 删除 | O(n) | O(n)（含搜索） |
| 内存 | 连续，栈或堆 | 分散，堆 |
| 额外内存 | 少 | 每个结点多一个指针 |

---

## Module 9: STL Containers & Iterators

### Part I: Containers

#### STL 三大类
1. **Containers** — 存储数据的类
2. **Iterators** — 访问容器中的元素
3. **Algorithms** — 常用算法（排序、搜索等）

#### Vector（动态数组）
- 连续内存，支持随机访问
- 头部插入慢，尾部插入快

```cpp
#include <vector>
vector<int> v;

v.push_back(10);      // 尾部添加
v.pop_back();         // 尾部删除
v.size();             // 元素个数
v[i];                 // 随机访问（不检查边界）
v.at(i);              // 随机访问（检查边界，越界抛异常）
v.empty();            // 是否为空
v.clear();            // 清空
v.front();            // 第一个元素
v.back();             // 最后一个元素
```

#### List（双向链表）
- 非连续内存，不支持随机访问
- 任意位置插入/删除都快

```cpp
#include <list>
list<int> l;

l.push_back(10);
l.push_front(20);
l.pop_back();
l.pop_front();
l.size();
l.empty();

// 插入（需要迭代器）
list<int>::iterator it = l.begin();
advance(it, 2);       // 移动迭代器
l.insert(it, 100);    // 在 it 位置插入
l.erase(it);          // 删除 it 位置的元素
```

#### Map（映射 / 键值对）
- 基于红黑树（平衡二叉搜索树）
- 键唯一，自动按键排序

```cpp
#include <map>
map<string, int> phonebook;

phonebook["Alice"] = 12345678;   // 插入/更新
phonebook["Bob"] = 87654321;

cout << phonebook["Alice"];      // 查找（如果不存在会创建！）
cout << phonebook.at("Alice");   // 查找（不存在抛异常）

// 检查是否存在
if (phonebook.count("Alice") > 0) { ... }
if (phonebook.find("Alice") != phonebook.end()) { ... }

// 遍历
for (auto &pair : phonebook) {
    cout << pair.first << ": " << pair.second << endl;
}
```

#### Vector vs List vs Map
| 容器 | 随机访问 | 头部插入 | 尾部插入 | 按键查找 |
|------|---------|---------|---------|---------|
| Vector | O(1) | O(n) | O(1)* | N/A |
| List | O(n) | O(1) | O(1)** | N/A |
| Map | N/A | O(log n) | O(log n) | O(log n) |

\* 可能需要 reallocation
\*\* 如果有 tail 指针

### Part II: Iterators

#### 基本概念
- Iterator 是用于索引容器中元素的类
- 类似指针，可以用 `*` 解引用

```cpp
vector<int> v = {10, 20, 30, 40, 50};
vector<int>::iterator it = v.begin();

*it = 100;               // 修改第一个元素为 100
cout << *it;             // 读取当前元素
it++;                    // 移动到下一个元素
```

#### 遍历容器的三种方式

**方式1：迭代器**
```cpp
for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
    cout << *it << " ";
}
```

**方式2：范围 for 循环**
```cpp
for (int x : v) {
    cout << x << " ";
}
```

**方式3：索引（仅限 vector）**
```cpp
for (int i = 0; i < v.size(); i++) {
    cout << v[i] << " ";
}
```

#### begin() 和 end()
- `c.begin()` — 指向第一个元素的迭代器
- `c.end()` — 指向最后一个元素**之后**的迭代器（不指向任何实际元素）
- 通常用于循环终止判断

#### 常量迭代器
```cpp
vector<int>::const_iterator it = v.begin();
// *it = 100;  // 错误！不能修改
```

#### List 的迭代器特殊之处
- list 的迭代器不支持 `it + n` 或 `it += n`
- 只能使用 `++` 和 `--`
- 需要使用 `std::advance(it, n)` 来移动

#### Algorithms + Iterators 搭配使用
```cpp
#include <algorithm>

sort(v.begin(), v.end());                     // 排序
reverse(v.begin(), v.end());                  // 反转
find(v.begin(), v.end(), target);             // 查找（返回迭代器）
binary_search(v.begin(), v.end(), target);    // 二分查找
min_element(v.begin(), v.end());              // 最小值
max_element(v.begin(), v.end());              // 最大值
count(v.begin(), v.end(), value);             // 计数
```

---

## Module 10: C Programming

### Part 1: printf() and scanf()

#### C vs C++ 的主要区别
| C | C++ |
|---|-----|
| 面向过程 | 面向对象 |
| 无 class / STL | 有 class / STL |
| 手动内存管理 (malloc/free) | new/delete |
| 通常更快 | 更方便开发 |
| C 标准库 | C++ 标准库 |

#### 编译 C 程序
```bash
gcc -pedantic-errors -std=c11 program.c -o program
```

#### Hello World in C
```c
#include <stdio.h>

int main(void) {
    printf("Hello World!\n");
    return 0;
}
```

#### printf() 格式说明符
```c
printf("%d", 42);           // 整数 (int)
printf("%f", 3.14);         // 浮点数 (double)
printf("%c", 'A');          // 字符
printf("%s", "hello");      // 字符串
printf("%p", (void*)ptr);   // 指针
printf("%x", 255);          // 十六进制
printf("%.2f", 3.14159);    // 保留2位小数
printf("%5d", 42);          // 场宽5，右对齐
printf("%-5d", 42);         // 场宽5，左对齐
```

#### scanf() 格式说明符
```c
int x;
double y;
char str[100];

scanf("%d", &x);        // 读取整数（注意 &）
scanf("%lf", &y);       // 读取 double（%lf，不是 %f）
scanf("%s", str);       // 读取字符串（不用 &，str 已经是地址）
scanf("%d %lf", &x, &y); // 同时读取多个
```

**注意：** `scanf("%s", ...)` 不能读取带空格的字符串，遇到空格停止

### Part 2: C Basics

#### 变量声明（C99 放宽限制）
- **C89**：变量必须在代码块的开头声明
- **C99+**：可以在任意位置声明（类似 C++）

```bash
# 编译时指定标准
gcc -std=c99 program.c -o program
```

#### 函数

**传值调用（Pass by Value）：**
```c
int sum(int a, int b) {
    return a + b;
}
// 对 a, b 的修改不会影响调用者的变量
```

**传地址调用（Pass by Reference，通过指针模拟）：**
```c
void swap(int *a, int *b) {
    int temp = *a;
    *a = *b;
    *b = temp;
}

int x = 5, y = 10;
swap(&x, &y);  // x=10, y=5
```

#### C-String 函数 (`<string.h>`)
```c
#include <string.h>

char s1[20] = "hello";
char s2[20] = "world";

strlen(s1);              // 长度
strcpy(s1, s2);          // 复制 s2 到 s1
strcat(s1, s2);          // 拼接 s2 到 s1 末尾
strcmp(s1, s2);          // 比较
strncpy(s1, s2, n);      // 安全复制（指定最大长度）
strncat(s1, s2, n);      // 安全拼接
```

#### 数组作为函数参数
```c
void printArray(int arr[], int size) {
    for (int i = 0; i < size; i++)
        printf("%d ", arr[i]);
}

// 等价于
void printArray(int *arr, int size) {
    ...
}
```

### Part 3: Memory Allocation and Struct

#### malloc() 和 free()
```c
#include <stdlib.h>

// 分配内存
int *arr = (int*)malloc(size * sizeof(int));
if (arr == NULL) {
    // 内存分配失败
    return 1;
}

// 使用...
arr[0] = 42;

// 释放内存
free(arr);
arr = NULL;  // 避免悬空指针
```

| C | C++ |
|---|-----|
| `malloc(n)` | `new` |
| `free(p)` | `delete` / `delete[]` |
| 需要强制类型转换 | 不需要 |
| 需要 `sizeof` | 不需要 |
| 返回 `void*` | 返回正确类型 |

#### Struct in C
```c
struct Student {
    int uid;
    char name[50];
    double gpa;
};

// 使用
struct Student s1;
s1.uid = 12345;
strcpy(s1.name, "Alice");
s1.gpa = 3.8;

// typedef 简化
typedef struct {
    int x;
    int y;
} Point;

Point p = {10, 20};
```

#### Typedef 好处
1. 不需要写 `struct` 关键字
2. 代码更简洁
3. 便于统一修改类型

---

## 编译命令速查

```bash
# C++11
g++ -pedantic-errors -std=c++11 program.cpp -o program

# C11
gcc -pedantic-errors -std=c11 program.c -o program

# C99 (for loop 内部声明变量)
gcc -std=c99 program.c -o program
```

---

## 常见错误和陷阱

1. **数组越界**：C/C++ 不检查数组边界
2. **内存泄漏**：`new`/`malloc` 后忘记 `delete`/`free`
3. **悬空指针**：释放后继续使用指针
4. **未初始化变量**：局部变量不会自动初始化
5. **scanf 忘记 &**：读取基本类型时忘记取地址
6. **C-String 缓冲区溢出**：确保字符数组足够大
7. **递归无限循环**：没有 base case 或 base case 永远无法到达
8. **文件未关闭**：用完文件记得 `.close()`

---

*Made with ❤️ for COMP2113 / ENGG1340 Review*
