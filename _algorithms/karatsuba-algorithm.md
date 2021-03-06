---
layout: entry
changelog:
  - summary: 見出し作成
    authors: kimiyuki
    reviewers:
    date: 2020-07-09T00:00:00+09:00
  - summary: 本文作成
    authors: ngtkana
    reviewers: ["noshi91", "misawa"]
    date: 2021-03-30T00:00:00+09:00
algorithm:
  input:
     環上の次数 $N - 1$ の多項式 $f, g$ の係数列
  output:
    積 $f g$ の係数列
  time_complexity: $\Theta ( N ^ { \log _ 2 3 } )$ 回の環演算
  space_complexity:
  aliases:
  level: yellow
description: Karatsuba 法とは、多項式乗算と多倍長整数乗算を $\Theta ( N ^ { \log _ 2 3} )$ 回の環演算で行うアルゴリズムである。
---


# Karatsuba 法

## 概要

Karatsuba 法とは、多項式乗算と多倍長整数乗算を $\Theta ( N ^ { \log _ 2 3} )$ 回の環演算で行うアルゴリズムである。


## 詳細


環 $R$ 上の次数 $N - 1$ の多項式 $f, g$ の積 $f g$ を計算する。これを long multiplication（筆算と同じ方法、grade-school multiplication とも）で計算すると、$\Theta ( N ^ 2 )$ 回の環演算を要する。また、計算量は同じであるが、次のように分割統治法で計算することができる。

```plaintext-katex
$\mathtt { LongMultiplication } ( f, g )$
 1. // 入力: $f, \ g$ は多項式
 2. // 出力: 積 $fg$
 3. $n = \max ( \mathrm { deg } ( f ), \mathrm { deg } ( g) ) + 1$
 4. $\mathtt { if } \ n \ \mathtt { == } \ 1$
 5.     $\mathtt { return } \ \left ( [ x ^ 0 ] f \right ) \left ( [ x ^ 0 ] g \right )$
 6. $m = \lfloor n / 2 \rfloor$
 7. 次数 $m$ で切って$f ( x ) = f _ 1 (x  ) x ^ m  + f _ 0 ( x ),  g ( x )  = g _ 1 ( x ) x ^ m + g _ 0 ( x )$ と分解する。
 8. $h _ 0 = \mathtt { LongMultiplication }( f _ 0 , g _ 0  )$
 9. $h _ 1 = \mathtt { LongMultiplication }( f _ 0 , g _ 1 ) + \mathtt { LongMultiplication }( f _ 1, g _ 0 )$
10. $h _ 2 = \mathtt { LongMultiplication }( f _ 1 , g _ 1  )$
11. $\mathtt { return } \ h _ 2 x ^ { 2 m } + h _ 1 x ^ m + h _ 0$
```

Karatsuba 法では、恒等式
$$
f _ 0 g _ 1 + f _ 1 g _ 0 = ( f _ 0 + f _ 1 ) ( g _ 0 + g _ 1 ) - f _ 0 g _ 0 - f _ 1 g _ 1
$$
を利用する。すると次のような疑似コードを得る。再帰呼出しの回数が $4$ 回から $3$ 回に減り、計算量が $\Theta ( N ^ { \log _ 2 3 } ) \subset O ( N ^ { 1.59 } )$ に改善する。


```plaintext-katex
$\mathtt { Karatsuba } ( f, g )$
 1. // 入力: $f, \ g$ は多項式
 2. // 出力: 積 $fg$
 3. $n = \max ( \mathrm { deg } ( f ), \mathrm { deg } ( g) ) + 1$
 4. $\mathtt { if } \ n \ \mathtt { == } \ 1$
 5.     $\mathtt { return } \ \left ( [ x ^ 0 ] f \right ) \left ( [ x ^ 0 ] g \right )$
 6. $m = \lfloor n / 2 \rfloor$
 7. 次数 $m$ で切って$f ( x ) = f _ 1 ( x  ) x ^ m  + f _ 0 ( x ),  g ( x )  = g _ 1 ( x ) x ^ m + g _ 0 ( x )$ と分解する。
 8. $h _ 0 = \mathtt { Karatsuba }( f _ 0 , g _ 0  )$
 9. $h _ 2 = \mathtt { Karatsuba }( f _ 1 , g _ 1  )$
10. $h _ 1 = \mathtt { Karatsuba }( f _ 0 + f _ 1, g _ 0 + g _ 1 )  - h _ 0 - h _ 2$
11. $\mathtt { return } \ h _ 2 x ^ { 2 m } + h _ 1 x ^ m + h _ 0$
```


## 定数倍について

Long multiplication は $2 N ^ 2$ 回以下の環演算を行う。

一方、Karatsuba 法は、$N$ が $2$ 冪のとき $9 N ^ { \log _ 2 3 }$ 回以下、一般の場合に $21 N ^ { \log _ 2 3 }$ 回以下の環演算を行う。

### 証明

再帰段階以外では、第 10 行の$f _ 0 + f _ 1, g _ 0 + g _ 1$ の計算に $\lfloor N / 2 \rfloor$ 回ずつ、$\mathtt { Karatsuba } ( f _ 0 + f _ 1, g _ 0 + g _ 1 )$ から $h _ 0, \ h _ 2$ を引くのに $N$ 回以下ずつ、第 11 行で $h _ 2 x ^ { 2 m } + h _ 0$ に $h _ 1 x ^ m$ を足すのに $N$ 回以下、合計 $4 N$ 回以下の環演算を要する。従って、Karatsuba 法の環演算の回数を $T ( N )$ とおくと、$T ( N ) \le 3 T ( \lceil N / 2 \rceil ) + 4 N$ が成り立つ。また $T ( 1 ) = 1$ である。

したがって $N$ が $2$ 冪のとき、

$$
T ( N )
\le 4 N  \sum _ { k = 0 } ^ { \log _ 2 N - 1 } \left ( \frac 3 2 \right ) ^ { k } + 3 ^ { \log _ 2 N }
= 8 N \left ( \left ( \frac 3 2 \right ) ^ { \log _ 2 N } - 1 \right ) + N ^ { \log _ 2 3 }
\le 9 N ^ { \log _ 2 3 }
$$

が成り立つ。

一方一般の場合は、$L = \lceil \log _ 2 N \rceil$ とおいて、

$$
\begin{align*}
T ( N )
&\le 4 \sum _ { k = 0 } ^ { L - 1 } 3 ^ k \left \lceil \frac N { 2 ^ k } \right \rceil
    + 3 ^ L
\le 4 \sum _ { k = 0 } ^ { L - 1 } \left ( N \left ( \frac 3 2 \right ) ^ k + 3 ^ k \right )
    + 3 ^ L
\cr
&= 8 N \left ( \left ( \frac 3 2 \right ) ^ L - 1 \right )
    + 2 \left ( 3 ^ L - 1 \right )
    + 3 ^ L
\cr
&\le 8 N \left ( \left ( \frac 3 2 \right ) ^ { \log _ 2 N + 1 } - 1 \right )
    + 2 \left ( 3 ^ { \log _ 2 N + 1 } - 1 \right )
    + 3 ^ { \log _ 2 N + 1 }
\cr
&= \left ( 12 + 6 + 3 \right ) 3 ^ { \log _ 2 N } - 8 N - 2
\le 21 N ^ { \log _ 2 3 }
\end{align*}
$$

が成り立つ。∎

## 多倍長整数の乗算への応用

多倍長整数の乗算も、桁数半分で分けて同様の方法で計算することで、高速に計算することができる。


## 参考文献

* 山本慎 ・三好重明 ・原正雄 ・谷聖一 ・衛藤和文 訳*コンピュータ代数ハンドブック*. 朝倉書店, 2006.
