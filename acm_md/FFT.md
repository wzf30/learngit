**FFT**

需要注意的是，数组大小要开到(n + m)的两倍， 防止len越界

还要注意a[i].x 和 a[i].y 到len 要初始化成0

```c++
#include <bits/stdc++.h>
#define here printf("Passing [%s] in LINE %d\n", __FUNCTION__, __LINE__);
#define debug(x) cout << #x << ":\t" << (x) << endl;

using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int, int> P;
const int maxn = 5e4 + 10;
const int maxm = 2e5 + 10;
const int INF = 0x3f3f3f3f;
const int mod = 998244353;
const double pi = acos(-1.0);

//多项式A阶数为n,B为m，
struct complexx{
    double x, y;
    complexx(double xx = 0, double yy = 0) {x = xx, y = yy;}
}a[maxn];

double coss[maxn], sinn[maxn];
int rev[maxn];
complexx operator + (complexx a, complexx b) {return complexx(a.x + b.x, a.y + b.y);}
complexx operator - (complexx a, complexx b) {return complexx(a.x - b.x, a.y - b.y);}
complexx operator * (complexx a, complexx b) {return complexx(a.x * b.x - a.y * b.y, a.y * b.x + a.x * b.y);}

void fft(int len, complexx *a, int o){
    for(int i = 0; i <= len; i ++) if(i < rev[i]) swap(a[i], a[rev[i]]);
    for(int j = 1; j < len; j <<= 1){//j枚举的是合并区间长度的一半，即把两个长度为j的序列合成一个长度为2*j的序列
        complexx wn = complexx(coss[j], o * sinn[j]);
        for(int k = 0; k < len; k += (j << 1)){//k为当前处理的区间的开头
            complexx w0 = complexx(1, 0);
            for(int i = 0; i < j; i ++, w0 = w0 * wn){//i为对应位
                complexx X = a[i + k], Y = w0 * a[i + j + k];
                a[i + k] = X + Y;
                a[i + k + j] = X - Y;//合并
            }
        }
    }
}

int n, m;
int main(){
    scanf("%d %d", &n, &m);
    for(int i = 0; i <= n; i ++) scanf("%lf", &a[i].x);
    for(int i = 0; i <= m; i ++) scanf("%lf", &a[i].y);
    
    //板子，要给定n + m 确定 len
    int len = 1, l = 0;
    for(;len <= n + m; len <<= 1, l++);
    for(int i = 0; i <= len; i ++) rev[i] = (rev[i >> 1] >> 1) | ((i&1) << (l - 1));//rev[i]位将i按二进制后的值（类似数位dp的思想）
    for(int i = 1; i <= len; i <<= 1) coss[i] = cos(pi / i), sinn[i] = sin(pi / i);//注意这里pi不用乘2
    fft(len, a, 1);//DFT
    for(int i = 0; i <= len; i ++)
        a[i] = a[i] * a[i];
    fft(len, a, -1);//IDFT

    //得到次数 0 到 n+m 的系数
    for(int i = 0; i <= n + m; i ++) printf("%d ", (int)(a[i].y / 2 / len + 0.5));      //记得除len
    
    return 0;
}

```





大乘数模板

```c++
#include <bits/stdc++.h>
#define here printf("Passing [%s] in LINE %d\n", __FUNCTION__, __LINE__);
#define debug(x) cout << #x << ":\t" << (x) << endl;

using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int, int> P;
const int maxn = 2e5 + 10;
const int maxm = 2e5 + 10;
const int INF = 0x3f3f3f3f;
const int mod = 998244353;
const double pi = acos(-1.0);

//多项式A阶数为n,B为m，
struct complexx{
    double x, y;
    complexx(double xx = 0, double yy = 0) {x = xx, y = yy;}
}a[maxn];

double coss[maxn], sinn[maxn];
int rev[maxn];
complexx operator + (complexx a, complexx b) {return complexx(a.x + b.x, a.y + b.y);}
complexx operator - (complexx a, complexx b) {return complexx(a.x - b.x, a.y - b.y);}
complexx operator * (complexx a, complexx b) {return complexx(a.x * b.x - a.y * b.y, a.y * b.x + a.x * b.y);}

void fft(int len, complexx *a, int o){
    for(int i = 0; i <= len; i ++) if(i < rev[i]) swap(a[i], a[rev[i]]);
    for(int j = 1; j < len; j <<= 1){//j枚举的是合并区间长度的一半，即把两个长度为j的序列合成一个长度为2*j的序列
        complexx wn = complexx(coss[j], o * sinn[j]);
        for(int k = 0; k < len; k += (j << 1)){//k为当前处理的区间的开头
            complexx w0 = complexx(1, 0);
            for(int i = 0; i < j; i ++, w0 = w0 * wn){//i为对应位
                complexx X = a[i + k], Y = w0 * a[i + j + k];
                a[i + k] = X + Y;
                a[i + k + j] = X - Y;//合并
            }
        }
    }
}

int n, m;
char S1[50100], S2[50100], ans[110000];

//要注意对于不同的n, m值，会有不同的len值和L值，所以rev数组需要更新
//此外需要保证[n+1, len]和[m+1, len]没有系数为0
int main(){

    while(~scanf("%s %s", S1, S2))
    {
        char *s1 = S1, *s2 = S2;
        int n = strlen(s1) - 1, m = strlen(s2) - 1, mark = 0;
        if(S1[0] == '-')  mark ^= 1,  s1++, n--;
        if(S2[0] == '-')  mark ^= 1,  s2++, m--;

        int len = 1, l = 0;
        for(;len <= n + m; len <<= 1, l++);
        for(int i = 0; i <= len; i++) rev[i] = (rev[i >> 1] >> 1) | ((i&1) << (l - 1));//rev[i]位将i按二进制后的值（类似数位dp的思想）
        for(int i = 1; i <= len; i <<= 1) coss[i] = cos(pi / i), sinn[i] = sin(pi / i);

        for(int i = 0; i <= n; i ++) a[i].x = s1[n-i] - '0';
        for(int i = n + 1; i <= len; i++)  a[i].x = 0;
        for(int i = 0; i <= m; i ++) a[i].y = s2[m-i] - '0';
        for(int i = m + 1; i <= len; i++)  a[i].y = 0;

        fft(len, a, 1);//DFT
        for(int i = 0; i <= len; i ++)
            a[i] = a[i] * a[i];
        fft(len, a, -1);//IDFT

        int cnt = 0, cur = 0;
        for(int i = 0; i <= m + n; i++)
        {
            cur += (int)(a[i].y / 2 / len + 0.5);
            ans[++cnt] = cur % 10 + '0';
            cur  /= 10;
        }
        while(cur)
        {
            ans[++cnt] = cur % 10 + '0';
            cur /= 10;
        }
        while(cnt >= 1 && ans[cnt] == '0')  cnt--;
        if(cnt == 0)  printf("0\n");
        else
        {
            if(mark == 1)  putchar('-');
            for(int i = cnt; i >= 1; i--)
                printf("%c", ans[i]);
            putchar('\n');
        }
    }
}


```



hdu 4609  给定N条边，边长在1e5，求多少种方案可以组成三角形

可以用fft预处理出两条边和为 i 有几种方案，然后每次选定一条最长边，看看两条边和大于它有几种方案(两条都在左，两条都在右，一左一右，包含自己)。

```c++
#include <bits/stdc++.h>
#define here printf("Passing [%s] in LINE %d\n", __FUNCTION__, __LINE__);
#define debug(x) cout << #x << ":\t" << (x) << endl;

using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int, int> P;
const int maxn = 4e5 + 10;
const int maxm = 2e5 + 10;
const int INF = 0x3f3f3f3f;
const int mod = 998244353;
const double pi = acos(-1.0);

//多项式A阶数为n,B为m，
struct complexx{
    double x, y;
    complexx(double xx = 0, double yy = 0) {x = xx, y = yy;}
}a[maxn];

double coss[maxn], sinn[maxn];
int rev[maxn];
complexx operator + (complexx a, complexx b) {return complexx(a.x + b.x, a.y + b.y);}
complexx operator - (complexx a, complexx b) {return complexx(a.x - b.x, a.y - b.y);}
complexx operator * (complexx a, complexx b) {return complexx(a.x * b.x - a.y * b.y, a.y * b.x + a.x * b.y);}

void fft(int len, complexx *a, int o){
    for(int i = 0; i <= len; i ++) if(i < rev[i]) swap(a[i], a[rev[i]]);
    for(int j = 1; j < len; j <<= 1){//j枚举的是合并区间长度的一半，即把两个长度为j的序列合成一个长度为2*j的序列
        complexx wn = complexx(coss[j], o * sinn[j]);
        for(int k = 0; k < len; k += (j << 1)){//k为当前处理的区间的开头
            complexx w0 = complexx(1, 0);
            for(int i = 0; i < j; i ++, w0 = w0 * wn){//i为对应位
                complexx X = a[i + k], Y = w0 * a[i + j + k];
                a[i + k] = X + Y;
                a[i + k + j] = X - Y;//合并
            }
        }
    }
}

int num[100010], pro[100010], t, n, m;
ll ans[400010], pre[400010];

int main(){
    scanf("%d", &t);
    while(t--)
    {
        int mx = 0;
        scanf("%d", &n);
        m = n;
        for(int i = 1; i <= n; i++)
        {
            scanf("%d", &pro[i]);  //pro记录边长，num[i] 记录边长为 i 的边有多少条
            num[pro[i]]++;
        }
        sort(pro + 1, pro + 1 + n);               //排序
        mx = pro[n];                //当前边长最大值为mx
        for(int i = 0; i <= mx; i++) a[i].x = a[i].y = num[i];     

        int len = 1, l = 0;                   //fft板子
        for(;len <= 2 * mx; len <<= 1, l++);
        for(int i = 0; i <= len; i ++) rev[i] = (rev[i >> 1] >> 1) | ((i&1) << (l - 1));
        for(int i = 1; i <= len; i <<= 1) coss[i] = cos(pi / i), sinn[i] = sin(pi / i);
        for(int i = mx + 1; i <= len; i++)  a[i].x = a[i].y = 0;      //记得清空
        fft(len, a, 1);
        for(int i = 0; i <= len; i ++)
        a[i] = a[i] * a[i];
        fft(len, a, -1);//IDFT

        for(int i = 0; i <= mx * 2; i++)  ans[i] = (ll)(a[i].y / 2 / len + 0.5);  //两个数和为i 的方案有ans[i]个
        for(int i = 0; i <= mx; i++)  ans[i*2] -= num[i];          //去掉一个边算两个
        for(int i = 0; i <= mx * 2; i++)  ans[i] /= 2;                   //一个方案算了两次
        for(int i = 1; i <= mx * 2; i++)  pre[i] = pre[i-1] + ans[i];          //计算前缀和

        ll res = 0;
        for(int i = 1; i <= n; i++)
        {
            res += pre[mx*2] - pre[pro[i]];
            res -= n - 1;                       //本身与其他
            res -=  (ll)(n - i) * (i - 1);          //一个左,一个右
            res -= (ll)(n - i) * (n - i - 1) / 2;     //两个在右
        }
        ll tot = (ll) n * (n - 1) * (n - 2) / 6;
        printf("%.7f\n", (double)res / tot);

        fill(num, num + 1 + mx, 0);           //多组样例
    }
    return 0;
}

```



![image-20201218221102479](C:\Users\admin\AppData\Roaming\Typora\typora-user-images\image-20201218221102479.png)

 可以进行CDQ分治，先计算前半部分[L, mid]的 f 值，然后计算这一部分 f 值对于后半部分 f 值的贡献

(f[L+1] + f[L+2]... + f[mid]) \* (g[1] + g[2] + g[R-mid])  乘出来对答案的贡献。 

再计算后半部分[mid+1, R]

```c++
#include <bits/stdc++.h>
#define here printf("Passing [%s] in LINE %d\n", __FUNCTION__, __LINE__);
#define debug(x) cout << #x << ":\t" << (x) << endl;

using namespace std;
typedef long long ll;
typedef unsigned long long ull;
typedef pair<int, int> P;
const int maxn = 8e5 + 10;
const int maxm = 2e5 + 10;
const int INF = 0x3f3f3f3f;
const int mod = 313;
const double pi = acos(-1.0);

//多项式A阶数为n,B为m，
struct complexx{
    double x, y;
    complexx(double xx = 0, double yy = 0) {x = xx, y = yy;}
}a[maxn];

double coss[maxn], sinn[maxn];
int rev[maxn];
complexx operator + (complexx a, complexx b) {return complexx(a.x + b.x, a.y + b.y);}
complexx operator - (complexx a, complexx b) {return complexx(a.x - b.x, a.y - b.y);}
complexx operator * (complexx a, complexx b) {return complexx(a.x * b.x - a.y * b.y, a.y * b.x + a.x * b.y);}

void fft(int len, complexx *a, int o){
    for(int i = 0; i <= len; i ++) if(i < rev[i]) swap(a[i], a[rev[i]]);
    for(int j = 1; j < len; j <<= 1){//j枚举的是合并区间长度的一半，即把两个长度为j的序列合成一个长度为2*j的序列
        complexx wn = complexx(coss[j], o * sinn[j]);
        for(int k = 0; k < len; k += (j << 1)){//k为当前处理的区间的开头
            complexx w0 = complexx(1, 0);
            for(int i = 0; i < j; i ++, w0 = w0 * wn){//i为对应位
                complexx X = a[i + k], Y = w0 * a[i + j + k];
                a[i + k] = X + Y;
                a[i + k + j] = X - Y;//合并
            }
        }
    }
}

int f[maxn], g[maxn], n;

void CDQ(int L, int R)
{
    if(L >= R)  return;
    int mid = (L + R) / 2;
    CDQ(L, mid);

    int len = 1, l = 0, mx = mid - L + R - L;
    for(;len <= mx; len <<= 1, l++);
    for(int i = L; i <= mid; i++)  a[i-L].x = f[i];
    for(int i = mid + 1; i - L <= len; i++)  a[i-L].x = 0;
    for(int i = 1; i <= R - L; i++)  a[i-1].y = g[i];
    for(int i = R - L + 1; i - 1 <= len; i++)  a[i-1].y = 0;

    for(int i = 0; i <= len; i ++) rev[i] = (rev[i >> 1] >> 1) | ((i&1) << (l - 1));//rev[i]位将i按二进制后的值（类似数位dp的思想）
    for(int i = 1; i <= len; i <<= 1) coss[i] = cos(pi / i), sinn[i] = sin(pi / i);//注意这里pi不用乘2
    fft(len, a, 1);//DFT
    for(int i = 0; i <= len; i ++)
        a[i] = a[i] * a[i];
    fft(len, a, -1);//IDFT

    for(int i = mid - L; i <= R - L - 1; i++)  f[i+L+1] = ((int)(a[i].y / 2 / len + 0.5) + f[i+L+1]) % mod;

    CDQ(mid + 1, R);
}


int main()
{
    while(~scanf("%d", &n) && n)
    {
        for(int i = 1; i <= n; i++)  scanf("%d", &g[i]), g[i] %= mod;
        for(int i = 1; i <= n; i++)  f[i] = g[i];
        CDQ(1, n);
        printf("%d\n", f[n]);
    }
}
```

