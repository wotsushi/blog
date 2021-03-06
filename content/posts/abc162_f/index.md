---
title: AtCoder Beginner Contest 162 F - Select Half
date: 2020-04-15
categories:
  - 競プロ
tags:
  - AtCoder
  - 動的計画法
---

# 問題情報
- 問題文: https://atcoder.jp/contests/abc162/tasks/abc162_f

# 観察
入力例1 ($A = (1, 2, 3, 4, 5, 6)$) について考える。
全体で3個選ぶ必要があるので、1を選ばない場合、必ず2を選ぶ必要がある。
仮に2を選んだとしよう。隣接する3は選べない。
残り2個選ぶ必要があるので必ず4を選ぶ必要がある。同様に6も選ぶ必要がある。

最初に1を選んだ場合、$(3, 4, 5, 6)$ から残り2個選ぶ必要がある。
同様に、3を選べば $(5, 6)$ から1個選ぶ必要があり、 4を選べば6を選ぶしかない。

これらの選べる組合せのうち、和を最大化するのは（当然だが）2, 4, 6を選んだ場合である。

# 考察
観察より、（だいたい）先頭の2要素から1要素選ぶ行為を繰り返すことになる。
偶数ならば2要素のうち後者を選ぶと、選べる要素は一意に定まるので初めて後者を選ぶ位置を全探索すれば解けそうである。
しかし $N$ が奇数の場合、選ぶ組合せが多くなりそうである（自由度が高そう）。

観察を振り返ってみよう。
入力例1で、最初に1を選んだ場合、$(3, 4, 5, 6)$ から残り2個選ぶ必要があり、
最初に2を選んだ場合、$(4, 5, 6)$ から残り2個選ぶ必要があった。
この部分列から $k$ 個選ぶ問題は元の問題の部分問題とみなせそうである。
つまり、動的計画法で解けそうである。

素直に部分列の長さと選ぶ残り個数の組を状態とする。
選ぶ残り個数としてとれる状態はごく僅かしかなさそうである。
例えば部分列の長さが5のとき、選ぶ残り個数は2または3しかない。
部分列の長さが4のとき、選ぶ残り個数は2しかない。
一般化すると、部分列の長さを $L$ とすると選ぶ個数は $\lfloor L / 2 \rfloor$ または $\lceil L / 2 \rceil$ の2通りである。
すなわち、$O(N)$ の状態空間となる。
あとは部分問題から順に解くだけである。

漸化式を立式するため、 $(A_1, \ldots, A_i)$ から $j$ 個選ぶときの和の最大値を $f_{i, j}$ で表すことにする。
$i = \lfloor j / 2 \rfloor$ ならば $A_i$ を選んでも選ばなくともよい。
- 選ぶほうが最適な場合: $f_{i, j} = f_{i - 2, j - 1} + A_i$
- 選ばないほうが最適な場合: $f_{i, j} = f_{i - 1, j}$

が成り立つ。
そうでなければ、$A_i$ を選ぶしかないので、 $f_{i, j} = f_{i - 2, j - 1} + A_i$ が成り立つ。
以上をもとに漸化式を立式すると以下となる。

$$
f_{i, j} = \begin{cases}
\max{(f_{i - 2, j - 1} + A_i, f_{i - 1, j})} & j = \lfloor \frac{i}{2} \rfloor \\\\
f_{i - 2, j - 1} + A_i & otherwise
\end{cases}
$$

ただし、$f_{i, 0} = 0$ と定める。

# 実装
$f$ の計算は漸化式にしたがって $i = 0, 1, \ldots, N$ と順に $j = \lfloor i / 2 \rfloor, \lceil i / 2 \rceil$ についてのみ
$f_{i, j}$ を計算すればよい。
$f$ を $O(N^2)$ のサイズの配列で管理する訳にはいかないので、連想配列を用いるとよい。

# 教訓
- 配列の使用にこだわらないほうが吉であった
- 素直にメモ化再帰で書いたほうが事故らないかも
