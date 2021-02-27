##### 题意：一个序列d由n(n <= 300)位二进制数(0或1)表示，要让这个序列的a[i+m] = a[i], (1 <= m <= n)可以进行两种操作：1.翻转前k*m个数，k为任意整数；2.翻转任意一位的数，问最小的操作数

##### 分析：可以分块分类讨论； 1. 当 m <= sqrt(n) <= 17时，由于m的位数很小，可以枚举最后每一块m的状态，令f(i, j, k)表示从后往前考虑，考虑到第i块状态是j时，k表示是否翻转，需要的最小操作数，然后枚举f(0, j, k)的最小值即为所求。对于最后一块，由于其个数<= m,所以需要单独考虑 ，j从低位到高位对应于每一块从小到大顺序       

#####   2. else 这时 n / m <= sqrt(n) ; 这时每一块比较大，而块数比较小，所以可以考虑枚举每一块是否翻转，j从低到高表示从第1块到最后一块，统计此时的操作数，然后统计第一类操作数(取相应位上0,1个数最少)，取最小答案

```C++
#include <bits/stdc++.h>

using namespace std;

int d[400], n, m, cnt, tmp1, yu, k0, f[40][1<<17][2], num1[400], num0[400];
int main()
{
    int ans = 999999999;
    char ch = '1';
    while((ch = getchar()) != '\n')   //读取n位数，存在数组d中(从下标0开始)
    {
        if(ch == '1') d[n++] = 1;
        else d[n++] = 0;
    }

    scanf("%d", &m);
    k0 = (n - 1) / m + 1; yu = n % m == 0 ? m : n % m;       //总共有k0块，最后一块有yu个数
    if(m <= sqrt(n))                                         //每一块个数比较小
    {
        int tmp = (k0 - 1) * m;                             //最后一块在d的起始下标
        for(int j = 0; j < (1 << yu); j++)                  //先枚举yu位数的状态
        {
            for(int w = 0; w < yu; w++)
                if(((j>>w) & 1) ^ d[tmp+w]) f[k0-1][j][0]++;
            f[k0-1][j][1] = yu - f[k0-1][j][0] + 1;
        }
        for(int j = (1 << yu); j < (1 << m); j++)          //再枚举(yu+1)到m位状态的数
        {
            f[k0-1][j][0] = f[k0-1][j%(1<<yu)][0];
            f[k0-1][j][1] = f[k0-1][j%(1<<yu)][1];
        }

        for(int i = k0 - 2; i >= 0; i--)                    //从倒数第二块开始枚举
        {
            tmp -= m;
            for(int j = 0; j < (1 << m); j++)
            {
                for(int w = 0; w < m; w++)
                    if(((j>>w) & 1) ^ d[tmp+w]) f[i][j][0]++;
                f[i][j][1] = m - f[i][j][0];                //相邻两块k不同次数加1
                f[i][j][0] += min(f[i+1][j][1] + 1, f[i+1][j][0]);   
                f[i][j][1] += min(f[i+1][j][0] + 1, f[i+1][j][1]);
            }
        }

        for(int j = 0; j < (1 <<m); j++) ans = min(f[0][j][1], min(ans, f[0][j][0]));
        cout<<ans<<endl;
    }
    else                        //块数比较小
    {
        for(int i = 0; i < (1 << k0); i++)           //枚举块数的状态
        {
            int tmp = 0;
            for(int j = 1; j < k0; j++)
                if(((i>>j) & 1) ^ (i>>(j-1) & 1))  tmp++;
            tmp += ((i >>(k0 - 1)) == 1);           //先统计第一类操作数

            memset(num1, 0, sizeof(num1));
            memset(num0, 0, sizeof(num0));
            for(int j = 0; j < n; j++)               //统计第二类操作数
            {
                if((i>>(j/m) & 1) ^ d[j]) num1[j%m]++;    //和0异或是本身，和1异或是翻转
                else num0[j%m]++;
            }
            for(int j = 0; j < m; j++)
                tmp += min(num1[j], num0[j]);
            ans = min(ans, tmp);
        }
        cout<<ans<<endl;
    }
}
```

​                                