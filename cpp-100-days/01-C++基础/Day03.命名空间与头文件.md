# Day 03 · 命名空间与头文件

## 今日目标
- 理解什么是命名空间、为什么需要它
- 学会使用 `std::` 前缀和 `using` 声明
- 了解 C++ 标准库头文件的命名约定

## 学习内容

### 为什么需要命名空间

一个大型程序里可能有成千上万个名字：函数名、变量名、类名。如果大家都把名字放在"全局空间"，很容易撞车——你写了个函数叫 `sort`，别人也写了个 `sort`，编译器就懵了。

命名空间（namespace）就是给名字套一层"文件夹"：把相关的名字归到一个篮子里，使用时用 `篮子::名字` 来引用，避免冲突。

### std 命名空间

C++ 标准库的所有名字都放在 `std`（standard）这个命名空间里。所以前几天我们写的是 `std::cout`、`std::cin`，而不是直接写 `cout`。

```cpp
#include <iostream>

int main() {
    std::cout << "Hello\n";   // 完整写法
}
```

### using 声明：偷懒的两种方式

每次都写 `std::` 有点烦，可以用 `using` 把名字引入当前作用域：

```cpp
#include <iostream>

using std::cout;      // 只引入 cout 这一个名字
using std::cin;

int main() {
    int x;
    cin >> x;
    cout << x << '\n';
}
```

也可以直接 `using namespace std;` 把整个 std 全部倒进来：

```cpp
#include <iostream>
using namespace std;    // 初学者写小程序方便，但工程代码不推荐

int main() {
    cout << "hi\n";
}
```

| 写法 | 适用场景 |
|---|---|
| `std::cout` | 工程代码、头文件，最稳妥 |
| `using std::cout;` | cpp 文件里频繁用到某几个名字 |
| `using namespace std;` | 刷题、写小程序图方便，不建议写在头文件里 |

### 自定义命名空间

你也可以自己造命名空间：

```cpp
#include <iostream>

namespace mylib {
    int add(int a, int b) { return a + b; }
}

int main() {
    std::cout << mylib::add(1, 2) << '\n';   // 用 :: 访问
}
```

命名空间可以嵌套：`mylib::math::add(...)`。

### 头文件的约定

- C 风格头文件：`#include <stdio.h>`（在 C++ 里仍可用，但不推荐）；
- C++ 风格头文件：`#include <iostream>`、`#include <vector>`，**没有 `.h` 后缀**；
- 把 C 头文件转为 C++ 风格时，加个 `c` 前缀、去掉 `.h`：`stdio.h` → `cstdio`。

## 核心重点
- 命名空间用来避免名字冲突，标准库全部在 `std` 里
- `::` 是作用域解析符，用来访问命名空间或类里的名字
- `using std::cout;` 只引入单个名字；`using namespace std;` 一把全引入
- 工程代码优先写 `std::`，刷题小程序可图方便用 using
- C++ 标准头文件无 `.h` 后缀

## 练习/任务
1. 自己定义一个命名空间 `circle`，里面写一个求面积的函数，在 main 里调用
2. 在一个文件里分别用三种方式输出一句话：完整 `std::cout`、`using std::cout`、`using namespace std`
3. 定义嵌套命名空间 `outer::inner`，里面放一个变量，main 中访问它
4. （选做）故意在全局和命名空间里各定义一个同名函数，观察编译器如何区分

## 打卡清单
- [ ] 已理解命名空间解决什么问题
- [ ] 已会写 `namespace xxx { ... }`
- [ ] 已分清 std::、using std::、using namespace std 三种写法
- [ ] 已知道 C++ 头文件不带 .h
