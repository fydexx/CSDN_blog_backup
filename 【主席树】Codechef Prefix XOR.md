[题面在这里](https://s3.amazonaws.com/codechef_shared/download/translated/SNCKEL17/mandarin/PREFIXOR.pdf)

一道很巧妙的题目……

构造前缀异或和
那么$S_jxorS_{i-1}\ge S_{j-1}xorS_{i-1}$
考虑$S_j$和$S_{j-1}$二进制下的LCP后一位
那么就对$S_{i-1}$的某一位产生了限制
这样可以线性求出每个i最远的j

然后把这个右端点塞到主席树里
对于每次询问，考虑左端点在$[L,R]$内，右端点如果在$[L,R]$内则对答案的贡献是$Right-i+1$，否则答案的贡献是$R-i+1$
随便统计一下就可以了

示例程序：

```
#include<cstdio>
#include<algorithm>
#define LL long long
using namespace std;
inline char nc(){
	static char buf[100000],*p1=buf,*p2=buf;
	return p1==p2&&(p2=(p1=buf)+fread(buf,1,100000,stdin),p1==p2)?EOF:*p1++;
}
inline int red(){
	int res=0,f=1;char ch=nc();
	while (ch<'0'||'9'<ch) {if (ch=='-') f=-f;ch=nc();}
	while ('0'<=ch&&ch<='9') res=res*10+ch-48,ch=nc();
	return res*f;
}

const int maxn=400005;
int n,t,q,must[32][2],s[maxn],ans[maxn];
struct node{
	node *l,*r;
	int L,R,cnt;LL sum;
	node() {}
	node(int ll,int rr,int c,LL s):L(ll),R(rr),cnt(c),sum(s) {}
	void pushup() {cnt=l->cnt+r->cnt;sum=l->sum+r->sum;}
}nil,base[10000000];
typedef node* P_node;
P_node null,rot[maxn],len;
void init(){
	nil=node(0,0,0,0);null=&nil;
	null->l=null->r=null;len=base;
}
P_node newnode(int a,int b,int c,LL d){
	*len=node(a,b,c,d);
	return len++;
}
P_node build(int L,int R){
	P_node x=newnode(L,R,0,0);
	if (L==R) {x->l=x->r=null;return x;}
	int mid=L+R>>1;
	x->l=build(L,mid);x->r=build(mid+1,R);
	return x;
}
P_node add(P_node fa,int k){
	P_node x=newnode(fa->L,fa->R,fa->cnt,fa->sum);
	if (x->L==x->R){
		x->cnt++;x->sum+=k;
		x->l=x->r=null;
		return x;
	}
	int mid=x->L+x->R>>1;
	if (k<=mid) x->l=add(fa->l,k),x->r=fa->r;else
	 x->l=fa->l,x->r=add(fa->r,k);
	x->pushup();
	return x;
}
int Cnt(P_node l,P_node r,int L,int R){
	if (l==null||r==null) return 0; 
	if (L>R||R<l->L||l->R<L) return 0;
	if (L<=l->L&&l->R<=R) return r->cnt-l->cnt;
	return Cnt(l->l,r->l,L,R)+Cnt(l->r,r->r,L,R);
}
LL Sum(P_node l,P_node r,int L,int R){
	if (l==null||r==null) return 0;
	if (L>R||R<l->L||l->R<L) return 0;
	if (L<=l->L&&l->R<=R) return r->sum-l->sum;
	return Sum(l->l,r->l,L,R)+Sum(l->r,r->r,L,R);
}
int main(){
	n=red(),t=red();
	for (int i=1;i<=n;i++) s[i]=s[i-1]^red();
	for (int i=0;i<=30;i++) must[i][0]=must[i][1]=n+1;
	for (int i=n;i>=1;i--){
		int rr=n+1;
		for (int j=0;j<=30;j++)
		 rr=min(rr,must[j][((s[i-1]>>j)&1)^1]);
		ans[i]=rr-1;
		for (int j=30;j>=0;j--)
		 if ((s[i-1]&(1<<j))^(s[i]&(1<<j))){
		 	must[j][(s[i-1]>>j)&1]=i;break;
		 }
	}
	init();rot[0]=build(1,n);
	for (int i=1;i<=n;i++)
	 rot[i]=add(rot[i-1],ans[i]);
	q=red();LL lastans=0;
	for (int i=1;i<=q;i++){
		int l=red(),r=red();
		l=(l+lastans*t)%n+1;r=(r+lastans*t)%n+1;
		if (l>r) swap(l,r);
		LL sum=Sum(rot[l-1],rot[r],l,r),cnt=Cnt(rot[l-1],rot[r],r+1,n);
		printf("%lld\n",lastans=sum+cnt*r-(LL)(l+r)*(r-l+1)/2+(r-l+1));
	}
	return 0;
}
```