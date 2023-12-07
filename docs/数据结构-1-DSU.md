# 数据结构

## DSU

```cpp
#include<numeric>

struct DSU{
    vector<int>fa, sz;
    DSU(int n) : fa(n + 1),sz(n+1, 1){
        iota(fa.begin(), fa.end(), 0);
    }

    int find(int x){
        return fa[x] == x ? x : fa[x] = find(fa[x]);
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

    int size(int x){return sz[find(x)]; }
};
```
## 带权DSU


+ 强制在线
+ `1 l r x`  代表  $a_{l} - a_{r} = x$ ，若矛盾则忽略。
+ `2 l r`  代表回答 $a_{l} - a_{r}$ ,如果推不出则忽略。

   我们记向量 $vec_{x} = a_{x} - a_{f[ x ]}$

   当已知 $vec_{x} = a_{x} - a_{y} $ 和 $vec_{y} = a_{y} - a_{z}$ 时，

   我们求 `x`  到 ` z `  的向量时，可以通过 $vec_{x} + vec_{y}$ 来计算

   递归压缩了父节点，得到已知信息 $vec_{par[x]}$ 为 $a_{par[x]} - a_{root}$

   那么节点 `x` 到 `root` 的关系就可以使用向量加法法则 $vec_{ x } +  vec_{par[x]}$ 得到

   而合并的时候，只需要更新一组各自根节点`root` 的差值即可。

```cpp
int find(int x){
    if(f[x] == x) return x; 
    int p = f[x]; 
    find(p);

    w[x] = w[x] + w[p];
    return f[x] = find(f[x]);
}

void solve(){
    int n,q;
    cin >> n >> q;

    for(int i = 1;i <= n;i ++){
        f[i] = i;
        w[i] = 0;
    }
    int t = 0;
    for(int i = 0;i < q;i ++){
        int ty, l, r;
        cin >> ty >> l >> r;
        l = (l + t) % n + 1;
        r = (r + t) % n + 1;
        if(ty == 2){
            if(find(l) != find(r)) continue;
            cout << w[l] - w[r] << endl;
            t = abs(w[l] - w[r]);
        }else if(ty == 1){
            int x;
            cin >> x;
            if(find(l) == find(r)) continue;
             w[f[l]] = x - w[l] + w[r];
             f[f[l]] = f[r];
        }
    }
}
```
