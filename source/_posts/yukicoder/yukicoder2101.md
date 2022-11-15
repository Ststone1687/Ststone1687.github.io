title: yukicoder No.2101 [Cherry Alpha N] ずっとこの数列だったらいいのに 解題心得
date: 2022-10-18 00:00:00
categories: yukicoder
sticky: 100
comments: true
expire: true
only:
- home
- category
- tag
tags:
- yukicoder
- bit
-------------------------
# 題目名稱：[如果一直都是這個數列就好了](https://yukicoder.me/problems/no/2101)

### 題目敘述

ゼルコバ送給チェリー一個長度為 $N$ 的數列當作禮物。
而事實上，チェリー收到的數列從第一天白天開始，每天都漸漸地在改變。
- 在第 $d$ 天時， $i=1,2,...,N$ ，如果 $d \ge T_i$ 且第 $i$ 為正數，則數列第 $i$ 項的值將會減少 $1$ 。
チェリー拿到數列時是第 $0$ 天晚上，接著會有 $Q$ 個問題，問題 $1,..,Q$ 。
- 問題 $q$ ：第 $D_q$ 晚時，チェリー的數列上第 $L_q$ 到 $R_q$ （包含兩端）共 $(R_q-L_q+1)$ 個數字的總和是多少？
而在チェリー所在的世界的日夜變化如下：
- 第 $0$ 天夜晚 $→$ 第 $1$ 天白天 $→$ 第 $1$ 天夜晚 $→$ 第 $2$ 天白天 $→ ⋯ →$ 第 $(d−1)$ 天夜晚 $→$ 第 $d$ 天白天 $→$ 第 $d$ 天夜晚 $→$ 第 $(d+1)$ 白天 $→ ⋯$

### 題目大意

他會給你個一長度為 $N$ 的序列，序列中的第 $i$ 個位置在時間 $T_i$ 後每天都會固定減少 $1$ ，最小只會變成 $0$ 。給你 $Q$ 筆詢問，問第 $d$ 天時的某段序列總和是多少。

### 想法

先將 $Q$ 筆詢問用時間順序排好，接著用BIT維護整個序列。
如果 $d \ge N_i+T_i$ ，則可以將 $N_i$ 直接移除。
否則若 $d \ge T_i$ ，則另外維護一個 $T_i$ 的BIT。
詢問中如果 $(N_i+T_i) > d \ge T_i$ ，則第 $i$ 個位置的大小會是 $N_i-((d+1)-T_i)$ ，假設這樣的位置有 $k$ 個，所以可以得到答案 $=$ 
$($ $SUM_{N_r} - SUM_{N_{\,l-1}}$ $)$ $-$ $($ $(k×(d+1))$ $-$ $($ $SUM_{T_r} - SUM_{T_{\,l-1}}$ $)$ $)$ ，時間複雜度 $O(N log N)$ 。 

### 程式碼   
```cpp
#pragma GCC optimize("Ofast")
#pragma GCC optimize(1)
#pragma GCC optimize(2)
#pragma GCC optimize(3)
#pragma GCC target("popcnt")
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef string str;
typedef pair<ll,ll> pii;
#define F first
#define S second
#define pb push_back
#define pq priority_queue
#define all(x) (x).begin(),(x).end()
#define bug(x) cerr << (x) << '\n'
#define yn(x) cout << (x==1?"Yes\n":"No\n")
#define PI 3.14159265358979323
#define Ststone ios_base::sync_with_stdio(0);cin.tie(0);cout.tie(0)
const ll max_n=2e5+20,max_int=2147483647,mod1=1e9+7,mod2=998244353;
ll dx[8] = {1,0,0,-1,1,1,-1,-1};
ll dy[8] = {0,1,-1,0,1,-1,1,-1};
ll t,n,q,m,k,a,b,c,d,e,f,x,bit[max_n][3];
vector<ll> v1,v2,v3,v4;
vector<pii> v,era,out;
vector<pair<pii,pii>> ans;
char cc;
str s;

void update(ll index,ll dalta,ll num){
    while(index<=max_n){
        bit[index][num] += dalta;
        index += index&(-index);
    }
}

ll query(ll index,ll num){
    ll sum = 0;
    while(index){
        sum += bit[index][num];
        index -= index&(-index);
    }
    return sum;
}

void solve(){
    cin >> n;
    for(ll i=1;i<=n;i++){
        cin >> a >> b;
        v1.pb(a);
        v2.pb(b);
        v.pb({b,i});
        era.pb({a+b-1,i});
        update(i,a,0);
    }
    cin >> q;
    for(ll i=0;i<q;i++){
        cin >> a >> b >> c;
        ans.pb({{a,i},{b,c}});
    }
    sort(all(v));
    sort(all(era));
    sort(all(ans));
    ll l = 0,r = 0;
    for(ll i=0;i<q;i++){
        ll x = ans[i].F.F,y = ans[i].S.F,z = ans[i].S.S;
        while(l<n&&v[l].F<=x){
            update(v[l].S,v[l].F,1);
            update(v[l].S,1,2);
            l++;
        }
        while(r<n&&era[r].F<=x){
            update(era[r].S,-v1[era[r].S-1],0);
            update(era[r].S,-v2[era[r].S-1],1);
            update(era[r].S,-1,2);
            r++;
        }
        ll sum = query(z,0)-query(y-1,0) -( (x+1)*(query(z,2)-query(y-1,2)) - (query(z,1)-query(y-1,1)));
        out.pb({ans[i].F.S,sum});
    }
    sort(all(out));
    for(ll i=0;i<q;i++){
        cout << out[i].S << '\n';
    }
    
}

int main(){
    Ststone;
    solve();
}
```
-------------------------
<script type="text/x-mathjax-config">
      MathJax.Hub.Config({tex2jax: {inlineMath: [['$','$'], ['\\(','\\)']]}});
</script>
<script type="text/javascript" src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
-------------------------