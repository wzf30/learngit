#### 归并dp

  一开始有$2^n$ 个队伍， 某个人是k只队伍的球迷。 首先一开始1和2打， 3和4打.... 输的进入lower group, 赢的进入upper group, 比如分别是1,3,5,8和2,4,6,7, 然后1,3打，5,8打，2,4打，6,7打，1,3的输者直接被淘汰，1,3的赢者打2,4的输者，赢者为lower group, 而2,4的赢者在upper group, 直到两个组各剩一个人，然后最后打一局，问球迷可以看到几场自己喜欢球队的比赛？

  第一轮比赛是分组，之后每进行一轮人数少一半，第i轮是每$2^i$个人中只有一个胜者，一个只输了一场的人，当第i+1轮时，相连的$2^i$ 个人的胜者对打，此局胜者保留，输者和两个败者的赢者打，赢的保留，因此想到归并的方式，当进行到第n轮时，前$2^{n-1}$ 和后 $2^{n-1}$ 各保留一个，然后第$n+1$轮巅峰之战。

  于是可以通过归并dp的方式，每次幂次合并，可定义$dp[i][j][x][y]$, 其中i 表示从第i个人开始的$2^j$个人，最后赢的人是否为喜欢的(x的01表示), 输的人是否为喜欢的(y的01表示), 由于第一轮比较特殊，特殊考虑，最后一轮就一局也得单独考虑。

```c++
#include <bits/stdc++.h>

using namespace std;
typedef long long ll;
const int maxn = 1e7 + 20;
const int INF = 0x3f3f3f3f;

int mark[1<<17], dp[18][1<<17][2][2], n, k, tmp, ans;

int main()
{
    scanf("%d %d", &n, &k);
    for(int i = 1; i <= k; i++)
    {
        scanf("%d", &tmp);
        mark[tmp-1] = 1;
    }
    memset(dp, -INF, sizeof(dp));      //一开始要初始化，这样防止在不存在的情况下考虑

    for(int i = 1; i <= n; i++)
    {
        if(i == 1)
        {
            for(int j = 0; j < (1<<n); j += 2)   //存在的情况，有喜欢的为1，否则为0
            {
                dp[1][j][mark[j]][mark[j+1]] = mark[j] | mark[j+1];
                dp[1][j][mark[j+1]][mark[j]] = mark[j] | mark[j+1];
            }
            continue;
        }
 
        for(int j = 0; j < (1<<n); j += (1<<i))    //第2轮到第n轮
        {
            for(int x1 = 0; x1 < 2; x1++)        //前半部分赢者x1输者y1
                for(int y1 = 0; y1 < 2; y1++)
                    for(int x2 = 0; x2 < 2; x2++)  //后半部分赢者x2输者y2
                        for(int y2 = 0; y2 < 2; y2++)
                        {
                            int cost = dp[i-1][j][x1][y1] + dp[i-1][j+(1<<(i-1))][x2][y2];
                            if(x1 || x2) cost++;        //赢者x1与x2对打
                            if(y1 || y2) cost++;        //输者y1与y2对打
                       //枚举八种情况
                            dp[i][j][x1][x2] = max(dp[i][j][x1][x2], cost + (x2 | y1));
                            dp[i][j][x1][x2] = max(dp[i][j][x1][x2], cost + (x2 | y2));

                            dp[i][j][x1][y1] = max(dp[i][j][x1][y1], cost + (x2 | y1));
                            dp[i][j][x1][y2] = max(dp[i][j][x1][y2], cost + (x2 | y2));

                            dp[i][j][x2][x1] = max(dp[i][j][x2][x1], cost + (x1 | y1));
                            dp[i][j][x2][x1] = max(dp[i][j][x2][x1], cost + (x1 | y2));

                            dp[i][j][x2][y1] = max(dp[i][j][x2][y1], cost + (x1 | y1));
                            dp[i][j][x2][y2] = max(dp[i][j][x2][y2], cost + (x1 | y2));
                        }
        }
    }
    ans = max(ans, dp[n][0][0][0]);         //第n+1轮单独考虑
    ans = max(ans, dp[n][0][0][1] + 1);
    ans = max(ans, dp[n][0][1][0] + 1);
    ans = max(ans, dp[n][0][1][1] + 1);

    printf("%d\n", ans);
}

```

