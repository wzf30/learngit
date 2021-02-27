#### 无向图求最小割   Stoer-Wagner算法

##### 注意：对于图中任意两点s和t, 它们要么属于最小割的两个不同集中, 要么属于同一个集。如果是后者, 那么合并s和t后并不影响最小割. 基于这么个思想, 如果每次能求出图中某两点之间的最小割, 然后更新答案后合并它们再继续求最小割, 就得到最终答案了. 

##### 算法分析： 1. min=INF，首先任意固定一个顶点P                                                                                              2.从点P用“类似”prim的算法扩展出“最大生成树”，记录最后扩展的顶点和最后扩展的边                        3.计算最后扩展到的顶点的切割值（即与此顶点相连的所有边权和），若比min小更新min                   4.合并最后扩展的那条边的两个端点为一个顶点                                                                                             5.转到2，合并N-1次后结束

##### 注意：1.Prim算法更新的d[i]，表示的是现在树上的点到i点的边权和，每次用Prim算法扩展到最后加进去的两个点为s, t, 则s-t割的最小值即为最后扩展的边的值。

##### 每次d要初始化成成0(中间有边则++)， 底下模板点从0开始，若不联通，则最小割为0

```c++
int dis[maxn], v[maxn], d[maxn][maxn], vis[maxn];

int Stoer_Wagner(int n)
{
    int res = INF;
    for(int i = 0; i < n; i++)         //初始化第i个节点就是i
        v[i] = i;
    while(n > 1)                      //每一次循环一次Prim,少掉一个节点
    {
        int maxp = 1, pre = 0;
        for(int i = 1; i < n; i++)          //首先找到可以扩展的最大的点，记为maxp
        {
            dis[v[i]] = d[v[0]][v[i]];
            if(dis[v[i]] > dis[v[maxp]])
                maxp = i;
        }
        memset(vis, 0, sizeof(vis));
        vis[v[0]] = 1;
        for(int i = 1; i < n; i++)
        {
            if(i == n - 1)                                      //如果扩展到最后一个点
            {
                res = min(res, dis[v[maxp]]);                      //更新最小割
                for(int j = 0; j < n; j++)
                {
                    d[v[pre]][v[j]] += d[v[j]][v[maxp]];        //将v[maxp]的边加到v[[pre]上
                    d[v[j]][v[pre]] = d[v[pre]][v[j]];
                }
                v[maxp] = v[--n];             //此时所有的v[maxp]已经合并到了v[pre],需要去除v[maxn]，便将v[n-1]移到v[maxn]的位置
                break;
            }
            vis[v[maxp]] = 1;                 //扩展最大的点
            pre = maxp;
            maxp = -1;
            for(int j = 1; j < n; j++)        //更新dis的值，并找出下一个可以扩展的最大的点
            {
                if(!vis[v[j]])
                {
                    dis[v[j]] += d[v[pre]][v[j]];
                    if(maxp == -1 || dis[v[maxp]] < dis[v[j]])
                        maxp = j;
                }
            }
        }
    }
    return res;
}
```

