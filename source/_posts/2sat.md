---
title: 2SAT
categories: algo
permalink: /algo/graph/2sat/
date: 2022-06-16 15:53:44
tags: 
 - 图论
 - 2SAT
 - 缩点
 - Tarjan
 - 拓扑序
 - 建图
---

<div class="info">
> $\rm SAT$ 问题指的是，给定多个不定长的布尔量之合取（称为 **“子句”**），求出满足子句之析取的一组可满足的解或报告无解。
</div>

这是第一个被提出的 **NPC** 问题，意味着若高效解决此问题就能完成对 $\rm P = NP$ 的证明。

$\rm 2SAT$ 问题中每一个子句长都为 $2$。

<!-- more -->

# 2SAT

核心逻辑是：

$$s \rightarrow t \Leftrightarrow \neg s \lor t$$

<div class="gray">
其他形式的逻辑表达式均可转化为子句。

> $s \lor t \Leftrightarrow (\neg s \rightarrow t) \land (\neg t \rightarrow s)$，恰为一组反图上对应的反边
>
> $s = 1 \Leftrightarrow s \lor s$
>
> $s = 0 \Leftrightarrow \neg s \lor \neg s$
</div>

按照上述方式建出一张图之后，若链上一点成立，那么整条链都是成立的（这意味着可以直接搜环得到答案）。除非 $u$ 与 $\neg u$ 在同一个 $\rm SCC$ 中，这也是无解的唯一情形。

要输出可满足的方案，选择 $u$ 与 $\neg u$ 拓扑序靠后的点，否则 $u \rightarrow \neg u$ 导致不能自洽，反之亦然。

反映到 $\rm Tarjan$ 算法上，关注 $\rm SCC$ 编号即可，相当于反向拓扑排序（栈）。

 - 复杂度：$\mathcal O(n + m)$

## 模板

 - [HDU](https://acm.hdu.edu.cn/showproblem.php?pid=3062)
 - [Acwing](https://www.acwing.com/problem/content/description/2404/)
 - [Luogu](https://www.luogu.com.cn/problem/P4782)

<details> <summary> 模板参考 </summary>

```cpp
#include <bits/stdc++.h>

using i64 = long long;  // <+>

static const int N = int(1E6 + 7) << 1; // double for inverse

struct Edge {
  int next, to;
} e[N];

int head[N], idx;

void add(int u, int v) { e[++idx] = {head[u], v}; head[u] = idx; }

int stk[N], top = 0;
int dfn[N], low[N], id[N], ts, cnt;
bool ins[N];

void tarjan(int u) {
  dfn[u] = low[u] = ++ts;
  stk[++top] = u, ins[u] = 1;
  for (int i = head[u]; i; i = e[i].next) {
    const int& to = e[i].to;
    if (!dfn[to]) {
      tarjan(to);
      low[u] = std::min(low[u], low[to]);
    } else if (ins[to]) {
      low[u] = std::min(low[u], dfn[to]);
    }
  }

  if (int y; low[u] == dfn[u]) {
    cnt ++;
    do {
      y = stk[top--], ins[y] = 0, id[y] = cnt;
    } while (y != u);
  }
}

int main() {
  int n, m;
  scanf("%d%d", &n, &m);
  for (int i = 0; i < m; ++i) {
    int u, v, x, y;
    scanf("%d%d%d%d", &u, &x, &v, &y);
    u -= 1, v -= 1;
    add(u << 1 | !x, v << 1 | y);
    add(v << 1 | !y, u << 1 | x);
  }

  for (int i = 0; i < n * 2; ++i) {
    if (!dfn[i]) {
      tarjan(i);
    }
  }

  for (int i = 0; i < n; ++i) {
    if (id[i << 1] == id[i << 1 | 1]) {
      return puts("IMPOSSIBLE"), 0;
    }
  }

  puts("POSSIBLE");
  for (int i = 0; i < n; ++i) {
    printf("%d%c", id[i << 1] >= id[i << 1 | 1], " \n"[i == n - 1]);
  }

  return 0 ^ 0;
}
```

</details>

好像挺有必要封装一份的，个人感觉用 `std::optional` 未必能更简洁。

<details> <summary class="danger"> 封装好的版本 </summary> 

```cpp
#include <bits/stdc++.h>

using i64 = long long; // <+>

template <class Fun> struct y_combinator_result {
  Fun fun_;
  template <class T>
  explicit y_combinator_result(T &&fun) : fun_(std::forward<T>(fun)) {}
  template <class... Args> decltype(auto) operator()(Args &&...args) {
    return fun_(std::ref(*this), std::forward<Args>(args)...);
  }
};

template <class Fun> decltype(auto) y_combinator(Fun &&fun) {
  return y_combinator_result<std::decay_t<Fun>>(std::forward<Fun>(fun));
}

struct TwoSat {
  int n;
  std::vector<std::vector<int>> e;
  std::vector<bool> ans;

  TwoSat(int _n) : n(_n * 2), e(_n * 2), ans(_n) {};
  
  void addClause(int u, bool x, int v, bool y) {
    e[u << 1 | !x].push_back(v << 1 | y);
    e[v << 1 | !y].push_back(u << 1 | x);
  }

  const std::vector<bool>* run() {
    std::vector<int> dfn(n), low(n), id(n), stk;
    std::vector<bool> ins(n);
    int ts = 0, cnt = 0, y = 0;
    
    auto tarjan = y_combinator([&](auto tarjan, int u) -> void {
      dfn[u] = low[u] = ++ ts;
      stk.push_back(u), ins[u] = 1;
      for (int &v : e[u]) {
        if (!dfn[v]) {
          tarjan(v);
          low[u] = std::min(low[u], low[v]);
        } else if (ins[v]) {
          low[u] = std::min(low[u], dfn[v]);
        }
      }
      if (dfn[u] == low[u]) {
        cnt ++;
        do {
          y = stk.back();
          stk.pop_back();
          ins[y] = 0, id[y] = cnt;
        } while (y != u);
      }
    });
    
    for (int i = 0; i < n; ++i) {
      if (!dfn[i]) {
        tarjan(i);
      }
    }
    
    for (int i = 0; i < n / 2; ++i) {
      if (id[i << 1] == id[i << 1 | 1]) {
        return nullptr;
      }
      ans[i] = id[i << 1] > id[i << 1 | 1];
    }
    
    return & ans;
  }
};

int main() {
  int n, m;
  scanf("%d%d", &n, &m);

  TwoSat ts(n);

  for (int i = 0; i < m; ++i) {
    int u, v, x, y;
    scanf("%d%d%d%d", &u, &x, &v, &y);
    ts.addClause(u - 1, x, v - 1, y);
  }
  
  auto ans = ts.run();
   
  if (ans != nullptr) {
    puts("POSSIBLE");
    for (bool i : * ans) {
      printf("%d ", i);
    }
  } else {
    puts("IMPOSSIBLE");
  }

  return 0 ^ 0;
}
```
</details>

<div class="gray">
> 笑死了，$\rm Acwing$ 这题是不是卡 `std::vector` 啊，半天过不去。
</div>

----

直接爆搜找环的代码可以被卡到 $\mathcal O(n m)$， 就不放了。

此外还有一个模板题：

 - [Luogu](https://www.luogu.com.cn/problem/P4171)
    - 最后这个题稍微注意一下读入即可，附上一段参考代码：

```cpp
std::string a, b;    
std::cin >> a >> b;

int u = std::stoi(a.substr(1)) - 1, v = std::stoi(b.substr(1)) - 1;
bool x = a[0] == 'm', y = b[0] == 'm';

ts.addClause(u, x, v, y);
```

----

## 2018 首尔 TV Show Game

 - [TV Show Game](https://codeforces.com/gym/101987/problem/K)

### 题意

$k$ 盏灯或红或蓝，现有 $n$ 个人，每个人选择 $3$ 盏灯并猜测其颜色。猜中至少两盏灯者可以赢取奖品，问是否有一种灯色安排方式使得所有人均获奖？构造合法解或报告不可能。

### 解答

问题的关键是 “至少” 两盏灯，我们直接对猜测的三个命题 $a, b, c$ 建边 $a \lor b, b \lor c, a \lor c$ 随后跑模板即可。

下面的代码中，我选取 `x == 'R'` 作为 “真”，这意味着输出构造时也需要认定 `'R'` 为 `id[i * 2] >= id[i * 2 + 1]` 对应的解才可。

```cpp
for (int i = 0; i < 3; ++i) {
  for (int j = i + 1; j < 3; ++j) {
    auto [u, x] = guess[i];
    auto [v, y] = guess[j];
    ts.addClause(u, x == 'R', v, y == 'R');
  }
}
```

## 牧师约翰这天很忙

 - [POJ](http://poj.org/problem?id=3683)
 - [Acwing](https://www.acwing.com/problem/content/373/)
 
### 题意

<++>

### 解答
 
<++>

<details> <summary> 展开参考代码 </summary>

别问，问就是 POJ 远古 OJ。

```cpp
#include <cstdio>
#include <algorithm>

static const int N = 7 + 2000, M = N * N;

struct edge { int next, to; } e[M];
int head[N], idx;

void add(int u, int v) {
  // e[++ idx] = (edge) { head[u], v };
  e[++ idx].next = head[u];
  e[idx].to = v;
  head[u] = idx;
}

void addClause(int u, bool x, int v, bool y) {
  add(u << 1 | !x, v << 1 | y);
  add(v << 1 | !y, u << 1 | x);
}

int stk[N], top;
int low[N], dfn[N], id[N], cnt, ts;
bool ins[N];

void tarjan(int u) {
  dfn[u] = low[u] = ++ ts;
  stk[++ top] = u, ins[u] = 1;
  for (int i = head[u]; i; i = e[i].next) {
    int &to = e[i].to;
    if (!dfn[to]) {
      tarjan(to);
      low[u] = std::min(low[u], low[to]);
    } else if (ins[to]) {
      low[u] = std::min(low[u], dfn[to]);
    }
  }
  if (low[u] == dfn[u]) {
    int y;
    ++ cnt;
    do {
      y = stk[top --], ins[y] = 0, id[y] = cnt;
    } while (y != u);
  }
}

struct timeline {
  int s, t, d;
  void input() {
    int h1, m1, h2, m2, du;
    scanf("%d%*c%d %d%*c%d %d", &h1, &m1, &h2, &m2, &du);
    // *this = (timeline) { h1 * 60 + m1, h2 * 60 + m2, du };
    s = h1 * 60 + m1;
    t = h2 * 60 + m2;
    d = du;
  }
  void output(bool chooseLatter) {
    if (chooseLatter) {
      printf("%02d:%02d %02d:%02d\n", (t - d) / 60, (t - d) % 60, t / 60, t % 60);
    } else {
      printf("%02d:%02d %02d:%02d\n", s / 60, s % 60, (s + d) / 60, (s + d) % 60);
    }
  }
} go [N];

bool intersect(int s1, int t1, int s2, int t2) {
  return t2 > s1 && s2 < t1;
}

int main() {
  int n;
  scanf("%d", &n);
  
  for (int i = 0; i < n; ++i) {
    go[i].input();
  }
  
  for (int i = 0; i < n; ++i) {
    for (int j = 0; j < i; ++j) {
      int s1 = go[i].s, t1 = go[i].t, d1 = go[i].d;
      int s2 = go[j].s, t2 = go[j].t, d2 = go[j].d;
      if (intersect(s1, s1 + d1, s2, s2 + d2)) { addClause(i, 1, j, 1); }
      if (intersect(s1, s1 + d1, t2 - d2, t2)) { addClause(i, 1, j, 0); }
      if (intersect(t1 - d1, t1, s2, s2 + d2)) { addClause(i, 0, j, 1); }
      if (intersect(t1 - d1, t1, t2 - d2, t2)) { addClause(i, 0, j, 0); }
    }
  }
  
  for (int i = 0; i < n * 2; ++i) {
    if (!dfn[i]) {
      tarjan(i);
    }
  }
  
  for (int i = 0; i < n; ++i) {
    if (id[i * 2] == id[i * 2 + 1]) {
      return 0 * puts("NO");
    }
  }
  
  puts("YES");
  for (int i = 0; i < n; ++i) {
    go[i].output(id[i * 2] >= id[i * 2 + 1]);
  }
  
  return 0;
}
```

</details>

## [NOI2017] 游戏

 - [Luogu](https://www.luogu.com.cn/problem/P3825)
 - [Acwing](https://www.acwing.com/problem/content/1034/)
 
### 题意

<++>

### 解答
 
<++>

<details> <summary> 展开参考代码 </summary>

<++>

</details>

## [JSOI2010] 满汉全席

 
### 题意

<++>

### 解答
 
<++>

<details> <summary> 展开参考代码 </summary>

<++>

</details>

## [POI2001] 和平委员会

 - [Luogu](https://www.luogu.com.cn/problem/P5782)
 
### 题意

<++>

### 解答
 
<++>

<details> <summary> 展开参考代码 </summary>

<++>

</details>
