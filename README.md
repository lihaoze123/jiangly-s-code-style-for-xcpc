# jiangly's code style for XCPC

本文是基于 [jiangly](https://codeforces.com/submissions/jiangly) 在 codeforces 上以往的提交记录，参考 [Menci's code style for OI](https://oi.men.ci/code-style-oi/) 编写的代码规范。

### 概览

*   **不应该**使用命名空间。(使用 `std::` 前缀代替)
*   **不应该**使用像 `LL` 或 `ULL` 这样的缩写作为类型别名。而是使用 `i64` 或 `u64`。
*   `main` 函数**应该**放在整个程序的末尾。
*   **应该**使用现代化的语法或数据类型，例如 lambda 函数，结构化绑定，`std::vector<std::vector<int>> a(n)` 代替 `int a[N]` 等。

### 预编译

*   `#include` 语句**必须**放在程序的开头。
*   **推荐**使用*万能头文件* (即 `#include <bits/stdc++.h>`)。
*   **不应该**使用 `#define` 或 `typedef` (`#define i64 long long`) 来为数据类型取别名，而**应该**使用 `using i64 = long long;`。
*   **应该**使用 `constexpr` 关键字定义常数。

### 缩进

*   对于每个代码块，**应该**使用 4 空格缩进。

### 空格及行

*   大括号**不应**换行。
*   多个意义独立的代码块之间**应该**用空行隔开。
*   二元运算符 (包括赋值运算符) 两侧**必须**有一个空格。
*   `if`, `for` 等控制流关键字与其后的左括号之间**必须**有一个空格。
*   `do-while` 中的 `while`、`if-else` 中的 `else` 与其前面的右大括号之间**必须**有一个空格。
*   双目运算符、三目运算符的两侧**必须**有一个空格。
*   逗号 `,` 与 `for` 中的分号 `;` 后面**必须**有一个空格。
*   在书写长表达式时，为了便于阅读，**可以**加一个空格 (e.g. `f[i][j] = f[ f[i][j - 1] ][j - 1]`)。
*   单目运算符的两侧**不能**有空格。
*   冒号的两侧**必须**有一个空格（除 `::`）。

### 命名

*   结构体、函数、变量、参数名**必须**使用驼峰命名法。
*   结构体首字母**必须**大写。
*   当问题中给定变量名时，**应该**优先使用。
*   命名中的单词**可以**使用缩写。函数、变量、参数名**可以**用一个小写字母代替。

一些常见名称：

*   a/b/c/m/n/p/q/t/s/v/val：变量的值，其中 a/b/c 常用于作为数组名，s, t 作为字符串。
*   i/j/k：只能用于循环结构中。
*   dp/ndp：动态规划中存储状态的数组。
*   cnt：计数。
*   siz：大小。
*   dir：方向。
*   ans/res：结果。
*   v: vector 数组
*   e: 边或元素
*   bel: belong 的简写

### 区间表示

*   **应该**使用左闭右开区间 `[l, r)` 表示区间，而非闭区间 `[l, r]`。
*   左闭右开区间的优势：
    *   计算区间长度简单：`r - l`。
    *   可以使用 `l == r` 表示空集。
    *   连续区间的端点自然重合，便于分治、线段树等算法实现。
    *   与 C++ 中下标从 0 开始的约定相符。
*   在实现以下算法时，左闭右开区间尤其有用：
    *   前缀和与差分：区间 `[l, r)` 的和为 `s[r] - s[l]`。
    *   二分查找：`lower_bound` 和 `upper_bound` 的实现更加统一。
    *   树状数组：区间查询和修改更加自然。
    *   线段树：递归时区间划分更加对称。

### 代码结构

jiangly 的代码通常遵循以下结构：

```cpp
#include <bits/stdc++.h>

using i64 = long long;
using u64 = unsigned long long;
using u32 = unsigned;
// 其他类型定义

// 常量定义
constexpr i64 inf = 1E18;

// 辅助函数定义

void solve() {
    // 读入数据
    int n;
    std::cin >> n;
    
    std::vector<int> a(n);
    for (int i = 0; i < n; i++) {
        std::cin >> a[i];
    }
    
    // 解题逻辑
    
    // 输出结果
    std::cout << ans << "\n";
}

int main() {
    std::ios::sync_with_stdio(false);
    std::cin.tie(nullptr);
    
    int t;
    std::cin >> t;
    
    while (t--) {
        solve();
    }
    
    return 0;
}
```

### 常用技巧

#### Lambda 函数

jiangly 经常使用递归 lambda 函数，特别是在树形结构的遍历中：

```cpp
auto dfs = [&](this auto &&self, int x, int p) -> void {
    // 处理当前节点
    for (auto y : adj[x]) {
        if (y == p) continue;
        self(y, x); // 递归调用
    }
};
dfs(0, -1);
```

#### 动态规划

动态规划中，jiangly 通常使用 `std::vector` 存储状态：

```cpp
std::vector dp(n, std::vector<i64>(n));
for (int r = 2; r < n; r++) {
    for (int l = r - 2; l >= 0; l--) {
        // 状态转移
        for (int i = l + 1; i < r; i++) {
            dp[l][r] = std::max(dp[l][r], dp[l + 1][i - 1] + dp[i + 1][r - 1] + a[l] * a[i] * a[r]);
        }
        for (int i = l; i < r; i++) {
            dp[l][r] = std::max(dp[l][r], dp[l][i] + dp[i + 1][r]);
        }
    }
}
```

#### 图论

在图论问题中，jiangly 使用邻接表表示图：

```cpp
std::vector<std::vector<int>> adj(n);
for (int i = 1; i < n; i++) {
    int u, v;
    std::cin >> u >> v;
    u--; // 0-indexed
    v--;
    adj[u].push_back(v);
    adj[v].push_back(u);
}
```

### 输入输出优化

jiangly 在每个程序开始都会进行输入输出优化：

```cpp
std::ios::sync_with_stdio(false);
std::cin.tie(nullptr);
```

这样可以显著提高输入输出速度，对于大规模数据的题目尤为重要。
