#### SPFA求负权+01分数规划

洛谷P1768 给定一个有向图，每条边有v和c，是否存在一个回路，使得∑vi / ∑ci最大

分析：若是存在∑vi / ∑ci > k, 那么最终答案一定比k要大，可化简为 k*∑ci - ∑vi < 0 即等价于把边权化为k*∑c - ∑v,问是否存在一个负环，若是存在负环，则让l = mid, 否则让r = mid, 一开始让l = 0,  若是这个图一开始就没有环，则一直找不到负环，最后l仍为0，输出-1                                                                                             spfa求负环用的是dfs的方法可以判断单点出发寻找负环，若是图一开始不连通的话，处理比较麻烦，所以将0作为超级源点，这样对答案不影响，同时只需从0出发即可；                                                                         

 寻找负环的思路是，存在负环时，最短路会一直在上面绕，我们猜测一条路径经过两次同一点即可判为存在负环。



##### 一开始vis要清0，dis要初始化成INF（这样第一次到达时才会被更新）                                                            

   若是要找正环，则将跑最短路换成跑最长路，并且dis初始化成-INF（这样第一次到达才会被更新）

```c++
struct edge
{
    int to, v, c;
};

vector<edge> G[maxn];
int vis[maxn], n, m;
double dis[maxn], l ,r, mid;

bool spfa(int s)
{
    vis[s] = 1;
    for(unsigned int i = 0; i < G[s].size(); i++)
    {
        edge e = G[s][i];
        double x = mid * e.c - e.v;
        if(dis[e.to] > dis[s] + x)             //如果这个点可以扩展
        {
            if(vis[e.to]) return true;         //如果在最短路中，则存在负环
            dis[e.to] = dis[s] + x;
            if(spfa(e.to)) return true;
        }
    }
    vis[s] = 0;                          //回溯
    return false;
}

int main()
{
    scanf("%d %d", &n, &m);
    int x, y, v, c;
    for(int i = 1; i <= m; i++)
    {
        scanf("%d %d %d %d", &x, &y, &v, &c);
        G[x].push_back((edge){y, v, c});
    }
    for(int i = 1; i <= n; i++)
        G[0].push_back((edge){i, 0, 0});

    l = 0, r = 201;
    while(r - l > eps)                     //二分判断
    {
        memset(dis, INF, sizeof(dis));
        memset(vis, 0, sizeof(vis));
        dis[0] = 0;
        mid = (l + r) / 2;
        if(spfa(0)) l = mid;
        else r = mid;
    }
    if(l == 0) printf("-1\n");
    else printf("%.1f\n", l);

}
```

