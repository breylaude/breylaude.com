+++
title = "Solving Wearry's problem"
description = ""
tags = [
    "algorithms",
]
date = "2019-09-09"
categories = [
    "dynamic programming",
]
+++

O(n+m) Algorithm implementation (the previous board has been deleted for a better reading experience):

```c
const int p=1000000007;
int fpm(int a,int b){
    int c=1;
    for(;b;b>>=1,a=a*a%p)if(b&1)c=c*a%p;
    return c;
}
int f[1000005][2];
signed main(){
    freopen("game.in","r",stdin);
    freopen("game.out","w",stdout);
    int n,m;
    read(n,m);
    if(n>m)swap(n,m);
    if(n==1) return write(fpm(2,m),'\n');
    if(n==2)return write(4*fpm(3,m-1)%p,'\n');
    if(n==3 && m==3)return write("112\n");
    int ans=4*
        fpm(4,n-2)%p*fpm(3,m-n)%p*fpm(2,n-1)%p;
    
    if(n==3)
        ans=(ans+4*4*fpm(3,m-4)%p*fpm(2,n-1))%p;
    else
        ans=(ans+4*5*fpm(4,n-4)%p*fpm(3,m-n)%p*fpm(2,n-1))%p;
    f[3][0]=1;
    for(int i=4;i<=m;++i){
        f[i][0]=f[i-1][0];
        if(i<=n){
            f[i][1]=(f[i-1][1]*4+f[i-2][0]*20)%p;
        }
        else if(i==n+1){
            f[i][1]=(f[i-1][1]*3+f[i-2][0]*16)%p;
        }
        else 
            f[i][1]=(f[i-1][1]*3+f[i-2][0]*12)%p;
    }
    if(n==m)
        ans=(ans+4*(f[n][0]*3+f[n][1]*2+f[n-1][0]*12)%p*fpm(2,n-2))%p;
    else
    ans=(ans+2*
            ((f[n][0]*4+f[n][1]*3+f[n-1][0]*16)*fpm(3,m-n-1)%p*fpm(2,n-1)+
             (f[m][0]*3+f[m][1]*2+f[m-1][0]*9)*fpm(2,n-2))%p
            )%p;
    write(ans,'\n');
    return 0;
}
```

O(log n + log m): (notice fn,0 = [n ≥ 3] , and then you can speed up the power quickly)

```c
const int p=1000000007,i3=333333336,i2=500000004;
int fpm(int a,int b){
    int c=1;
    for(;b;b>>=1,a=a*a%p)if(b&1)c=c*a%p;
    return c;
}
signed main(){
    freopen("game.in","r",stdin);
    freopen("game.out","w",stdout);
    int n,m;
    read(n,m);
    if(n>m)swap(n,m);
    if(n==1) return write(fpm(2,m),'\n');
    if(n==2)return write(4*fpm(3,m-1)%p,'\n');
    if(n==3 && m==3)return write("112\n");
    int ans=4*
        fpm(4,n-2)%p*fpm(3,m-n)%p*fpm(2,n-1)%p;
    
    if(n==3)
        ans=(ans+4*4*fpm(3,m-4)%p*fpm(2,n-1))%p;
    else
        ans=(ans+4*5*fpm(4,n-4)%p*fpm(3,m-n)%p*fpm(2,n-1))%p;
    
    int fn=n==3?0:(fpm(4,n-4)-1)*i3%p*20%p;
    if(n==m)
        ans=(ans+4*(3+fn*2+(n>3)*12)%p*fpm(2,n-2))%p;
    else{
        int fm=(fn*3+(n>3)*16),w=fpm(3,m-n-1);
        fm=(fm*w+12*(w-1)%p*i2)%p;
        ans=(ans+2*
            ((4+fn*3+(n>3)*16)*fpm(3,m-n-1)%p*fpm(2,n-1)+
             (3+fm*2+(m>3)*9)*fpm(2,n-2))%p
            )%p;
    }
    write(ans,'\n');
    return 0;
}
```
