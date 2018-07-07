[题面在这里](http://www.lydsy.com/JudgeOnline/problem.php?id=1036)

典型的树链剖分……不解释了
不懂的可以看[这里](http://blog.csdn.net/linkfqy/article/details/69706777)

```
#include<cstdio>
#include<algorithm>
using namespace std;
const int maxn=30005,maxe=60005;
int n,q;
int son[maxe],nxt[maxe],lnk[maxn],tot;
void add(int x,int y){
    son[++tot]=y;nxt[tot]=lnk[x];lnk[x]=tot;
}
inline int red(){
    int tot=0,f=1;char ch=getchar();
    while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=getchar();}
    while ('0'<=ch&&ch<='9') tot=tot*10+ch-48,ch=getchar();
    return tot*f;
}
int siz[maxn],fa[maxn],dep[maxn],H_son[maxn],top[maxn],id[maxn],tim;
void dfs1(int x){
    siz[x]=1;dep[x]=dep[fa[x]]+1;
    for (int j=lnk[x];j;j=nxt[j])
     if (fa[x]!=son[j]){
        fa[son[j]]=x;dfs1(son[j]);
        if (siz[son[j]]>siz[H_son[x]]) H_son[x]=son[j];
        siz[x]+=siz[son[j]];
     }
}
void dfs2(int x,int lst){
    top[x]=lst;id[x]=++tim;
    if (H_son[x]) dfs2(H_son[x],lst);
    for (int j=lnk[x];j;j=nxt[j])
     if (fa[x]!=son[j]&&H_son[x]!=son[j]) dfs2(son[j],son[j]);
}
struct node{
    int x,l,r,mx,s;
    node(int x=0,int y=0) {l=x,r=y;x=mx=s=0;}
}tre[maxn*4];
void buildtree(int x,int l,int r){
    tre[x]=node(l,r);
    if (l==r) return;
    int mid=l+r>>1;
    buildtree(x<<1,l,mid);buildtree(x<<1|1,mid+1,r);
}
void ist(int x,int k,int w){
    if (k<tre[x].l||tre[x].r<k) return;
    if (tre[x].l==tre[x].r) {tre[x].x=tre[x].mx=tre[x].s=w;return;}
    ist(x<<1,k,w);ist(x<<1|1,k,w);
    tre[x].mx=max(tre[x<<1].mx,tre[x<<1|1].mx);
    tre[x].s=tre[x<<1].s+tre[x<<1|1].s;
}
int qrymx(int x,int l,int r){
    if (r<tre[x].l||tre[x].r<l) return 0x80000000;
    if (l<=tre[x].l&&tre[x].r<=r) return tre[x].mx;
    return max(qrymx(x<<1,l,r),qrymx(x<<1|1,l,r));
}
int qrys(int x,int l,int r){
    if (r<tre[x].l||tre[x].r<l) return 0;
    if (l<=tre[x].l&&tre[x].r<=r) return tre[x].s;
    return qrys(x<<1,l,r)+qrys(x<<1|1,l,r);
}
int askmx(int x,int y){
    int res=0x80000000;
    while (top[x]!=top[y]){
        if (dep[top[x]]<dep[top[y]]) swap(x,y);
        res=max(res,qrymx(1,id[top[x]],id[x]));
        x=fa[top[x]];
    }
    if (id[x]>id[y]) swap(x,y);
    res=max(res,qrymx(1,id[x],id[y]));
    return res;
}
int asks(int x,int y){
    int res=0;
    while (top[x]!=top[y]){
        if (dep[top[x]]<dep[top[y]]) swap(x,y);
        res+=qrys(1,id[top[x]],id[x]);
        x=fa[top[x]];
    }
    if (id[x]>id[y]) swap(x,y);
    res+=qrys(1,id[x],id[y]);
    return res;
}
int main(){
    n=red();
    for (int i=1,x,y;i<n;i++) x=red(),y=red(),add(x,y),add(y,x);
    dep[0]=siz[0]=fa[1]=tim=0;
    dfs1(1);dfs2(1,1);
    buildtree(1,1,n);
    for (int i=1;i<=n;i++) ist(1,id[i],red());
    q=red();
    while (q--){
        char s[10];scanf("%s",s);
        if (s[1]=='H'){
            int u=red(),t=red();
            ist(1,id[u],t);
        }else
        if (s[1]=='M'){
            int x=red(),y=red();
            printf("%d\n",askmx(x,y));
        }else{
            int x=red(),y=red();
            printf("%d\n",asks(x,y));
        }
    }
    return 0;
}
```