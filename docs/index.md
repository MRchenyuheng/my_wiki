# 第四场训练赛

# A Adventure in Flatland 

只需要判断是否在不同象限即可。

```cpp
void solve()
{
    int a,b,c,d;
    cin >> a >> b >> c >> d;
    int res = (a * c < 0 ? 1 : 0) + (b * d < 0 ? 1 : 0);
 
    cout << (res > 0 ? 1 : 0) << endl;
}
```

# B Hard Brackets Problem 

2023 ccpc guilin 签到

我们抓住右括号生成的情况。一个是左括号带来的配对，一个是右括号自己输入的。

左括号的数量必须小于等于右括号的数量。

注意到，一旦右括号的数量大于左括号的数量，这部分多出来的右括号数量，不能帮助后续的左括号匹配。

```cpp

void solve()
{
    string s; cin >> s; int n = s.size();
    int l = 0; int r = 0;
    for(int i = 0;i < n;i ++){
        if(s[i] == '(') l ++;
        else r ++;
        if(r > l) l = r = 0;
    }
 
    if(l > r) cout << "impossible" << endl;
    else cout << s << endl;
}

```

# C 滑雪 

记忆化搜索。

记 $dp_{i,j}$ 为从 $(i,j)$ 出发，最长能走多长的距离即可。
 
```cpp
const int N = 110;

int n,m;

int dx[] = {-1,0,1,0};
int dy[] = {0,-1,0,1};

int dp[N][N];
int a[N][N];

int dfs(int x,int y)
{
  if(dp[x][y]) return dp[x][y];

  dp[x][y] = 1;
   
  for(int i = 0;i < 4;i ++){
    int nx = x + dx[i];
    int ny = y + dy[i];
    if(nx > n || nx < 1 || ny > m || ny < 1) continue;
    if(a[nx][ny] >= a[x][y]) continue;
    dp[x][y] = max(dp[x][y],dfs(nx,ny) + 1);
  }

  return dp[x][y];
}

void solve()
{
  cin >> n >> m;
  for(int i = 1;i <= n;i ++)
    for(int j = 1;j <= m;j ++)
      cin >> a[i][j];

  int res = 0;

  for(int i = 1;i <= n;i ++)
    for(int j = 1;j <= m;j ++) 
      res = max(res,dfs(i,j));
  
  cout << res << endl;
  
}
```

# D 最长公共子序列 

## 简单版本

```cpp
  for(int i = 1;i <= n;i ++) for(int j = 1;j <= n;j ++){
    if(a[i] == b[j]) dp[i][j] = max(dp[i - 1][j - 1] + 1,dp[i][j]);
    dp[i][j] = max(dp[i][j],dp[i - 1][j]);
    dp[i][j] = max(dp[i][j],dp[i][j - 1]);
  }
```

## 当前版本

考虑到了排列的性质转化。

将 $a$ 数组重新离散化标号。

由于 $a$ 数组是递增的，那么 $a$ 的子序列也是递增的。

由于排列，反过来，递增的序列 一定是 $a$ 中的子序列。

转化为求 $b$ 中序列的最长上升子序列。

有两种做法。

一种是二分，一种转化为二维偏序。

```cpp
/*

for(int i = 1;i <= n;i ++)
  for(int j = 1;j <= n;j ++){
    if(a[i] == b[j]) dp[i][j] = max(dp[i - 1][j - 1] + 1,dp[i][j]);
    dp[i][j] = max(dp[i][j],dp[i - 1][j]);
    dp[i][j] = max(dp[i][j],dp[i][j - 1]);
  }



*/

const int N = 1e6 + 100;

template<class T>
struct BIT {
    T c[N];
    int size;
    void resize(int s) {
        size = s;
        for(int i = 0;i <= s;i ++) c[i] = 0;
    }

    T query(int x) {
        assert(x <= size);
        T res = 0;

        for (; x; x -= x & -x) {
            res = max(c[x],res);
        }

        return res;
    }

    void modify(int x, T val) {
        assert(x != 0);

        for (; x <= size; x += x & -x)
            c[x] = max(c[x],val);
    }
};

BIT<int> c;

void solve(){
  int n; cin >> n;
  c.resize(n);
  vector<int> a(n + 1),b(n + 1),dp(n + 1,0);
  map<int,int> mp;
   for(int i = 1;i <= n;i ++){ cin >> a[i]; mp[a[i]] = i;  }  

  for(int i = 1;i <= n;i ++){ cin >> b[i]; b[i] = mp[b[i]]; }
  
  int res = 0;
  for(int i = 1;i <= n;i ++){
    int x = c.query(b[i] - 1) + 1;
    res = max (res,x);
    c.modify(b[i],x);
  }

  cout << res << endl;
  
}
```


# E 最短路计数 

由于是到同一个点 $1$ 号节点的距离。

做一遍 $bfs$ 即可。

转移，从上一个深度转移过来。


```cpp
const int N = 1e6 + 100;
const int mod = 100003;
vector<int> g[N];
int res[N];
int d[N];

void solve(){
  int n,m; 
  cin >> n >> m;
  for(int i = 0;i < m;i ++){
    int u,v;
    cin >> u >> v;
    g[u].push_back(v);
    g[v].push_back(u);
  }
  queue<int> q; vector<bool> st(n + 1,false);

  q.push(1); st[1] = true; res[1] = 1;

  while(q.size()){
    auto u = q.front(); q.pop();
    for(auto v : g[u]){
        if(st[v] == false) {
          st[v] = true; 
          q.push(v);
          d[v] = d[u] + 1;
        }

        if(d[v] == d[u] + 1) (res[v] += res[u])%=mod;
    }
  }

  for(int i = 1;i <= n;i ++) cout << res[i] << "\n";

}
```


# F Constructing Roads

最小生成树的板子题。

每次选长度最小的边，贪心的看，两边节点是否在同一个连通块。

不在就合并，在的话就不选。

具体证明，反证法。


```cpp
const int N = 110;

int n,m;

struct edge
{
  int val;
  int u,v;

}e[N * N];

int fa[N];
int sz[N];

int find(int x){
  return fa[x] == x ? x : fa[x] = find(fa[x]);
}

void resize(int n){
  for(int i = 0;i <= n + 3;i ++){
   fa[i] = i; sz[i] = 1;
  }
}

bool same(int x,int y){
    return find(x) == find(y);
}

bool merge(int x,int y){
  x = find(x),y = find(y);
  if(x == y) return false;
  if(sz[x] <  sz[y]) swap(x, y);
  sz[x] += sz[y]; fa[y] = x;
  return true;
}

bool cmp(edge x,edge y){
  return x.val < y.val;
}


void solve(){

  cin >> n;
  for(int i = 1;i <= n;i ++)
    for(int j = 1;j <= n;j ++)
    { 
      cin >> e[(i - 1) * n + (j - 1)].val;
      e[(i - 1) * n + (j - 1)].u = i;
      e[(i - 1) * n + (j - 1)].v = j;
    } 
   
  sort(e,e + n * n,cmp);

  resize(n);

  cin >> m;

  for(int i = 0;i < m;i ++){
    int u,v; cin >> u >> v;
    merge(u,v);
  }

  int res = 0;

  for(int i = 0;i < n * n;i ++){
    if(same(e[i].u,e[i].v) == false){
      res += e[i].val;
      merge(e[i].u,e[i].v);
    }
  }

  cout << res << endl;


}
```

# G Flipping Cards 

2023 ccpc guilin 

二分+DP

二分的单调性满足。

对于当前的数字 $val$，我们判断是否可以凑成至少有 $(n+1)/2$ 个 大于等于的数。

我们用 $suf_{i}$ 记录后缀到 $i$ 的 $a$ 数组中大于等于 $val$ 的数量。
我们用 $pre$ 记录前缀到 $i$ 的 $a$ 数组中大于等于 $val$ 的数量。
我们用 $dp$ 记录到 $i$ 的当前阶段，且翻转的终点到 $i$，前 $i$ 个数中 大于等于 $val$ 的数量最大值。

那么数量最多便是 $max{ dp + suf_{i + 1} }$

```cpp
const int N = 3e5 + 100;

int n;
int a[N];
int b[N];
int suf[N];

void solve(){
  cin >> n;
  for(int i = 1;i <= n;i ++) cin >> a[i] >> b[i];

  auto check = [&](int val){
    for(int i = n;i >= 1;i --) suf[i] = suf[i + 1] + (a[i] >= val);

    int pre = 0, dp = 0;
    int res = suf[1];
    
    for(int i = 1;i <= n;i ++){
      dp = max(dp,pre) + (b[i] >= val); 
      pre += (a[i] >= val);
      res = max(res,dp + suf[i + 1]); // 以 i 结尾
    }

    return res >= (n + 1) / 2;

  };

  int l = 1, r = 1e9;
  while (l < r)
  {
    int mid = (l + r + 1) >> 1;
    if (check(mid)) l = mid;
    else r = mid - 1;
  }

  cout << l << endl;

}
```


