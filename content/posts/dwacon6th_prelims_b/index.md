---
title: 第6回 ドワンゴからの挑戦状 予選 B - Fusing Slimes
date: 2020-03-29
categories:
  - 競プロ
tags:
  - AtCoder
  - 数学
  - 期待値
---

# 問題情報
- 問題文: https://atcoder.jp/contests/dwacon6th-prelims/tasks/dwacon6th_prelims_b
- 解説: https://img.atcoder.jp/dwacon6th-prelims/editorial.pdf

# 考察
ほぼ公式の解説通り。少しだけ補足。
求める解は $(N - 1)! \sum_{i = 1}^{N - 1} (x_{i + 1} - x_i) \sum_{j = 1}^{i} p_{j, i}$ だが、これは期待値の線形性を利用している。
つまり、各スライムの移動距離の期待値は区間ごとの期待値の和に等しいことを利用している。

直観的には成り立ちそうだが、念のため形式的に表現してみよう。
スライム $j$ の移動距離を確率変数 $X_j$, スライム $j$ が$x_i, x_{i + 1}$ 間内で移動する距離を確率変数 $Y_{j, i}$ で表すことにすると $E[X_j] = E[\sum_{i = 1}^{N - 1} Y_{j, i}]$ である。期待値の線形性により $E[\sum_{i = 1}^{N - 1} Y_{j, i}] = \sum_{i = 1}^{N - 1} E[Y_{j, i}]$ が成り立つ。
$E[Y_{j, i}] = (x_{i + 1} - x_i)p_{j, i}$ より全スライムの移動距離の期待値は
$\sum_{j = 1}^{N - 1} E[X_j] = \sum_{j = 1}^{N - 1} \sum_{i = 1}^{N - 1} E[Y_{j, i}] = \sum_{i = 1}^{N - 1} \sum_{j = 1}^{N - 1} E[Y_{j, i}] = \sum_{i = 1}^{N - 1} \sum_{j = 1}^{N - 1} (x_{i + 1} - x_i)p_{j, i}$ となる。
$j > i$ ならば $p_{j, i} = 0$　なので $\sum_{i = 1}^{N - 1} \sum_{j = 1}^{N - 1} (x_{i + 1} - x_i)p_{j, i} = \sum_{i = 1}^{N - 1} \sum_{j = 1}^{i} (x_{i + 1} - x_i)p_{j, i}$ を得る。

# 実装

解説の通り、逆数の累積和は事前に計算しておけば $\sum_{j = 1}^{i} p_{j, i}$ を $O(1)$ で計算できる。
逆数の累積和計算はModIntを用いると楽。
