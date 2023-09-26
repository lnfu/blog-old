---
title: "LADR 第一章習題"
date: 2023-07-28T16:20:32+08:00
draft: false
author: "Enfu Liao"
math: true
# cover:
#     image: "<image path/url>" # image path/url
#     alt: "<alt text>" # alt text
#     caption: "<text>" # display caption under cover
#     relative: false # when using page bundles set this to true
#     hidden: true # only hide on current single page
---

> 這篇的數學式找個時間重打（用 shortcodes）

> LADR = Linear Algebra Done Right

1. Suppose $a$ and $b$ are real numbers, not both 0. Find real numbers $c$ and $d$ such that
$$
\frac{1}{a+ib} = c + id
$$.

> 這個高中數學，略。

2. Show that 
$$
\frac{-1 + i\sqrt{3}}{2}
$$
is a cube root of 1.

> 這個也高中數學，略。

3. Prove that if $a\in F$, $v\in V$, and $av = 0$, then $a = 0$ or $v = 0$
> 拆成兩個 case（$a = 0$ 和 $a \neq 0$）
>
> 如果 $a = 0$ 就直接結束。
>
> 如果 $a \neq 0$，$a$ 會有一個乘法反元素 $a^{-1}$ 使得 $a^{-1}a = 1$
> $$v = 1v = (a^{-1}a)v = a^{-1}(av) = a^{-1}0 = 0$$
> （使用到 associativity 結合律）

4. Prove that $-(-v) = v$ for every $v\in V$
> 一樣從 associativity 下手
> $$-(-v) = (-1)(-1v) = ((-1)(-1))v = 1v = v$$

5. For each of the following subsets of $F^{3}$, determine whether it is a subspace of $F^{3}$

(a) $$\{ (x_{1}, x_{2}, x_{3}) \in F^{3} : x_{1} + 2x_{2} + 3x_{3} = 0 \}$$
(b) $$\{ (x_{1}, x_{2}, x_{3}) \in F^{3} : x_{1} + 2x_{2} + 3x_{3} = 4 \}$$
(c) $$\{ (x_{1}, x_{2}, x_{3}) \in F^{3} : x_{1}x_{2}x_{3} = 0 \}$$
(d) $$\{ (x_{1}, x_{2}, x_{3}) \in F^{3} : x_{1} = 5x_{3} \}$$

> 這個太 trivial，答案是 O, X, X, O

6. Prove that the intersection of any finite collection of subspaces of $V$ is a subspace of $V$
> 令 $U_{1}, U_{2}, ..., U_{n}$ 都是 $V$ 的 subspace
>
> 令 $U = U_{1} \cap U_{2} \cap U_{3} \cap ... \cap U_{n}$
> - 因為 $0 \in U_{i}$ for $i = 1, 2, ..., n$，所以 $0 \in U$
> - 證明加法封閉性：
>   - 令 $v, w \in U$，那麼 $v, w \in U_{i}$，因為 $U_{i}$ 都是 subspace 所以有封閉性（$v + w \in U_{i}$），因此 $v + w \in U$
> - 證明乘法（scalar mulplication）封閉性：
>   - 令 $v \in U$，那麼 $v \in U_{i}$，$av \in U_{i}$, $a \in F$，因此 $av \in U$

7. Give an example of a nonempty subset $U$ of $R^{2}$ such that $U$ is closed under addition and under taking additive inverses (meaning $-u \in U$ whenever $u \in U$), but $U$ is not a subspace of $R^{2}$.
> 有加法封閉性和反元素，但不是 subspace。
>
> 可以找一個沒有滿足 scalar mulplication 封閉性的例子。（e.g., 有理數）
> 
> $$(1, 1) \in Z^{2}$$
> $$\sqrt{2} (1, 1) = (\sqrt{2}, \sqrt{2}) \notin Z^{2}$$

8. Give an example of a nonempty subset $U$ of $R^{2}$ such that $U$ is closed under scalar multiplication, but $U$ is not a subspace of $R^{2}$.
> 這題也蠻簡單的，我們可以找兩條不同且通過原點的線的聯集
>
> 最簡單的例子就是 $x = 0$ 和 $y = 0$
> $$V = \{ (x, y) : x = 0 \text{ or } y = 0 \}$$
> $$(1, 0) \in V \text{ and } (0, 1) \in V \text{ but } (1, 0) + (0, 1) = (1, 1) \notin V$$

9. Prove that the union of two subspaces of $V$ is a subspace of $V$ if and only if one of the subspaces is contained in the other.
> 這題的反例可以參考上題的解答。
>
> 證明：
>
> 可以先證明右到左，如果其中一個 subspace 只是另一個的子集，那麼聯集後就是比較大的 subspace（當然是 $V$ 的 subspace）
> 
> 左到右可以用反證法，假設 $U$ 和 $W$ 是 $V$ 的 subspace 且沒有包含，$U \cup W$ 是 $V$ 的 subspace。
>
> 也就是存在 $u$ 和 $w$ 滿足 $u \in U$、$u \notin W$、$w \in W$、$w \notin U$
> 
> 若 $u + w \in U$ 則 $(u + w) + (-u) \in U$，所以 $w \in U$，矛盾。$u + w \notin U$。
> 
> 同理 $u + w \notin W$。
>
> 因此 $u + w \notin U \cup W$（與假設矛盾）

10. Suppose that $U$ is a subspace of $V$. What is $U + U$?
> 我有想到答案是 $U$ 但是沒寫出證明。
>
> 證明：
> 
> 因為 $U$ 是 subspace 所以有封閉性，所以  $U + U \subseteq U$
>
> 因為 $u = u + 0$ 對所有 $u \in U$ 又 $0 \in U$，所以 $U \subseteq U + U$

11. Does the operation of addition on the subspaces of $V$ have an additive identity? Which subspaces have additive inverses?
> 一開始沒看懂題目。
>
> 課本第一章已經定義了 subspace 的加法，題目問的是是否這樣的加法有單位元素和反元素
>
> 單位元素是 $\{0\}$
> $$U + \{0\} = \{0\} + U = U$$
> 反元素的話只有 $\{0\}$ 有（他自己），因為
> $$U + W = \{0\} \implies U = \{0\} \wedge W = \{0\}$$

12. Prove or give a counter-example: If $U_{1}$, $U_{2}$, $W$ are subspaces of $V$ such that
$$U_{1} + W = U_{2} + W$$
then $U_{1} = U_{2}$

> 錯誤。直接舉個反例
>
> $$W = (x, 0)$$
> $$U_{1} = (0, y)$$
> $$U_{2} = (x, x)$$

13. Is the operation of addition on the subspaces of $V$ commutative? Associative?
> 這題也很直覺。
>
> 假設 $v = u + w \in U + W$，$u \in U$ and $w \in W$ where $U$ and $W$ are subspaces of $V$。
>
> $v = w + u \in W + U$（commutative）
>
> associateve 的部份也差不多，假設 $v = (u_{1} + u_{2}) + u_{3} \in (U_{1} + U_{2}) + U_{3}$，以下省略。

14. Suppose $U$ is the subspace of $P(F)$ consisting of all polynomials $p$ of the form
$$p(z) = az^{2} + bz^{5}$$
where $a, b \in F$. Find a subspace $W$ of $P(F)$ such that $P(F) = U \oplus W$

> 這題可以很容易想到是要次方是 $2$ 和 $5$ 為 $0$ 其他任意。
>
> 不過證明的部份稍嫌繁瑣了。
>
> 大致絲路如下：
> - 證明 $W$ 是 subspace
>   - $0 \in W$
>   - closed under addition
>   - closed under scalar mulplication
> - $P(F) = U \oplus W$ if and only if $P(F) = U + W \wedge U \cap W = \{0\}$（這個很重要）
>   - $P(F) = U + W$
>   - $U \cap W = \{0\}$


15. Prove or give a counter-example: If $U_{1}$, $U_{2}$, $W$ are subspaces of $V$ such that
$$V = U_{1} \oplus W \text{ and } V = U_{2} \oplus W$$
then $U_{1} = U_{2}$
> 錯誤。同 12 的反例。