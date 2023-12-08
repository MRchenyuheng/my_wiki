# Hash

## Hash1

```cpp
struct Hash{
  const int sz;
  const int B1 = 29;  const int MOD1 = 1e9 + 7;
  const int B2 = 131; const int MOD2 = 1e9 + 9;

  vector<int>ha1, ha2,rha1,rha2;
  vector<int>pow1,pow2;
  string s;

  Hash(string &ss,int n) : s(ss),sz(n),ha1(n+1),ha2(n+1),rha1(n+1),rha2(n+1),pow1(n+1),pow2(n+1)
  {
    pow1[0] = pow2[0] = 1;
    for(int i = 1;i <= sz;i ++){
      pow1[i] = pow1[i - 1] * B1 % MOD1;
      pow2[i] = pow2[i - 1] * B2 % MOD2;
    }
    for(int i = 1;i <= sz;i ++){
      ha1[i] = (ha1[i - 1] * B1 + s[i - 1]) % MOD1;
      ha2[i] = (ha2[i - 1] * B2 + s[i - 1]) % MOD2;
      rha1[i] = (rha1[i - 1] * B1 + s[sz - i]) % MOD1;
      rha2[i] = (rha2[i - 1] * B2 + s[sz - i]) % MOD2;
    }
  }

  pair<int,int> cacl_hash(int l,int r)
  {
      int res1 = ((ha1[r] - ha1[l - 1] * pow1[r - l + 1]) % MOD1 + MOD1) % MOD1;
      int res2 = ((ha2[r] - ha2[l - 1] * pow2[r - l + 1]) % MOD2 + MOD2) % MOD2;
      return {res1, res2};
  }

  pair<int, int> cacl_rhash(int l, int r)
  {
      int res1 = ((rha1[sz - l + 1] - rha1[sz - r] * pow1[r - l + 1]) % MOD1 + MOD1) % MOD1;
      int res2 = ((rha2[sz - l + 1] - rha2[sz - r] * pow2[r - l + 1]) % MOD2 + MOD2) % MOD2;
      return {res1, res2};
  }

  bool is_palindrome(int l, int r) //判断s[l, r]是否为回文串
  {
      return cacl_hash(l, r) == cacl_rhash(l, r);
  }

  pair<int,int> add(pair<int,int> a,pair<int,int> b)
  {
      int res1 = (a.first + b.first) % MOD1;
      int res2 = (a.second + b.second) % MOD2;
      return {res1, res2};
  }

  pair<int,int> mul(pair<int,int> a, int k)
  {
    int res1 = a.first * pow1[k] % MOD1;
    int res2 = a.second * pow2[k] % MOD2;
    return {res1, res2};
  }

};

int n;

void solve()
{
  cin >> n;

  string x;

  vector<pair<int,int> > tmp;

  for(int i = 1;i <= n;i ++)
  {
    cin >> x;
    struct Hash t(x,x.size());
    tmp.push_back( t.cacl_hash(1,x.size()) );

  }

  sort(tmp.begin(),tmp.end());

  int sz = tmp.size();

  int res = 1;
  for(int i = 1;i < sz;i ++) if(tmp[i] != tmp[i - 1]) res ++;
  cout << res << endl;

}
```

## Hash2

```cpp
typedef unsigned long long ULL;
const int maxn = 305*305;
/* 字符集大小 */
const int sigma = maxn;
/* hash次数 */
const int HASH_CNT = 2;
int n;
int s[maxn];
 /* char* 1-bas
 * sum[i] = s[i]+s[i-1]*Seed+s[i-2]*Seed^2+...+s[1]*Seed^(i-1)*/
ULL Prime_Pool[] = {1998585857ul,23333333333ul};
ULL Seed_Pool[]={911,146527,19260817,91815541};
ULL Mod_Pool[]={29123,998244353,1000000009,4294967291ull};
struct Hash{
    ULL Seed,Mod;
    ULL bas[maxn];ULL sum[maxn];
    int perm[sigma];
    Hash(ULL Seed, ULL Mod):Seed(Seed),  Mod(Mod){
        bas[0] = 1;
        for (int i = 1; i <= n; i++){
            bas[i] = bas[i - 1] * Seed % Mod;
        }
    }
    void init(){
        for (int i=1;i<=n;i++){
            sum[i] = (sum[i-1]*Seed%Mod+s[i])%Mod;
        }
    }
    /*random_shuffle 离散化id，防止kill_hash*/
    void indexInit(){
        iota(perm + 1, perm + 1 + sigma, 1);
        random_shuffle(perm+1,perm+1+sigma);
        for (int i=1;i<=n;i++){
            sum[i] = (sum[i-1]*Seed%Mod+perm[s[i]])%Mod;
        }
    }
    ULL getHash(int l,int r){
        return (sum[r]-sum[l-1]*bas[r-l+1]%Mod+Mod)%Mod;
    }
}hasher[HASH_CNT] = {Hash(Seed_Pool[0], Mod_Pool[0]), Hash(Seed_Pool[1], Mod_Pool[1])};
map<pair<pair<ULL,ULL>,int>,int>veid;int vecnt;
map<string,int>id;int idcnt;
vector<int> pos[maxn];
string a[maxn];
int sumL[maxn];

void solve(){
    cin>>n;
    for (int i=1;i<=n;i++){
        cin>>a[i];
        if (!id[a[i]])id[a[i]] = ++idcnt;
        s[i] = id[a[i]];
        sumL[i] = sumL[i-1]+a[i].size();
    }
    for (int i=0;i<HASH_CNT;i++){
        hasher[i].indexInit();
    }
    int ans = sumL[n]+n-1;
    for (int i=1;i<=n;i++){
        for (int j=1;j<=n;j++){
            ULL hash1 = hasher[0].getHash(i,j);
            ULL hash2 = hasher[1].getHash(i,j);
            int len = j-i+1;
            pair<pair<ULL,ULL>,int> x = {{hash1,hash2},len};
            if (veid[x]==0)veid[x] = ++vecnt;
            pos[veid[x]].push_back(i);
        }
    }
    int maxDelta =0;
    for (auto x:veid){
        int len = x.first.second;
        int i = x.second;
        sort(pos[i].begin(),pos[i].end());
        int num =0;
        for (int j=0,last = -maxn;j<pos[i].size();j++){
            if (pos[i][j]>=last+len){
                last = pos[i][j];
                num++;
            }
        }
        if (num==1)continue;
        int cost1 = sumL[pos[i][0]+len-1]-sumL[pos[i][0]-1]+len-1;
        int cost2 = len;
        int tempDelta = (cost1-cost2)*num;
        maxDelta = max(maxDelta,tempDelta);
    }
    cout<<ans-maxDelta<<endl;
    return ;
}
```

+ 参考资料

[模版题](https://www.luogu.com.cn/problem/P3370)

[参考模版1](https://blog.csdn.net/m0_51028279/article/details/127166841?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169915840016800180660511%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=169915840016800180660511&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-1-127166841-null-null.142^v96^pc_search_result_base6&utm_term=%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%93%88%E5%B8%8C%E6%9D%BF%E5%AD%90&spm=1018.2226.3001.4187)

[参考模版2](https://www.cnblogs.com/FrankChen831X/p/12401300.html)

[参考模版3](https://blog.csdn.net/weixin_51797626/article/details/126831022?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522169915754416800213084270%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fall.%2522%257D&request_id=169915754416800213084270&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v1~rank_v31_ecpm-5-126831022-null-null.142^v96^pc_search_result_base6&utm_term=%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%93%88%E5%B8%8C%E6%A8%A1%E7%89%88&spm=1018.2226.3001.4187)


[oi-wiki](https://oi-wiki.org/string/hash/)


