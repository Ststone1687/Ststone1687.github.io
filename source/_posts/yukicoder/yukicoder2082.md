title: yukicoder No.2082 AND OR XOR 解題心得
date: 2022-10-19 00:00:00
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
- dp
-------------------------
# 題目名稱：[AND OR XOR](https://yukicoder.me/problems/no/2082)

### 題目敘述

對於非負整數的 $a,b$ ，定義一個二元運算子 $*$ 。
- $x * y = (A×(x$ $AND$ $y)+B×(x$ $OR$ $y)+C×(x$ $XOR$ $y))$ $mod$ $4$
長度為 $N$ 的數列 $X$ 如果滿足以下條件則被稱作 **好的數列** 。
- 每一項都由 $0,1,2,3$ 組成。
- $(...((X_1*X_2)*X_3)*...)*X_N = (...((X_N*X_{N-1})*X_{N-2})*...)*X_1$ 成立。
求 **好的數列** 的個數，由於答案可能會很大，所以請取除以 $998244353 $的餘數後出。

### 想法

這個問題可以用dp來處理，雖然 $N=5000$ ，但是這題的複雜度不是 $O(N^2)$ 而是 $O(N×4^5)$ 。
首先可以定義三個數列$P,X,R$，$X$代表任意的 **好的數列** ，然後滿足：
$1.$ $P_{i-1} * X_i = P_i$
$2.$ $R_i * X_i = R_{i-1}$
假設 $(...((X_1*X_2)*X_3)*...)*X_N = Y$ ，接著窮舉 $Y$ 。
而若 $R_1 = P_N = Y$ ，則 $(...((X_1*X_2)*X_3)*...)*X_N = (...((X_N*X_{N-1})*X_{N-2})*...)*X_1$ 成立。
於是可以定義狀態 $dp[i][j][k]$ 代表在位置 $i$ 時， $P_i=j$ 且 $R_i=k$ 有解。
因為 $P_1*X_2 = P_2$ 且 $R_2*X_2 = R_1$ ，且 $R_1 = Y$ ，然後可以列出初始狀態：
$dp[1][j][k] = \begin{cases}1 & ,  j*k=Y \\ 0 & , Otherwise \end{cases}$
接著可以列出轉移 $dp[i][P_i][R_i] = \sum \begin{cases}dp[i-1][P_{i-1}][R_{i-1}] & ,  P_{i-1}*X_{i}=P_{i} \quad and \quad R_{i}*X_{i}=R_{i-1} \\ 0 & ,  Otherwise \end{cases}$
因為 $P_{N-1}*X_N = P_N$ 且 $R_{N}*X_N = R_{N-1}$ ，且 $P_N = Y$ 。
所以最後需要檢查所有的 $P_{N-1} * R_{N-1} = Y$ ，把滿足條件的 $dp[N-1][P_{N-1}][R_{N-1}]$ 加起來就會是答案了。

### 程式碼   
```cpp
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
ll t,n,q,m,a,b,c,d,e,dp[5010][10][10],ans=0;
vector<ll> v,v1,v2,v3,v4;
char cc;
str s;

ll f(ll x,ll y){
    return ((a*(x&y))+(b*(x|y))+(c*(x^y)))%4;
}

void solve(){
    cin >> n;
    cin >> a >> b >> c;
    for(ll goal=0;goal<4;goal++){
        for(ll i=0;i<5010;i++){
            for(ll j=0;j<4;j++){
                for(ll k=0;k<4;k++){
                    dp[i][j][k] = 0;
                }
            }
        }
        for(ll i=0;i<4;i++){
            for(ll j=0;j<4;j++){
                if(f(i,j)==goal)
                    dp[1][i][j] = 1;
            }
        }
        for(ll i=2;i<n;i++){
            for(ll s1=0;s1<4;s1++){
                for(ll s2=0;s2<4;s2++){
                    for(ll r1=0;r1<4;r1++){
                        for(ll r2=0;r2<4;r2++){
                            for(ll ak=0;ak<4;ak++){
                                if(f(s1,ak)==s2&&f(r2,ak)==r1){
                                    dp[i][s2][r2] += dp[i-1][s1][r1];
                                    dp[i][s2][r2] %= mod2;
                                }
                            }
                        }
                    }
                }
            }
        }
        for(ll i=0;i<4;i++){
            for(ll j=0;j<4;j++){
                if(f(i,j)==goal)
                    ans +=  dp[n-1][i][j];
                    ans %= mod2;
            }
        }
    }
    cout << ans << '\n';
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