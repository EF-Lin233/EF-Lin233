---
layout: post #Do not change.
category: [algorithms, programming, cpp] #One, more categories or no at all.
title:  "Binary Lifting  倍增法压缩空间规模"
date:   2024-04-11 21:29:00 +0800
author: eef #Author's nick.
#nextPart: _posts/2021-01-30-example.md #Next part.
prevPart: _posts/os/2024-04-01-Basic-Linux-Operations.md #Previous part.
#og_image: assets/example.png #Open Graph preview image.
og_description: "使线性的处理转化为对数级的处理，大大地优化时间复杂度" #Open Graph description.
fb_app_id: example
---

倍增法(Binary Lifting)是一种利用二进制的性质，不断翻倍地处理线性问题的算法，可以大大降低时间复杂度。在介绍倍增法之前，我们先介绍一下快速幂方法(Fast Power)引入相关思想。

## Fast Power

假设已知两个自然数$a$和$n$,要求$a^n$，最简单的方法是直接调用相关函数，例如`c++`中用`pow(a,n)`，`Python`中用`a**b`，姑且认为这样做的时间复杂度是`O(1)`。同时需要考虑到相关数据结构的范围大小，例如有符号`int`型变量占4个字节，可表示的范围为$-2^{31}\sim 2^{31}-1$，当我们要求$2^{32}$时就不能用`int`型变量。

现在考虑一个问题，假设我们要在尽可能小的数据类型（如`int`）下求$4^{32}$对`MOD`=1 000 000 007的余数，该如何操作呢？此时最自然想到的方法是累乘，即从1开始，逐步乘4，直到32次，这种方法的时间复杂度是`O(n)`。
```cpp
long long a = 4, n = 32, sum = 1;
int mod = 1e9 + 7;
for(int i=0;i<n;++i){
  sum *= a;
  sum %= mod;
}
```

那么顺着这个思路，再增大一下每步乘的倍数，由于$4^{32}$也即$8^{16}$，每次乘8的话就只需要乘16次；每次乘32的话就只需要乘8次......当然，也要考虑到并非所有的$a^n$都可以写成$a^{n_1 ^{n_2}}$的形式（例如$3^{17}$），我们可以选择一种更普遍的因数积形式——二进制。

仍以$4^{32}$为例，注意到$32 = 100 000_{(2)}$,即$32=2^{5}$,于是我们可以设置一个因子$x$，$x$初始值为4，每过一轮令$x = x*x$,这样只需要6轮即可求出$4^{32}$。

| Step | 1 | 2 | 3 | 4 | 5 | 6 |
|----|---|------|---|---|--|--|
| Value | $4^1$  | $4^2$ | $4^4$ | $4^8$ | $4^{16}$ | $4^{32}$ |

那$3^{17}$呢？也是同样的操作。$17=10 001_{(2)}$,即$17=2^4+2^1$,只需要5轮即可求出$3^{17}=3^{16}+3^1$。

| Step | 1 | 2 | 3 | 4 | 5 |
|----|---|------|---|---|---|
| Value | $3^1$  | $3^2$ | $3^4$ | $3^8$ | $3^{16}$ |

所以快速幂的原理就是依次遍历[$1-log_2 n$]步处因子，如果n的二进制表示中第i位为1，就把第i步的因子加入结果,时间复杂度为`O(logN)`。
```cpp
long long a = 4, b = 32, sum = 0;
int mod = 1e9 +7;
while(b){
  if(b&1) sum += a;
  a *= a;
  a %= mod;
  b >>= 1;
}
```

## Binary Lifting
倍增思想与上节快速幂的思想类似，就是通过预处理一切规模为2的幂次大小的子问题，然后将真实问题看作成这些子问题的合并。让我们来思考这样一个问题——读入一棵n个节点的树，m次询问第x个节点的第y个祖先(即从该点向上沿着父节点走y步)。n与m规模都是$10^{18}$，如何快速求解这个问题呢？

如果一步一步地向着父节点溯源，时间复杂度为`O(m*n)`，最差的情况下将达到惊人的$10^{32}$，这显然不符合我们的要求。对此应用倍增法的话，$\lfloor log_2 10^{18} \rfloor=59$,对于每个节点我们只需存储代表其第$2^0 \sim 2^{59}$的60个祖先节点，时间复杂度直接减小为`O(m*logN)`。

现在的问题就变成了如何构造节点i的logN个祖先节点？假设我们用$pa[x][y]$表示节点$x$的第$2^y$个祖先节点，根据定义可知$pa[x][0]$即节点x的父节点(第$2^0$个祖先节点)，$pa[x][1]$即节点x的祖父节点（也即父节点的父节点）。按照这种递推关系可知

\\[
pa[x][y] = pa[pa[x][y-1]][y-1]
\\]

说人话就是，节点x的第$2^y$个祖先节点是节点x的第$2^{y-1}$个祖先节点的第$2^{y-1}$个祖先节点。举个例子，现在有一条链a->b->c->d->e->f->g->h->i->j->k,要求a的第8个祖先节点(即i),我们可以先求a的第4个祖先节点e，再求e的第4个祖先节点i。

函数形式如下
```cpp
TreeAncestor(int n, vector<int>& parent){
  int size = 32 - __builtin_clz(n); // 纪录n的最高位，例如10111，最高位为5
  vector<vector<int>> pa(n, vector<int>(size, -1));
  for(int i=0;i<n;++i){
    pa[i][0] = parent[i];
  }
  for(int j=1;j<size;++j){
    for(int i=0;i<n;++i){
      if(pa[i][j-1]==-1) continue; // 节点i没有第2^(j-1)个祖先
      pa[i][j] = pa[pa[i][j-1]][j-1];
    }
  }
}
```
在得到pa数组后，我们就可以根据y的二进制中为1的位置，来得到节点x的第y个祖先节点。仍以链a->b->c->d->e->f->g->h->i->j->k为例，要求节点a的第6个祖先节点（即g），由于$6=110_{(2)}$，这里可以分别从低位到高位、从高位到低位的求取祖先节点。

（1）先求a的第$2^1$个祖先节点c，再求c的第$2^2$个祖先节点g。(a->b->c) ->(c->d->e->f->g)

（2）先求a的第$2^2$个祖先节点e，再求e的第$2^1$个祖先节点g。(a->b->c->d->e)->(e->f->g)

函数形式如下
```cpp
// 从低位到高位
int getKthAncestor(int node, int k) {
    int cnt = 0;
    while(k){
        if(k&1){
            node = pa[node][cnt];
            if(node==-1) break;
        }
        k >>= 1;
        cnt ++;
    }
    return node;
}
// 从高位到低位
int getKthAncestor(int node, int k) {
  int cnt = 32 - __builtin_clz(k);
  if(cnt==0) return node;
  cnt--;
  while(cnt>=0){ // k // 也可以这么做
      if(k&(1 << cnt)){
          node = pa[node][cnt];
          // k -= 1 << cnt; // 每次循环去掉k的最高位。最高位为1，减去1 << cnt,为0不做处理
          if(node==-1) break;
      }
      cnt--;
  }
  return node;
}
```

## Lowest Common Ancestor

在倍增法的基础上，我们来探讨下最近公共祖先问题。假设现在有一棵多叉树如下所示。

<a href="/assets/img/posts/bst.png" data-lity class="sx-center">
  <img src="/assets/img/posts/bst.png"/>
</a>

（这里的二叉搜索树只是为了方便举例的一种特殊情况）假设我们现在要求这棵树上某两个节点的最近公共祖先，如4和13的最近公共祖先是8；1和6的最近公共祖先是3；13和10的最近公共祖先是10。

为了方便描述，我们先设定几个符号：

- 节点x的深度为d[x]
- 节点x的父节点为p[x]

不难推出，假设y为x的第n个祖先，有d[x]=d[y]+n。所以我们可以用DFS或BFS先预处理一遍得到d[n]数组，在由节点间的深度关系求解LCA问题。

- 假设现在有两个不同的节点u、v，d[u]>d[v]，我们先让u向上层移动，直到d[u]-d[v]。(调用前面的getKthAncestor(u, d[u]-d[v]))
- 然后令u和v同时向上移动，每移动一轮都检查u和v是否重合，当u、v首次重合时便是我们想要求的LCA。

如果每一轮都只将u和v向上移动一步，操作的时间复杂度是`O(n)`，为了减小时间复杂度，我们可以再用二分法。假设将u移动到v的同层后，d[v]=d[u]=D，LCA的深度要小于D，即$d[LCA]\in [0, D)$。现在的操作为：

- 令$t = \lfloor log_2 D \rfloor$，i的初值为t
- 每轮比较pa[u][i]与pa[v][i]是否相同（u的第$2^i$个祖先节点与v的第$2^i$个祖先节点是否相同），如果相同说明u和v最近的公共祖先应该是pa[u][i]或它的子节点，即d[LCA]>=d[pa[u][i]];如果不同，则说明u和v最近的公共祖先还在第$2^i$个祖先节点之前，d[LCA]< d[pa[u][i]],我们要让u和v向上移动$2^i$次 (u = pa[u][i], v = pa[v][i])
- 重复这个操作，直到遍历完i = t, ... , 0。u和v的最近公共节点就是u和v的父节点。

```cpp
int lca(int u, int v, vector<vector<int>>pa, vector<int>depth){
    if(depth[u]<depth[v]) swap(u,v);
    u = getKthAncestor(u, depth[u]-depth[v]);
    if(u==v) return v;
    int t = 32 - __builtin_clz(depth[u]);
    t--;
    while(t>=0){
        if(pa[u][t]!=pa[v][t]){
            u = pa[u][t];
            v = pa[v][t];
        }
    }
    return pa[u][0]; // 返回u、v的父节点
}
```
