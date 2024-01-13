# 2023 ICPC Jiangxi Provincial Contest



# L

输出 `n-1`即可。

# A

留空


# I

节子有一棵树，树上有 $n$ 个节点。每条边的值为 $w$ 。那么她将执行 $q$ 个操作。操作有两种：

类型 1：给出三个整数 $x y z$ ，改变 $x$和 $y$ 之间简单路径上每条边的值。如果操作前某条边的值是 $w$ ，那么操作后该值就会变成 $w \oplus z$ 。 $\oplus$ 表示位操作 xor。

类型 2：给出整数 $x$ ，并打印与 $x$ 相连的所有边的值的比特 XOR。

## 思考

我们只需要将边权记录到点权上即可。

```cpp
void solve()
{
    int n, q;
    cin >> n >> q;
    vector<int> a(n + 1);
 
    for (int i = 1; i < n; i ++ )
    {
        int x, y, w;
        cin >> x >> y >> w;
        a[x] ^= w;
        a[y] ^= w;
    }
    for (int i = 1; i <= q; i ++ )
    {
        int op;
        cin >> op;
        if (op == 1)
        {
            int x, y, w;
            cin >> x >> y >> w;
            a[x] ^= w;
            a[y] ^= w;
        }else
        {
            int x;
            cin >> x;
            cout << a[x] << '\n';
        }
    }
}
```

# C

sg() 打表。


# J

注意到每个点的数值有势能，每次更新都会减小。

并且，每个点的数值不会超过 `1e5` 即可。

```cpp
void solve()
{
    int n, m;
    cin >> n;
    vector<int> b(n + 1);
    for (int i = 1; i <= n; i ++ ) cin >> b[i];
    cin >> m;
    for (int i = 1; i <= m; i ++ )
    {
        int op;
        cin >> op;
        if (op == 0)
        {
            int x, y;
            cin >> x >> y;
            b[x] = min(b[x], y);
 
        }else
        {
            int x;
            cin >> x;
            int ans = inf;
            for (int j = x; j >= 1; j -- )
            {
                if ((j - x) * (j - x) > 200000) break;
                ans = min(ans, (j - x) * (j - x) + b[j]);
            }
 
            for (int j = x; j <= n; j ++ )
            {
                if ((j - x) * (j - x) > 200000) break;
                ans = min(ans, (j - x) * (j - x) + b[j]);
            }
            cout << ans << '\n';
        }
    }
}   
```

# k

注意到，操作一的含意就是交换两个相邻的差分值。
而答案就是要求最小的几个差分值，对答案没有影响。

```cpp
const int N = 1e6 + 100;
int a[N];
vector<int> s;
 
void solve()
{
    int n; cin >> n;
    for(int i = 1;i <= n;i ++) cin >> a[i];
    for(int i = 2;i <= n;i ++) s.push_back(a[i - 1] - a[i]);
    s.push_back(0);
 
    sort(s.begin(),s.end());
 
    for(int i = 1;i < (int)s.size();i ++)s[i] += s[i - 1];
 
    //for(int i = 0;i < s.size();i ++) cout << s[i] << ' ';
    //cout << endl; 
 
    int q; cin >> q;
    
    for(int i = 1; i <= q;i ++)
    {
        int op; cin >> op;
        if(op == 0)
        {
            int x; cin >> x;
        }
        else
        {
            int k;cin >> k;
            cout << s[s.size() - k] << endl;
        }
    }
 
}
```

# F

暂略

# B

注意到，就是要求有多少次越过 `M`。

所以就是求最后的那个数值里面包含多少 `M`即可。

一些小细节，求和前要先取模。还有一些边界细节。


```cpp
void solve()
{
    int k;
    cin >> k;
    vector<int> a(k);
    for (int i = 0; i < k; i ++ ) cin >> a[i];
    int n, m, x;
    cin >> n >> m >> x;
 
    for (int i = 0; i < k; i ++ ) a[i] %= m;
    x %= m;
 
    int res = 0;
    for (int i = 0; i < k; i ++ ) res += a[i];
    int all = x;
    all += (n / k) * res;
 
    for (int i = 0; i < (n % k); i ++ )
    {
        all += a[i];
    }
 
    int ans = n - (all / m);
    cout << ans << '\n';
 
}   
```

# H

好题！


节子有一个长度为 $n$  的排列组合。**保证 $n$ 是偶数。** 现在她把前面的 $\frac n2$ 个数字分成序列 $A$ ，把最后的 $\frac n2$ 个数字分成序列 $B$ ，然后进行以下运算：

- 如果 $A$ 和 $B$ 都为空，则停止运算。
- 如果 $A$ 和 $B$ 中只有一个空序列，则选择另一个序列的第一个元素，将其删除并放到序列 $P$ 的末尾。
- 如果 $A$ 和 $B$ 都不是空数列，则比较 $A$ 和 $B$ 的第一个元素，选择较小的一个，删除它并将它放到数列 $P$ 的末尾。

不难发现序列 $P$ 也是一个排列。

现在节子想知道，对于所有的 $n!$ 排列，是否存在一种排列可以通过上述操作得到排列 $P$ 。

