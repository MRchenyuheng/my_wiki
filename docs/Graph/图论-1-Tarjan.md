# 图论

## Tarjan
给一个  n 个点 m 条边的图，输出所有的强连通分量。

```cpp
#define pb push_back

int n,m;
const int N = 1e5 + 100;
vector<int> e[N];

int dfn[N],low[N],ins[N],bel[N];
int idx,cnt;

stack<int> stk;
vector<vector<int>> scc;

void dfs(int u)
{
    dfn[u] = low[u] = ++idx;
    ins[u] = true; stk.push(u);

    for(auto v : e[u])
    {
        if(!dfn[v]) dfs(v);
        if(ins[v]) low[u] = min(low[u],low[v]);
    }
    if(dfn[u] == low[u])
    {
        vector<int> c; ++ cnt;
        while(true){
            int v = stk.top();
            c.pb(v);ins[v] = false;
            bel[v] = cnt;
            stk.pop();
            if(u == v) break;
        }
        sort(c.begin(),c.end());
        scc.pb(c);
    }
}

void solve()
{ 
   cin >> n >> m;
   for(int i = 0;i < m;i ++){
    int u,v; cin >> u >> v;
    e[u].pb(v);
   }
   for(int i = 1;i <= n;i ++) if(!dfn[i]) dfs(i);
   sort(scc.begin(),scc.end());
   for(auto c : scc){
    for(auto u : c) cout << u << ' ';
    cout << endl;
   }
}
```
