##### 给定一个n×m的矩阵，用1×2的方块去填满(彼此不能覆盖)，问有几种方法？

##### 分析：若是从上到下，从右到左进行dp，每次只规定方块能向左向上放，可以发现(i, j)的状态和(i-1,j)与(i,j-1)的状态有关，即和前m个相关，我记涂上为1，没涂为0，dp[1<<m]来枚举前m个的状态(更前面的全是1)。

```c++
int n, m, cur;
ll dp[2][1<<11];

void update(int pre, int crt)     //pre为之前m个的状态，crt为pre可以变成的状态(m+1个)      
{
    if(crt & (1<<m)) dp[cur][crt^(1<<m)] += dp[1-cur][pre];   //crt的第一个状态必须为1
}
int main()
{
    while(~scanf("%d %d", &n, &m))
    {
        if(n == 0 && m == 0) break;
        if(m > n) swap(n, m);
        memset(dp, 0, sizeof(dp));
        dp[cur][(1<<m)-1] = 1;
        for(int i = 0; i < n; i++)
            for(int j  = 0; j < m; j++)
            {
                cur ^= 1;                   //每次操作都会让cur在0与1之间翻转
                memset(dp[cur], 0, sizeof(dp[cur]));      //1-cur为前m个的轮廓线
                for(int k = 0; k < (1<<m); k++)        //枚举1-cur轮廓线的状态
                {                                      //分析可以得到新的轮廓线的状态
                    update(k, k<<1);                       //不涂，等待右下方
                    if(i && !(k & 1<<(m-1))) update(k, ((k | 1<<(m-1))<<1) + 1);  //往上
                    if(j && !(k & 1)) update(k, ((k | 1)<<1) + 1);    //往左
                }
            }
        printf("%lld\n", dp[cur][(1<<m)-1]);
    }
}
```

