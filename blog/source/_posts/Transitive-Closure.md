---
title: Transitive Closure 問題與矩陣相乘的關聯
date: 2018-01-07 14:18:40
tags: ["Graph Algorithms", "Matrix Multiplication"]
mathjax: true
---

## Transitive Closure 問題

給定一個 $n$ 個點的有向無權圖 $G=(V, E)$，我們想要計算出一個 $n\times n$ 的二元矩陣 $TC$，其中如果 $v_i$ 有一條路徑可以到 $v_j$，那麼 $TC[i, j] = 1$，否則 $TC[i, j] = 0$。

## 演算法們

簡單的演算法包括：從每一個點分別作一次 DFS/BFS，找出可以到達的點集，這樣花的時間是 $O(nm)$。

另一種演算法是基於二元矩陣相乘的：我們令 $A$ 表示圖 $G$ 的鄰接矩陣（Adjacency Matrix），那麼我們可以知道 $TC = A \lor A^2 \lor A^3 \lor \cdots \lor A^{n-1}$。在二元矩陣相乘的世界中，無論作什麼操作都是 MOD 2。（也就是說，乘法是 AND、加法是 XOR）。如果我們允許加法變成 OR 的話，那麼我們更可以寫成 $TC = A^{n-1}$，因為此時 $A^{n-1}[i, j]$ 代表的是從 $i$ 到 $j$ 走至多 $n-1$ 步是否能到達。

從上述的方法中，我們知道 Transitive Closure 問題可以轉化為 Boolean Matrix Multiplication。如果寫成公式的話長這樣：

$${\tt{TC}}\leq_p {\tt{BMM}}$$

今天要聊的有兩件事情：
1. 其實二元矩陣相乘（加法是 OR 版）可以轉化為 Transitive Closure。
2. 利用矩陣相乘的演算法可以在 $O(n^\omega)$ 時間內算出，因此上述轉化的過程我們得到一個計算 Transitive Closure 的 $O(n^\omega \lg n)$ 的方法。但實際上，我們可以做到 $O(n^\omega)$ 時間。

## 二元矩陣相乘的等價性

這個證明是來自 Fischer 和 Meyer 1971 年的結果。

要證明 ${\tt{BMM}}\leq_p {\tt{TC}}$，我們假設有一個厲害的演算法可以解決 Transitive Closure 問題。
對於兩個給定的方陣 $A, B\in\\{0, 1\\}^{n\times n}$，我們構造出以下的圖，使得它的鄰接矩陣長得像這樣：

$$\left(
    \begin{array}{c|c|c}
    I & A & 0\\\\
    \\hline
    0 & I & B\\\\
    \\hline
    0 & 0 & I
\end{array}
\right)$$

經過一陣精密計算後，我們發現他的 Transitive Closure 長得像這樣：

$$\left(
    \begin{array}{c|c|c}
    I & A & {\color{red}{AB}}\\\\
    \\hline
    0 & I & B\\\\
    \\hline
    0 & 0 & I
\end{array}
\right)$$

所以我們就可以輕易地透過「解決 Transitive Closure 問題」來解決「Binary Matrix Multiplication」了，他們是差不多難度的問題！

## 用矩陣相乘時間來算 Transitive Closure

本節的目標是介紹一個演算法，利用與矩陣相乘同樣的漸進函數時間算出 Transitive Closure。
事實上，他只利用了一個單純的分而治之技巧！

這個演算法是 Munro 在 1971 年的時候提出來的：
1. 首先先把整個圖用強連通塊演算法(strongly connected components)縮起來（因為屬於同一個強連通塊的點，可以連通到／被連通到的點集合都相同）。
2. 因此我們可以假設輸入的圖會是一個 DAG (directed acyclic graph)。
3. 既然整個圖是一個 DAG，它就會存在一個拓樸排序 (topological order)，這個順序可以用一次 DFS 在線性時間得到。
4. 假設輸入的點集合是一個拓樸排序，我們發現他的鄰接矩陣可以排列成一個上三角矩陣：
$$M = \left(
\begin{array}{c|c}
A & X \\\\
\\hline
0 & B
\end{array}
\right)$$
其中 $A, B$ 也都是上三角矩陣。
5. 透過觀察可以發現，Transitive Closure 可以表示為
$$M^{\\star} = \left(
\begin{array}{c|c}
A^{\\star} & A^{\\star} XB^{\\star}\\\\
\\hline
0 & B^{\\star}
\end{array}
\right)$$
如果實際計算 $A^{\\star}XB^{\\star}$ 用一般的 $O(n^\omega)$ 矩陣相乘，那麼我們可以得到以下遞迴式：
$$ T(n) = 2T(n/2) + O(n^\omega)$$
解得
$$T(n) = O(n^\omega)。$$



## 後話 —— 真的只能用一般矩陣相乘來算 Transitive Closure 了嗎？

從文章前面的部分，我們可以知道計算 Transitive Closure 與二元矩陣相乘其實是等價的。但問題是，二元矩陣相乘是不是只能用跟一般矩陣相乘相同的方法做呢？

如果說一般的矩陣相乘是 $(\times, +)$-矩陣相乘的話，我們需要的二元矩陣相乘其實是 $(\land, \lor)$-矩陣相乘。這兩者還是稍有不同的。比方說，後者不支援「減法」的存在，所以像是 [Strassen 演算法](https://en.wikipedia.org/wiki/Strassen_algorithm) 等矩陣相乘演算法就沒有辦法直接套用在二元矩陣的情形上。因此在二元矩陣相乘的研究上，衍伸出了另一派被稱為「Combinatorial Algorithm」的演算法類型。雖然沒有明確的定義，但這個流派的演算法似乎與二元矩陣有較深的關聯。
不過就效率而言，目前這一系列不仰賴實數體／整數環的演算法相對較差，都是 $\tilde{O}(n^3/\log^cn)$ 等級的。

## 參考資料

[1] https://www.cs.bgu.ac.il/~dinitz/Course/SS-12/transitiveClosure.pdf
[2] http://theory.stanford.edu/~yuhch123/files/cbmm.pdf
[3] Efficient Determination of the Transitive Closure of a Directed Graph, Munro 1971 https://www.sciencedirect.com/science/article/pii/S0019995873902283 
[4] Boolean matrix multiplication and transitive closure, Fischer and Meyer 1971 http://ieeexplore.ieee.org/document/4569672/
[5] A Fast Expected Time Algorithm for Boolean Matrix Multiplication and Transitive Closure, O'Neil 1973 https://ac.els-cdn.com/S0019995873902283/1-s2.0-S0019995873902283-main.pdf?_tid=082c89aa-f96e-11e7-b46d-00000aab0f6b&acdnat=1515963843_169630399eb8c8e785f8b7c7490c6cef 