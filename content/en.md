+++
title = 'En'
date = 2022-11-18T23:29:15+08:00
draft = false
+++
```
#include<bits/stdc++.h>
#define P 2000
#define N 5005
#define M 400005
using namespace std;

int n,s,s2,t;
int cnt = 1;
int vis[N];
int dis[N];
int head[N];
long long cost;

struct edge{
    int to;
    int nxt;
    int val;
    int w;
}e[N<<1];

struct node{
    int x;
    int y;
    friend bool operator <(node a,node b)
    {
        if(a.x != b.x)
        {
            return a.x < b.x;
        }
        return a.y < b.y;
    }
}p[N];

void add(int x,int y,int z,int w)
{
    e[++cnt] = (edge){y,head[x],z,w};
    head[x] = cnt;
}
void addedge(int u,int v,int c,int w){
    printf("%d %d\n",u,v);
    add(u,v,c,w);add(v,u,0,-w);
}
bool spfa()
{
    memset(vis,0,sizeof(vis));
    memset(dis,0x3f,sizeof(dis));
    dis[s] = 0;
    vis[s] = 1;
    queue<int> q;
    q.push(s);
    while(!q.empty())
    {
        int u = q.front();
        q.pop();
        vis[u] = 0;
        for(int i=head[u];i;i=e[i].nxt)
        {
            int v = e[i].to;
            if(dis[v] < dis[u] + e[i].w && e[i].val)
            {
                dis[v] = dis[u] + e[i].w;
                if(!vis[v])
                {
                    q.push(v);
                    vis[v] = 1;
                }
            }
        }
    }
    return dis[t] != 0x3f3f3f3f;
}

int dfs(int u,int flow)
{
    if(u == t)
    {
        vis[t] = 1;
        return flow;
    }
    int used = 0;
    vis[u] = 1;
    for(int i=head[u];i;i=e[i].nxt)
    {
        int v = e[i].to;
        if((!vis[v] || v == t) && e[i].val != 0 && dis[v] == dis[u] + e[i].w)
        {
            int minflow = dfs(v,min(e[i].val,flow - used));
            if(minflow != 0)
            {
                 cost += e[i].w * minflow;
                 e[i].val -= minflow;
                 e[i^1].val += minflow;
                 used += minflow;
                 if(used == flow) break;
            }
        }
    }
    return used;
}

void costflow()
{
    while(spfa())
    {
        vis[t] = 1;
        while(vis[t])
        {
            memset(vis,0,sizeof(vis));
            dfs(s,1<<30);
        }
    }
}

int main()
{
    s = 0;
    t = 4001;
    s2 = 4002;
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        cin>>p[i].x>>p[i].y;
    }
    sort(p+1,p+n+1);
    for(int i=1;i<=n;i++){
        printf("%d : %d,%d\n",i,p[i].x,p[i].y);
    }
    for(int i=1;i<=n;i++)
    {
        int maxy = 0,it=0;
        for(int j=i-1;j>=1;j--)
        {
            if(p[j].y > p[i].y || p[j].y < maxy)
            {
                continue;
            }
            maxy = max(maxy,p[j].y);
            it=j;
        }
        addedge(it,i,1,0);
    }

    costflow();
    cout<<cost<<endl;
    return 0;
}
```