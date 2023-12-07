## dfs序

[Codeforces Round 907 (Div2) F](https://codeforces.com/contest/1891/problem/F)

给你一棵有根树，根位于顶点 $1$ ，最初由一个顶点组成。每个顶点都有一个数值，初始值为 $0$ 。还有两种类型的 $q$ 查询：

- 第一种类型：在顶点 $v$ 上添加一个编号为 $sz + 1$ 的子顶点，其中 $sz$ 是树的当前大小。新顶点的数值将是 $0$ 。
- 第二种类型：在顶点 $v$ 的子树中所有顶点的数值中添加 $x$ 。

完成所有查询后，输出最终树中所有顶点的数值。

* * *

我们首先先将所有询问离线，并且先建树。

然后算出dfs序列。然后处理询问。

对于第一种询问。我们将该节点清空为 $0$。

对于第二种询问。我们通过转化后的dfs序列，通过树状数组维护区间加减即可。


```cpp
const int N = 5e5 + 100;
 
int l[N],r[N];
vector<int> g[N];
int tot;
 
template<class T>
struct BIT {
    T c[N];
    int size;
    void resize(int s) {
        size = s;
        for(int i = 1;i <= s;i ++) c[i] = 0;
    }
 
    T query(int x) {
        assert(x <= size);
        T res = 0;
 
        for (; x; x -= x & -x) {
            res += c[x];
        }
 
        return res;
    }
 
    void modify(int x, T val) {
        assert(x != 0);
 
        for (; x <= size; x += x & -x)
            c[x] += val;
    }
};
 
void dfs(int u,int f)
{
  l[u] = ++tot;
  for(auto v : g[u])
  {
    if(v == f) continue;
    dfs(v, u);
  }
  r[u] = tot;
}
 
BIT<int> c;
 
typedef struct qu 
{
  int op,v,x;
}E;
 
int n,q;
 
void solve()
{ 
  n = 1; tot = 0;
  cin >> q;
 
  vector<E>evt;
 
 
 
  for(int i = 1;i <= q;i ++)
  {
    int op,v,x;
    cin >> op;
    if(op == 1)
    {
      n ++;
      cin >> v;
      g[v].push_back(n);
      evt.push_back((E){op,v,n});
    }
    else
    {
      cin >> v >> x;
      evt.push_back((E){op,v,x});
    }
  }
 
 
  dfs(1,0);
 
  c.resize(n);
 
  for(auto t : evt)
  {
    if(t.op == 1)
    {
      int res = c.query(l[t.x]);
      c.modify(l[t.x], -res);
      c.modify(r[t.x]+1, res);
    }
    else {
      c.modify(l[t.v],t.x);
      c.modify(r[t.v] + 1, -t.x);
    }
  }
 
  for(int i = 1;i <= n;i ++)
  cout << c.query(l[i]) << " \n"[i == n];
  
  for(int i = 0;i <= n + 3;i ++)
  {
    g[i].clear();
    l[i] = r[i] = 0;
  }
}
```

* * *

给一棵 $𝑛$ 个点的树。有如下询问。

+ `1 x y`，将 $𝑥$ 点的点权改成 $𝑦$ 。

+ `2 x`，询问 $𝑥$ 点子树的点权和，到根的路径的点权和。

对于第一种 `子树点权和` 的计算类似于前一题。
对于第二种 `点到根的路径的点权和`，可以利用差分数组进行计算。

```cpp
const int N = 2e5 + 100;

int l[N],r[N];
vector<int> e[N];
int tot;
int a[N];

template<class T>
struct BIT {
    T c[N];
    int size;
    void resize(int s) {
        size = s;
        for(int i = 1;i <= s;i ++) c[i] = 0;
    }

    T query(int x) {
        assert(x <= size);
        T res = 0;

        for (; x; x -= x & -x) {
            res += c[x];
        }

        return res;
    }

    void modify(int x, T val) {
        assert(x != 0);

        for (; x <= size; x += x & -x)
            c[x] += val;
    }
};

void dfs(int u,int f)
{
  l[u] = ++tot;
  for(auto v : e[u])
  {
    if(v == f) continue;
    dfs(v, u);
  }
  r[u] = tot;
}

BIT<int> c1,c2;

int n,q;

void solve()
{
  cin >> n >> q;
  
  c1.resize(n);
  c2.resize(n);

  for(int i = 1;i < n;i ++)
  {
    int u,v; cin >> u >> v;
    e[u].push_back(v);
    e[v].push_back(u);
  }
  dfs(1,0);

  for(int i = 1;i <= n;i ++)
  {
    cin >> a[i];
    c1.modify(l[i], a[i]);
    c2.modify(l[i], a[i]);
    c2.modify(r[i] + 1, -a[i]);
  }

  for(int i = 1;i <= q;i ++)
  {
    int ty; cin >> ty;
    if(ty == 1){
      int x,y; cin >> x >> y;
      int d = y - a[x];
      a[x] = y;
      c1.modify(l[x], d);
      c2.modify(l[x], d);
      c2.modify(r[x] + 1, -d);
    }
    else
    {
      int x; cin >> x;
      cout << c1.query(r[x]) - c1.query(l[x] - 1) << ' ' << c2.query(l[x]) << endl;
    }
  }


}

```
