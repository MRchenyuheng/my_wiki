# 末日在干什么 有没有空 可以来写DP吗?

# 线性DP

[CF 1613 D MEX Sequences](https://codeforces.com/contest/1613/problem/D)

对于一个序列 $x_1, x_2, \dots, x_k$ 是 `MEX-correct` 序列当且仅当对于所有的$i$ ($1 \le i \le k$) $|x_i - \operatorname{MEX}(x_1, x_2, \dots, x_i)| \le 1$ 成立。给一个序列求所有满足条件子序列的个数。

考虑权值。

我们可以发现若一个序列是 `MEX-correct`的,设最大值为 $maxv$,必只有以下两种形态。

+ 从 $0$ 到 $maxv$ 的数值的各个数量至少为 $1$。
+ 从 $0$ 到 $maxv - 2$ 和 从 $maxv$ 到 $maxv$ 的数值的各个数量至少为 $1$。

简单来说就是，

+ $[0, maxv]$ 每个元素都有。

+ $[0, maxv - 1]$ 和 $[maxv, maxv]$每个元素都有。

那么定义,到目前决策阶段 $i-1$ 的状态中最大值为 $val$ ,且满足条件 $1$的数量为 $dp1_{val}$,满足条件2的为 $dp2_{val}$
那么我们决策 i 的时候有如下转移

+ $dp2_{a[i]} += dp2_{a[i]}$,第二种情况最大值可以添加。

+ $dp2_{a[i] + 2} += dp2_{a[i] + 2}$,第二种情况第二大值可以添加。

+ $dp2_{a[i]} += dp1_{a[i] - 2}$,第二种情况由在第一种情况转化而来。

+ $dp1_{a[i]} += dp1_{a[i]}$

+ $dp1_{a[i]} += dp1_{a[i] - 1}$

```cpp
void solve()
{
    cin >> n;
    vector<int> a(n + 1);
    rep(i,1,n) cin >> a[i];
 
    vector<int> dp1(n + 5), dp2(n + 5);
    
    
    dp1[0] = 1; // 向右偏移一位，这里的 0 相当于 -1
 
    for(int i = 1;i <= n;i ++)
    {
        a[i] ++;
        (dp2[a[i]] += dp2[a[i]]) %= mod; 
        (dp2[a[i] + 2] += dp2[a[i] + 2]) %= mod;
        if(a[i] >= 2)(dp2[a[i]] += dp1[a[i] - 2]) %= mod;
        (dp1[a[i]] += dp1[a[i]]) %= mod;
        (dp1[a[i]] += dp1[a[i] - 1]) %= mod;
 
    }
 
    int res = 0;
 
    for(int i = 1;i <= n + 1;i ++)
    (res += dp1[i] + dp2[i]) %= mod;
    
    cout << res << endl;
```

[ABC321 F](https://atcoder.jp/contests/abc321/tasks/abc321_fhttps://atcoder.jp/contests/abc321/tasks/abc321_f)

我们有一个盒子，最初是空的。  
让我们按照输入中给出的顺序，总共执行以下两种类型的 $Q$ 操作。

类型 $1$：在盒子中放入一个写有整数 $x$ 的小球。

类型 $2$：从盒子中取出一个写有整数$x$的小球。  

每次操作后,求从盒子中拾取一定数量的球，使写在球上的整数和为$K$的数量。

* * *

开一个 $dp$ 桶，考虑 $dp_{i}$ 为当前情况下构成数字 $i$ 的方案有多少个。

每次修改进行 $O(n)$ 枚举即可。


```cpp
const int N = 5e3 + 100;
int dp[N];

int a[N];

void solve()
{
  int q,k;
  cin >> q >> k;

  dp[0] = 1;

  for(int i = 0;i < q;i ++)
  {
    string op; int v;

    cin >> op >> v;

    if(op[0] == '+'){
      for(int j = k;j >= v;j --){
        dp[j] += dp[j - v];
        dp[j] %= mod;
      }
    }
    else
    {
      for(int j = v;j <= k;j ++){
        dp[j] += (mod - dp[j - v]);
        dp[j] %= mod;
      }
    }

    cout << dp[k] << endl;
  }
}
```

# 概率DP

[BZOJ #3036. 绿豆蛙的归宿](https://hydro.ac/d/bzoj/p/3036)

给一个有向无环图，起点为 $1$，终点为$n$;

到达每一个顶点时，如果有 $k$ 条离开该点的道路，那么走向每条路的概率为 $\frac{1}{k}$。

求到终点的距离期望。

记 $f_{i}$ 表示为从 $i$ 到终点 $n$ 的期望长度。

考虑一条边 $u \rightarrow v$,由于进行了拓扑排序,所以$dp_{v}$ 已知。

$dp_{u} = p \times ( dp_{v} +  w_{u \rightarrow v} )$, $p = \frac{1}{k}$。

```cpp
void topsort()
{
    queue<int> q;
    q.push(n);

    while(q.size())
    {
        int u = q.front(); q.pop();
        //int sz = g[u].size();

        for(auto t : g[u])
        {
            int v = t.first; int len = t.second;
            d[v] += 1.0 / sz[v] * (len + d[u]);
            din[v] --;
            if(din[v] == 0) q.push(v);
        }
    }

}


void solve()
{

    cin >> n >> m;
    for(int i = 1;i <= m; i ++)
    {
        int u,v,c;
        cin >> u >> v >> c;

        din[u] ++; sz[u] ++;
        g[v].push_back(PII(u,c));
    }

    topsort();

    cout << fixed << setprecision(2) <<  d[1] << endl;

}
```
思考参考:[为什么通常期望dp要倒推？](https://www.zhihu.com/question/464263211)


# DP与二进制

[CF 1635 D. Infinite Set](https://codeforces.com/contest/1635/problem/D)

给$n$个数$a_{n}$，一个大小限制$p$
可以进行任意次数的操作，每次操作选定集合里一个$x$
将$2 \times x + 1$或者$4 \times x$加入到集合中
问该集合中小于$2^{p}$的数有几个

将$x$看成二进制。
第一个操作便是将`x << 1 | 1`加到集合中。看成在某个数的二进制表示后面加一个`1`。
第二个操作便是将`x << 2`加到集合中。看成在某个数的二进制表示后面加两个`0`。

我们可以发现一个性质，根据奇偶性，一个数要么由操作`1`变过来，要么由操作`2`变过来。

设$f_{i}$代表原二进制串左移$i$个单位能得到的所有的不同二进制串的个数
设$dp_{i}$代表原二进制串左移$i$个单位能得到的不同二进制串的个数

可知$dp_{i} = dp_{i-1} + dp_{i-2}$也就是通过前两种方法得到转移
而$f_{i}$也就是$dp_{i}$的前缀和

对于某个长度为$l$的数$x$
他可以形成长度为$l + 1$的数的种数为`1`种。即法一。
他可以形成长度为$l + 2$的数的种数为`2`种。即两步法一和一步法二。

```cpp

const int N = 2e5 + 100;
const int mod = 1e9 + 7;

int f[N];
int sum[N];
int a[N];
map<int,bool> mp;

int n,p;

void init()
{
  f[0] = f[1] = 1;
  sum[0] = 1;
  sum[1] = 2;
  for(int i = 2;i <= 2e5 + 10;i ++){
    f[i] = (f[i - 1] + f[i - 2])%mod;
    sum[i] = (sum[i - 1] + f[i])%mod;
  }
}

bool check(int x)
{
   while(x)
   {
     if(x % 2 == 1) x >>= 1;
     else if(x % 4 == 0)x >>= 2;
     else break;

     if(mp.count(x)) return false;
   }

   return true;
}

int bit(int x)
{
  int res = 0;
  while(x) x >>= 1, res ++;
  return res;
}

void solve()
{
   init();
   cin >> n >> p;
   int res = 0;

   for(int i = 1;i <= n;i ++) cin >> a[i];
   for(int i = 1;i <= n;i ++) mp[a[i]] = true;

   for(int i = 1;i <= n;i ++)
   if(check(a[i])){
    int len = bit(a[i]);
    if(p - len >= 0) (res += sum[p - len]) %= mod;
   }

   cout << res << endl;

}
```

upd:20231023

