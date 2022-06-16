---
title: test-md-format
permalink: /blog/test-md-format/
categories: test
tags: test
toc: true
date: 2022-05-01 22:18:19
updated: 2022-05-25 07:21:50
---

这是一篇用于测试 markdown 高亮与数学公式渲染的文章。

<!-- more -->

![](/img/wp-1.jpg)_壁纸_

# Format Test Page

{% aplayer "1919180" "114514" "https://114514.uno/xianbei.mp3" "/img/114514/114514.png" %}

## formula

$$
f(x) = x ^ 2
$$

$$
\begin{array} [cc]\\\\
&\mathtt{mathrm}   &\mathrm{ABCDEabcde1234}\\\\
&\mathtt{mathit}   &\mathit{ABCDEabcde1234}\\\\
&\mathtt{mathbf}   &\mathbf{ABCDEabcde1234}\\\\
&\mathtt{mathsf}   &\mathsf{ABCDEabcde1234}\\\\
&\mathtt{mathtt}   &\mathtt{ABCDEabcde1234}\\\\
&\mathtt{mathcal}  &\mathcal{ABCDEabcde1234}\\\\
&\mathtt{mathscr}  &\mathscr{ABCDEabcde1234}\\\\
&\mathtt{mathfrak} &\mathfrak{ABCDEabcde1234}\\\\
&\mathtt{mathbb}   &\mathbb{ABCDEabcde1234}\\\\
\end{array}
$$

$$
\large P = \frac{\displaystyle{
\sum_{i=1}^n (x_i- x)
(y_i- y)}}
{\displaystyle{\left[
\sum_{i=1}^n(x_i-x)^2
\sum_{i=1}^n(y_i- y)^2
\right]^{1/2}}}\\
$$

$$
\color{black}{黑色},\color{red}{红色},\color{green}{绿色},\color{brown}{棕色},\color{pink}{粉色}\\
\color{white}{白色},\color{blue}{蓝色},\color{red}{红色},\color{yellow}{黄色},\color{gray}{灰色}\\
\color{orange}{橘色},\color{teal}{靛青},\color{darkgray}{深灰},\color{olive}{橄榄},\color{violet}{紫罗兰}\\
\color{lime}{青柠},\color{lightgray}{淡灰},\color{cyan}{青色},\color{magenta}{品红},\color{purple}{紫色}
,\color{silver}{银色}
$$

$$
\large \underset{j=1}{\overset{\infty}{\LARGE \mathrm K}}\frac{a_j}{b_j}
=\cfrac{a_1}{b_1
+\cfrac{a_2}{b_2
+\cfrac{a_3}{b_3
+\cfrac{a_4}{b_4
+\ddots}}}}\\
$$

$$
\large
\cfrac{a_{1}}{b_{1}+\cfrac{a_{2}}{b_{2}+\cfrac{a_{3}}{b_{3}+\ddots }}}
={\genfrac{}{}{}{}{a_1}{b_1}}  
{\genfrac{}{}{0pt}{}{}{+}}   
{\genfrac{}{}{}{}{a_2}{b_2}}   
{\genfrac{}{}{0pt}{}{}{+}}   
{\genfrac{}{}{}{}{a_3}{b_3}}   
{\genfrac{}{}{0pt}{}{}{+\dots}}\\
$$

## tags

<div class="primary">

primary

</div>

<div class="secondary">

> secondary

</div>

<div class="success">

> success

</div>

<div class="danger">

> danger

</div>

<div class="warning"> 

> warning

</div>

<div class="info"> 

> info

</div>

<div class="gray">

> gray

</div>

<div class="yellow">

> yellow

</div>

## code
```go
package main

import "fmt"

func main() {
  ch := make(chan float64)
  ch <- 1.0e10    // magic number
  x, ok := <- ch
  defer fmt.Println(`exitting now\`)
  go println(len("hello world!"))
  return
}
```

<details>
<summary> C++ —— The best language! </summary>

```cpp
#include <bits/stdc++.h>

using i64 = long long; // <+>

void solve() {
  int n;
  std::cin >> n;
  for (int i = n; i > 0; --i) {
    std::cout << i << " \n"[i == 1];
  }
}

int main() {
  std::cin.tie(nullptr)->sync_with_stdio(false);

  int tt = 1;
  std::cin >> tt;
  while (tt --) {
    solve();
  }

  return 0 ^ 0;
}
```
</details>