## 基础 01trie 模版

`01Trie` 是解决区间连续异或和和最大异或和的有利工具。

[HDU 4825](http://acm.hdu.edu.cn/showproblem.php?pid=4825)

给一个数集集合，给 $M$ 个询问。
每个询问 $ask_{i}$，给出集合中与他异或值最大的数。

```cpp
const int N = 1e5 + 100;
const int Bit = 32;

int n,m;

struct Trie
{
    int id; int root;
    int tr[N * 32][2];
    long long val[N * 32];

    void init(){
        id = 1;
        root = 0;
        memset(tr[0], 0,sizeof(tr));
    }

    void insert(long long x){
    
    int go = root;
    for(int i = Bit;i >= 0;i --){
        int u = (x >> i) & 1;
        if(tr[go][u] == 0){
            memset(tr[id], 0, sizeof(tr[id]));
            tr[go][u] = id;
            val[id ++] = 0;
        }
        go = tr[go][u];
    }
    val[go] = x;

   }

   long long query(long long x){
        int go = root;
        for(int i = Bit;i >= 0;i --){
            int u = (x >> i) & 1;
            if(tr[go][u ^ 1]) go = tr[go][u ^ 1];
            else go = tr[go][u];
        }
        return val[go];
   }

}f;



void solve()
{
    f.init();
    cin >> n >> m;
    for(int i = 0;i < n;i ++)
    {
        long long x; cin >> x;
        f.insert(x);
    }

    while(m --)
    {
        long long v; cin >> v;
        cout << f.query(v) << endl;
    }

}
```