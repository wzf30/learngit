##### Dinic算法

##### 邻接表+dfs递归版本

```C++
struct edge
{
    int to, cap, rev;            //rev记录对应反向边在对应邻接表的下标
};
vector<edge> G[maxn];              
int level[maxn], iter[maxn];       //level记录bfs时与s的距离，iter记录当前弧

void add_edge(int from, int to, int cap)            
{
    G[from].push_back((edge){to, cap, G[to].size()});       
    G[to].push_back((edge){from, 0, G[from].size()-1});
}

void bfs(int s, int t)
{
    queue<int> que;
    memset(level, -1, sizeof(level));
    level[s] = 0;
    que.push(s);
    while(!que.empty())
    {
        int v = que.front(); que.pop();
        for(int i = 0; i < G[v].size(); i++)
        {
            edge &e = G[v][i];               
            if(e.cap > 0 && level[e.to] < 0)    //其可达并且第一次到达
            {
                level[e.to] = level[v] + 1;
                if(e.to == t) return;
                que.push(e.to);
            }
        }
    }
}

int dfs(int v, int t, int f)             //从v到t的最大流
{
    if(v == t) return f;
    for(int &i = iter[v]; i < G[v].size(); i++)        //弧优化
    {
        edge &e = G[v][i];
        if(e.cap > 0 && level[v] < level[e.to])        //可达并且是下一层
        {
            int d = dfs(e.to, t, min(f, e.cap));
            if(d > 0)
            {
                e.cap -= d;
                G[e.to][e.rev].cap += d;
                return d;
            }
        }
    }
    return 0;
}

int max_flow(int s, int t)
{
    int flow = 0, f;          
    for(;;)
    {
        bfs(s, t);                //首先bfs构造分层图
        if(level[t] < 0) return flow;          //t不可达直接退出
        memset(iter, 0, sizeof(iter));         
        while((f = dfs(s, t, INF)) > 0)        //每次增加一条最短增广路
            flow += f;
    }
}

```

##### kuangbin优化版：1.采用链式前向星代替vector   2.手写队列与栈节约bfs与dfs时间                                                           注意：head一开始时需要初始化成-1，链式前向星进行dfs要确定跳出的节点

```c++
struct edge
{
    int to, flow, cap, next;
}e[maxm];

int tol, head[maxn];                  //用链式前向星来记录
void add_edge(int u,int v, int w)
{
    e[tol].to = v; e[tol].flow = 0; e[tol].cap = w;
    e[tol].next = head[u]; head[u] = tol++;
    e[tol].to = u; e[tol].flow = w; e[tol].cap = w;
    e[tol].next = head[v]; head[v] = tol++;
}

int level[maxn], Q[maxn];            //level记录分层，Q为手写队列
void bfs(int s, int t, int n)
{
    memset(level, -1, sizeof(level[0]) * n);      //一开始初始化为-1
    int k1 = 0, k2 = 0;
    level[s] = 0;
    Q[k2++] = s;
    while(k1 < k2)
    {
        int u = Q[k1++];
        for(int i = head[u]; i != -1; i = e[i].next)
        {
            int v = e[i].to;
            if(e[i].cap > e[i].flow && level[v] == -1)   //可达并且是第一次到达
            {
                level[v] = level[u] + 1;
                if(v == t) return;                       //如果是t则bfs完成
                Q[k2++] = v;
            }
        }
    }
    return;
}

int sta[maxn], cur[maxn];             //sta记录栈（对应的边在e里的下标）， cur来复刻head
int dfs(int s, int t, int n)
{
    int maxflow = 0, tail = 0, u = s;       //tail记录栈的长度，u来记录当前遍历到的顶点
    for(int i = 0; i < n; i++) cur[i] = head[i];
    while(cur[s] != -1)
    {
        if(u == t)                        //如果遍历到t
        {
            int tp = INF;
            for(int i = tail - 1; i >= 0; i--) tp = min(tp, e[sta[i]].cap - e[sta[i]].flow);  //首先算出这条增光路的贡献
            maxflow += tp;
            for(int i = tail - 1; i >= 0; i--)
            {
                e[sta[i]].flow += tp;
                e[sta[i]^1].flow -= tp;
                if(e[sta[i]].cap - e[sta[i]].flow == 0)    //如果发现某一个i已经满流，则栈的长度变成i-1
                    tail = i;
            }
            u = e[sta[tail]^1].to;   //通过其对应边来找到u的位置
        }
        else if(cur[u] != -1 && e[cur[u]].cap > e[cur[u]].flow && level[u] < level[e[cur[u]].to]) //这条边可以扩展的话
        {
            sta[tail++] = cur[u];
            u = e[cur[u]].to;
        }
        else                                  //顶点u现在搜索到的这条边不能扩展
        {
            while(u != s && cur[u] == -1)          //顶点u的边搜索完了
                u = e[sta[--tail]^1].to;
            cur[u] = e[cur[u]].next;               //没有搜索完，搜索下一条边
        }
    }
    return maxflow;
}

int max_flow(int s, int t, int n)
{
    int flow = 0;
    for(;;)
    {
        bfs(s, t, n);
        if(level[t] < 0) return flow;
        flow += dfs(s, t, n);
    }
}

int main()
{
    int n, m, x, y, z;
    scanf("%d %d", &n, &m);
    memset(head, -1, sizeof(head[0]) * (n + 2));
    for(int i = 1; i <= n; i++)
    {
        scanf("%d %d", &x, &y);
        add_edge(0, i, x);
        add_edge(i, n + 1, y);
    }
    for(int i = 0; i < m; i++)
    {
        scanf("%d %d %d", &x, &y, &z);
        add_edge(x, y, z);
        add_edge(y, x, z);
    }
    printf("%d\n", max_flow(0, n+1, n+2));

}

```



