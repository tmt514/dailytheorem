---
title: Makespan 問題的 Greedy 近似解法
date: 2017-12-19 08:39:37
tags:
---

## Makespan 問題

有 $m$ 台相同的機器以及 $n$ 件工作，每一件工作只能挑選一台機器執行，不能切開。
第 $i$ 件工作執行要花 $p_i$ 時間。求一種工作排程方案，使得最慢結束的那台機器最早結束。

## 一種 Greedy 演算法

首先我們可以把所有工作依照執行時間由大到小排序，因此不妨假設 $p_1 \ge p_2 \ge \ldots \ge p_n$。
接下來，對於每一個工作 $p_i$，我們把它指派到**當前工作總量最少的那台機器**。

### 定理一

上述的 Greedy 演算法是 2-approximation。

### 定理一的證明

老樣子，我們令 $ALG$ 表示上述貪婪法算出來的最慢那台機器需要的計算時間、令 $OPT$ 表示最佳解。於是上面定理要說的是 $ALG \le 2\cdot OPT$。

首先，我們先不妨假設 $p_n$ 是被指派到工作量最大的那台機器（如果不然，我們就把最小的工作拿掉，不影響近似程度）。接下來，考慮 $p_n$ 被放進去的當下，根據 greedy 演算法的規則，我們知道 $ALG \le (p_1+p_2+\cdots+p_{n-1}) / m + p_n$。

但顯然 $OPT \ge (p_1+p_2+\cdots+p_n)/m$，因為總會有一台機器的工作總時間達到平均值。此外，顯然 $OPT$ 要能容得下一整個最久工作的時間，於是 $OPT\ge p_1\ge p_n$。因此，我們可以得到
$$
\begin{equation}
ALG \le OPT + p_n \le 2\cdot OPT \label{eq1}
\end{equation}
$$

### 定理二

上述的 Greedy 演算法是 $\frac{3}{2}$-approximation。

### 定理二的證明

我們想證明的其實是：若 $ALG\neq OPT$，那麼 $p_n \le \frac{1}{2} OPT$。證明如下：如果有某一台機器被分配至少兩件工作 $p_i, p_j$，那麼由於排序的關係 $OPT \ge p_i+p_j \ge 2p_n$，得證。否則的話，每一台機器都只被分配到一個工作，顯然這時候 $OPT=p_1=ALG$。
將結果代入 $\eqref{eq1}$ 以後，就得證了。

### 定理三

上述的 Greedy 演算法是 $\frac{4}{3}$-approximation。

### 定理三的證明

我們想證明的其實是：若 $p_n > \frac13 OPT$，那麼 $ALG = OPT$。（另一種 case 可以由 $\eqref{eq1}$ 直接得證。）證明如下：當 $p_n > \frac13 OPT$ 的時候，顯然在最佳解當中，所有機器至多只有 2 件工作被執行（如果有某台機器執行了 3 件工作，那麼 $OPT \ge 3 p_n$ 與假設矛盾）。當最佳解中，所有機器至多只有 2 件工作被執行時，我們要證明的是 Greedy 解也會找到 $OPT$。

假定 $p_n > \frac13 OPT$，我們仍可以不妨假設 $p_n$ 位於總工時最長的唯一一台機器上。在這個假設之下，我們得知在 Greedy 演算法的過程中，一開始會將 $p_1$ 至 $p_m$ 依序安排至每一台不同的機器上。假設第 $i$ 件工作被安排到第 $i$ 台機器上。接下來，我們證明：對於 $i=1, 2, \ldots, n-m$，第 $m+i$ 件工作會被指派到第 $m+1-i$ 台機器上。若不然，某個 $p_x$ 會被放到某一台帶有兩件工作的機器上，此時便有 $p_n \le p_x \le \frac13 OPT$ 與假設矛盾。因此，$ALG=p_n+p_{m-n+1}$。

接下來證明在上述假設之下 $OPT=p_n+p_{m-n+1}$：假設 $OPT=p_n+p_i$，而且 $i > m-n+1$，根據鴿籠原理必定有 (1) 某三個工作被塞在編號 $>m-n+1$ 的機器理，或是 (2) 至少在 $1, 2, \ldots, m-n$ 其中一台機器上有兩件工作。若 (1) 發生直接與假設矛盾，若 (2) 發生，則該台機器上的兩件工作總時間必定大於或等於 $p_n+p_i$，也與假設矛盾。因此得證。

### 定理四

$\frac43$-approxmation 是 Greedy 演算法最緊緻的上界：存在一系列測試資料使得 Greedy 演算法的 $\frac{ALG}{OPT}\to \frac43$。

### 定理四的證明

令 $n=2m+1$，然後有 3 個長度是 $m$ 的工作、然後 $m+1, m+2, \ldots, 2m-1$ 長度的各兩個。在 Greedy 演算法的作用下，在放完 $2m$ 個工作以後，每一台機器的工作量都剛好會是 $3m-1$。因此加上最後一件工作時，工時最長的那台機器，工作量變成 $4m-1$，因此 $ALG=4m-1$。

但是，我們可以利用 $m-1$ 台機器，將所有 $2m-2$ 件各兩個的工作放入使得每一台機器工作量恰好都是 $3m$，再將 $3$ 個長度為 $m$ 的工作放到最後一台機器中。因此有 $OPT=3m$。因此當 $m\to \infty$ 的時候 $\frac{ALG}{OPT} \to \frac43$。

### 延伸思考

* 當 $m$ 也變成一個固定的 parameter，而將 $n$ 拉到很大的時候，似乎會有更好的上界。

### 延伸參考

* $\sf{Makespan}\in \textrm{PTAS}$

## 參考資料

[1] http://theory.epfl.ch/osven/courses/Approx13/Notes/lecture2.pdf