给定一个长为 n 的序列 a，求出其 **m 阶差分或前缀和。**
结果的每一项都需要对 1004535809 取模。

给定n, m, type（0为前缀和，1为差分）



运用生成函数，$F(x) = \sum_{i = 0}^∞ a_i x^i$

前缀和，则乘 $1 + x + x^2 + ... x^∞ = \frac{1}{1-x}$, k维前缀就是 $F(x) * \frac{1}{(1-x)^k}$

![image-20210203220758989](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210203220758989.png)

差分，则乘  $(1 - x) $:

![image-20210203221131815](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210203221131815.png)

①原根 G = 3，要注意 Gi 是模意义下的逆元，要进行替换

②A数组的次数是n, B数组的次数是m, 都是从0开始填充

③每次先要init,计算出Len,L,Inv, 如果有多次运算，记得A数组[n+1, len]的部分要清零(同理B数组)

④板子四行，得到的答案在A中

```c++
#include <bits/stdc++.h>
#define here printf("modassing [%s] in LINE %d\n", __FUNCTION__, __LINE__);
#define debug(x) cout << #x << ":\t" << (x) << endl;

using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int, ll> P;
const int maxn = 1e5 + 10;
const int maxm = 2e6 + 10;
const int INF = 0x3f3f3f3f;
const ll mod = 1004535809;
const double pi = acos(-1.0);

ll fpow(ll x, ll k)
{
	ll ans = 1, base = x;
	while(k)
    {
		if(k & 1) ans = (ans * base) % mod;
		base = (base * base) % mod, k >>= 1 ;
	}
	return ans ;
}

ll gi()
{
	char cc = getchar();
	ll cn = 0, flus = 1 ;
	while( cc < '0' || cc > '9' ) {  if( cc == '-' ) flus = - flus ; cc = getchar() ; }
	while( cc >= '0' && cc <= '9' )  cn = ( cn * 10 + cc - '0' ) % mod, cc = getchar() ;
	return cn * flus;
}

const ll Gi = 334845270;
const ll G = 3;
ll n, m, type;
ll A[maxn<<2], B[maxn<<2], R[maxn<<2];
ll len, L, Inv;

void init(int x)
{
	len = 1, L = 0;
	while(len <= x) len <<= 1, ++L;
	for(int i = 0; i <= len; i++) R[i] = (R[i >> 1] >> 1 ) | ( ( i & 1 ) << ( L - 1 )) ;
	Inv = fpow(len, mod - 2);
}


void NTT(ll *a, int type) {
	for(int i = 0; i < len; ++i)
        if(R[i] > i) swap(a[i], a[R[i]]);

	for(int k = 1; k < len; k <<= 1 )
    {
		ll d = fpow(( type == 1 ) ? G : Gi, ( mod - 1 ) / ( k << 1 )), g;
		for(int i = 0; i < len; i += ( k << 1 ))
        {
            g = 1;
            for(int j = i; j < i + k; ++ j, g = (g * d) % mod)
            {
                ll Nx = a[j], Ny = (a[j + k] * g) % mod;
                a[j] = ( Nx + Ny ) % mod, a[j + k] = ( Nx - Ny + mod) % mod;
            }
        }
	}
	if(type != 1) for(int i = 0; i <= len; i++) a[i] = a[i] * Inv % mod;
}

int main()
{
	n = gi();  m = gi();   type = gi();
	for(int i = 0; i < n; i++) scanf("%lld", &A[i]);
	B[0] = 1;
	if(type == 0)
    {
        for(int i = 1; i <= n; i++)
            B[i] = B[i - 1] * (m + i - 1) % mod * fpow(i, mod - 2) % mod;
    }
    else
    {
        for(int i = 1; i <= n; i++)
            B[i] = (-B[i - 1] * (m - i + 1 + mod) % mod * fpow(i, mod - 2) % mod + mod) % mod;
    }

	init(n + n);
	NTT(A, 1), NTT(B, 1);
	for(int i = 0; i <= len; i++)  A[i] = A[i] * B[i] % mod;
	NTT(A, -1);

	for(int i = 0; i < n; i++)  printf("%lld ", A[i]) ;
	return 0;
}

```



![image-20210204205200572](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210204205200572.png)



![image-20210204205246161](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20210204205246161.png)

组合数可以预先处理出来



```c++
#include <bits/stdc++.h>
#define here printf("modassing [%s] in LINE %d\n", __FUNCTION__, __LINE__);
#define debug(x) cout << #x << ":\t" << (x) << endl;

using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int, ll> P;
const int maxn = 3e5 + 10;
const int maxm = 2e6 + 10;
const int INF = 0x3f3f3f3f;
const ll mod = 998244353;
const double pi = acos(-1.0);

ll fpow(ll x, ll k)
{
	ll ans = 1, base = x;
	while(k)
    {
		if(k & 1) ans = (ans * base) % mod;
		base = (base * base) % mod, k >>= 1 ;
	}
	return ans ;
}

ll Gi = 334845270;
ll G = 3;
ll A[maxn<<1], B[maxn<<1], R[maxn<<1];
ll len, L, Inv;

void init(int x)
{
	len = 1, L = 0;
	while(len <= x) len <<= 1, ++L;
	for(int i = 0; i <= len; i++) R[i] = (R[i >> 1] >> 1 ) | ( ( i & 1 ) << ( L - 1 )) ;
	Inv = fpow(len, mod - 2);
}


void NTT(ll *a, int type) {
	for(int i = 0; i < len; ++i)
        if(R[i] > i) swap(a[i], a[R[i]]);

	for(int k = 1; k < len; k <<= 1 )
    {
		ll d = fpow(( type == 1 ) ? G : Gi, ( mod - 1 ) / ( k << 1 )), g;
		for(int i = 0; i < len; i += ( k << 1 ))
        {
            g = 1;
            for(int j = i; j < i + k; ++ j, g = (g * d) % mod)
            {
                ll Nx = a[j], Ny = (a[j + k] * g) % mod;
                a[j] = ( Nx + Ny ) % mod, a[j + k] = ( Nx - Ny + mod) % mod;
            }
        }
	}
	if(type != 1) for(int i = 0; i <= len; i++) a[i] = a[i] * Inv % mod;
}

int n, k, x, mx, q;
int cnt[maxn], pre1[maxn], pre2[maxn], X[maxn];
ll fac[maxn<<1], invfac[maxn<<1], res[maxn<<2];

ll C(ll n, ll m)
{
    if(n < m)  return 0;
    return fac[n] * invfac[m] % mod * invfac[n - m] % mod;
}

int main()
{
    Gi = fpow(G, mod - 2);
    scanf("%d %d", &n, &k);
    for(int i = 1; i <= n; i++)
    {
        scanf("%d", &x);
        cnt[x]++;
        mx = max(mx, x);
    }
    for(int i = 1; i <= k; i++)  scanf("%d", &X[i]);
    for(int i = 1; i <= k; i++)  mx = max(mx, X[i]);

    for(int i = 1; i <= mx; i++)  pre1[i] += pre1[i-1] + (cnt[i] == 1);        
    for(int i = 1; i <= mx; i++)  pre2[i] += pre2[i-1] + (cnt[i] >= 2);
    fac[0] = invfac[0] = 1;
    int lim = max(pre1[mx], pre2[mx] * 2);
    for(int i = 1; i <= lim; i++)  fac[i] = fac[i-1] * i % mod;        //预先处理逆元
    invfac[lim] = fpow(fac[lim], mod - 2);
    for(int i = lim - 1; i >= 1; i--)  invfac[i] = invfac[i+1] * (i + 1) % mod;

    for(int i = 1; i <= k; i++)
    {
        x = X[i];
        int k1 = pre1[x-1], k2 = pre2[x-1];
        A[0] = B[0] = 1;

        init(k1 + 2 * k2);
        for(int j = 1; j <= k1; j++)  A[j] = C(k1, j) * fpow(2, j) % mod;
        for(int j = 1; j <= 2 * k2; j++)  B[j] = C(2 * k2, j);
        for(int j = k1 + 1; j <= len; j++)  A[j] = 0;
        for(int j = 2 * k2 + 1; j <= len; j++)  B[j] = 0;

        NTT(A, 1), NTT(B, 1);
        for(int j = 0; j <= len; j++)  A[j] = A[j] * B[j] % mod;
        NTT(A, -1);

        for(int j = 0; j <= k1 + 2 * k2; j++) res[j+x+1] = (res[j+x+1] + A[j]) % mod;
    }

    scanf("%d", &q);
    for(int i = 1; i <= q; i++)
    {
        scanf("%d", &x);
        if(x % 2 == 0)  printf("%lld\n", res[x/2]);
        else printf("%d\n", 0);
    }
}
```





![img](file:///F:\Tencent Files\469802172\Image\Group2\@1\0U\@10UDNQXNQG6}`~_MR~IKRU.jpg)