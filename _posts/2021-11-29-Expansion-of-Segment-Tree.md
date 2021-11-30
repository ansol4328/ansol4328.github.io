---
title: 세그먼트 트리의 확장
date: 2021-11-29
description:
categories:
 - Algorithm
 - Data Structure
tags:
 - Algorithm
 - Data Structure
 - Segment Tree
 - Divide and Conquer
---
# 개요
이 포스팅에서는 기초적인 세그먼트 트리와 응용을 다루도록 한다.

# 분할 정복
세그먼트 트리를 알기 위해서는 기본적으로 **분할 정복**을 알아야한다. 분할 정복은 **2가지의 단계**로 이루어지는데, **문제를 더 작은 문제로 나누는 Divide 단계**와 **작은 문제의 결과값을 합쳐 현재 문제를 해결하는 Conquer 단계**가 있다. 이때 더 작은 문제란, 구해야할 대상은 같고 따져야할 데이터의 범위만 줄어든 문제를 의미한다. 예시 문제 2개를 살펴보도록 하자.   

## 예제 1
> $N$ 개의 정수 $A_1, A_2, \cdots{}, A_N$ 이 주어진다. 이때 최댓값을 구하여라.   

해당 문제는 단순히 $O(N)$ 에 모든 원소의 값을 확인함으로써 해결이 충분히 가능하지만, 동일한 시간 복잡도로 분할 정복을 이용하여 해결해보도록 하자. **Divide 단계**를 먼저 생각해보면, 현재 $[L,R]$ 범위의 최댓값을 구하고자 한다면, $m=\lfloor { {L+R} \over 2 } \rfloor$ 를 기준으로 2개의 더 작은 구간으로 나눌 수 있다. **즉, 구간 $[L,R]$ 이 더 작은 구간 $[L,m]$ 과 $[m+1,R]$ 로 나누어 진다.**   

이제 작은 문제의 결과값을 합쳐서 더 큰 문제의 답을 구하는 **Conquer 단계**를 생각해보자. 우리는 **현재 $[L,R]$ 범위의 최댓값을 구하고자 한다.** 또한 이미 이보다 작은 문제에 대한 정답은 이미 구해져 있는 상태이다. 즉, **더 작은 구간 $[L,m]$ 과 $[m+1,R]$ 에서의 최댓값을 알고 있다.** 자명히 $[L,R]$ 범위의 최댓값은 앞서 divide 한 더 작은 2개의 구간의 최댓값 중 큰 값이므로, **둘 중에 더 큰 값을 고르는 과정이 Conquer 단계**가 된다.   

시간 복잡도는 $T(N)=2T({N\over2})+O(1)$ 이므로, 이 식을 풀어보면 $T(N)=O(N)$ 이 되게 된다. 다음으로는 조금 더 어려운 문제를 풀어보도록 하자.   

## 예제 2
> a, b, c 로만 이루어진 문자열 $S$ 가 주어진다. $S$ 의 문자들을 a, b, c 중에 하나로 바꿀 수 있는 연산을 임의의 횟수만큼 할 수 있다고 하자. 이때 $S$ 의 sub-sequence 중 abc 가 나타나지 않도록 하기 위한 최소한의 연산 횟수를 구하여라.   

풀어야 할 문제 자체는 상당히 명확하다. **주어진 범위에서 sub-sequence로 abc 가 나타나지 않는 최소 연산 횟수**를 구하면 된다. 그렇다면 이 값을 구하기 위해서는 어떤 값들을 알아야 할까? 생각을 해보면 아래 3가지 경우 중에 1가지를 만족하면 sub-sequence 로 abc 가 나타나지 않음을 알 수 있다.

> 구간 $[L,R]$ 을 더 작은 구간 $[L,m]$ 과 $[m+1,R]$ 로 나눈다고 생각하자.   
> 1. $[L,m]$ 에 sub-sequence로 abc가 나타나지 않고, $[m+1,R]$ 에 sub-sequence로 c가 나타나지 않음.   
> 2. $[L,m]$ 에 sub-sequence로 ab가 나타나지 않고, $[m+1,R]$ 에 sub-sequence로 bc가 나타나지 않음.   
> 3. $[L,m]$ 에 sub-sequence로 a가 나타나지 않고, $[m+1,R]$ 에 sub-sequence로 abc가 나타나지 않음.   

따라서 이 3가지 경우 중 최솟값을 고르면 되고, 각 경우를 구하기 위해 필요한 값들을 따져보면 다음과 같다.   

> $[L,R]$ 에서 sub-sequence로 a, b, c, ab, bc, abc 가 나타나지 않도록 하는 최소 연산횟수   

따라서 더 작은 문제에 대하여 위의 값들을 계산을 해 놓으면, 더 큰 문제에 대하여 계산이 가능하고 이 단계가 자연스레 Conquer 단계일 것이다. 코드는 아래와 같다. **시간 복잡도는 첫번째 예제와 같은 구조이므로, 자명히 $O(N)$** 이 된다.

```cpp
#include<bits/stdc++.h>

using namespace std;

struct Val{
    int a, b, c, ab, bc, abc;
    Val(){
        tie(a,b,c,ab,bc,abc)=make_tuple(0,0,0,0,0,0);
    }
    Val(int _a, int _b, int _c, int _ab, int _bc, int _abc){
        tie(a,b,c,ab,bc,abc)=make_tuple(_a,_b,_c,_ab,_bc,_abc);
    }
};

int n;
char s[100005];

Val DnC(int l, int r)
{
    Val ret;
    if(l==r){
        if(s[l]=='a') ret.a++;
        if(s[l]=='b') ret.b++;
        if(s[l]=='c') ret.c++;
        return ret;
    }
    int mid=(l+r)>>1;
    Val L=DnC(l,mid);
    Val R=DnC(mid+1,r);
    ret.a=L.a+R.a; ret.b=L.b+R.b; ret.c=L.c+R.c;
    ret.ab=min({L.ab+R.b,L.a+R.b,L.a+R.ab});
    ret.bc=min({L.bc+R.c,L.b+R.c,L.b+R.bc});
    ret.abc=min({L.abc+R.c,L.ab+R.bc,L.a+R.abc});
    return ret;
}

int main()
{
    scanf("%d",&n);
    scanf("%s",&s[1]);
    Val ans=DnC(1,n);
    printf("%d\n",ans.abc);
    return 0;
}
```

# 기초 세그먼트 트리
흔히 기초 세그먼트 트리란, 다음의 연산을 $O(\log{N})$ 에 가능하게 해주는 자료구조를 말한다.   

> 1. $i$ 번째 원소를 $x$ 로 바꾼다.   
> 2. $[L,R]$ 값들의 최솟값/최댓값/합/곱을 구한다.   

생각을 해보면 위의 예시들에서 구하고자 하는 값은 위에서 다룬 **첫번째 예제와 유사한 방법으로 분할 정복을 통하여 구할 수 있음**을 알 수 있다. 그렇다면 **매번 배열의 값을 바꾸고, 분할 정복을 하면 될까? 과연 이것이 최선의 방법일까?** 라는 의문에서 세그먼트 트리는 나오게 된다. 원소의 값이 바뀔 때, **분할 정복을 한 결과물이 바뀌는 경우는 $L\leq{}i\leq{}R$ 인 경우** 뿐이다. 만약 구간 $[L,R]$ 에 $i$ 가 포함 되지 않으면, 자명히 이전에 계산된 값과 똑같은 값이 나올 것이다.   

이미 계산한 값을 다시 참조할 때 빠르게 구하는 방법은 DP 에서의 발상을 그대로 가져오면 된다. DP가 최소한의 변수를 기반으로 한 완전 탐색을 메모이제이션하여 시간 복잡도를 낮추듯이, **세그먼트 트리는 분할 정복을 메모이제이션** 하면 된다. 따라서 **$L\leq{}i\leq{}R$ 인 경우에 대하여만 새롭게 계산**을 하고, **나머지 범위는 이미 계산된 값을 참조만 하여 전체 답을 빠르게 갱신**할 수 있게 된다. 자세한 구현은 다양한 블로그에서 잘 설명을 해 놓았으니 생략하도록 하겠다.   

# 세그먼트 트리의 확장
세그먼트 트리의 확장이란 그다지 특별한 내용이 아니다. 하지만 흔히 사람들이 세그먼트 트리를 공부할 때 해당 자료구조의 본질을 까먹고, 기초 세그먼트 트리의 내용만 기억하게 되기 때문에 발상을 하지 못하는 경우가 많다.(본인의 경우도 그랬다. 단순히 합, 곱, 최대, 최소를 로그 시간에 구해야 할 때 세그를 사용하는 사고 구조였다.) **세그먼트 트리의 본질이란 무엇인가? 앞서 언급한 바와 같이 분할 정복을 메모이제이션한 자료구조**이다. 즉, **분할 정복으로 해결이 가능한 문제는 쿼리 문제로 변환이 가능**하며, 그러한 문제는 **세그먼트 트리를 이용**하면 쉽게 풀린다.   

하지만 모든 분할 정복 문제가 쿼리 문제로 출제가 가능한 것은 아니며, $T(N)=2T({N\over2})+O(1)$ 와 같이 Conquer 단계가 상수 시간 내지는 충분히 작은 시간에 가능한 경우(ex. $O(\log{N})$) 에 한하여 가능할 것이다. 왜냐하면 세그먼트 트리로 본 문제를 푸는 경우에는 **업데이트를 하는데 $O(\log{N}\times{C(N)})$ 만큼의 시간이 걸리기 때문**이다. ( $C(N)=$ Conquer 단계에서 필요한 시간)   

마찬가지로 예제를 살펴보도록 하자.   
[Codeforces 1609E William The Oblivious](https://codeforces.com/contest/1609/problem/E)   

해당 문제를 살펴보면 위에서 다룬 예제2에 업데이트 쿼리가 추가된 문제임을 알 수 있다. 따라서 위의 분할정복 코드에 메모이제이션을 추가하여 세그먼트 트리를 구성하면 문제를 쉽게 해결할 수 있게된다. 아래는 본 문제를 해결하는 코드이다.   

```cpp
#include<bits/stdc++.h>
#define fi first
#define se second
#define pb push_back

using namespace std;
typedef long long ll;
typedef pair<ll,ll> P;

struct Node{
    int a, b, c, ab, bc, abc;
    Node(){
        tie(a,b,c,ab,bc,abc)=make_tuple(0,0,0,0,0,0);
    }
    Node(int _a, int _b, int _c, int _ab, int _bc, int _abc){
        tie(a,b,c,ab,bc,abc)=make_tuple(_a,_b,_c,_ab,_bc,_abc);
    }
};

struct SegTree{
    vector<Node> tree;
    int base;
    SegTree(int a){
        base=1;
        while(base<a) base<<=1;
        tree.resize(base*2);
        base--;
    }
    Node combine(Node &l, Node &r){
        Node ret(l.a+r.a,l.b+r.b,l.c+r.c,0,0,0);
        ret.ab=min({l.a+r.b,l.ab+r.b,l.a+r.ab});
        ret.bc=min({l.b+r.c,l.bc+r.c,l.b+r.bc});
        ret.abc=min({l.abc+r.c,l.a+r.abc,l.ab+r.bc});
        return ret;
    }
    void updt(int idx, char x){
        Node tmp;
        if(x=='a') tmp.a++;
        if(x=='b') tmp.b++;
        if(x=='c') tmp.c++;
        tree[idx+=base]=tmp;
        for(idx>>=1 ; idx ; idx>>=1){
            tree[idx]=combine(tree[idx*2],tree[idx*2+1]);
        }
    }
    int qry(){
        return tree[1].abc;
    }
};

int n, q;
char s[100005];

int main()
{
    scanf("%d %d",&n,&q);
    scanf("%s",&s[1]);
    SegTree T(n);
    for(int i=1 ; i<=n ; i++) T.updt(i,s[i]);
    for( ; q ; q--){
        int a; char b;
        scanf("%d %c",&a,&b);
        T.updt(a,b);
        printf("%d\n",T.qry());
    }
    return 0;
}
```

기본적인 세그먼트 트리를 구현할 줄 아는 사람이라면 세그먼트 트리의 확장이 무슨 의미인지 눈치를 챘을 것이다. 세그먼트 트리의 확장이란, 단순히 기초 세그먼트 트리에서 리턴 값으로 하나의 정수를 보냈다면, **확장된 버전은 더 다양한 정보를 노드에 담아서 보내도 된다**는 것이다. 요즘에는 웰노운으로 알려진 **금광 세그 역시 확장된 세그먼트 트리의 예시**라고 볼 수 있을 것이다. 아래 예시 문제를 첨부하니 기회가 될 때 풀어본다면 해당 글이 무슨 말을 하고 싶은지 더 잘 이해할 수 있을 것이다.   

[BOJ 10167 금광](https://www.acmicpc.net/problem/10167)   
[BOJ 13203 읽어내기](https://www.acmicpc.net/problem/13203)   

# 결론
세그먼트 트리의 본질은 분할 정복이며, 따라서 확장된 세그를 구현하는 것의 핵심은 해당 문제를 분할정복으로 풀 줄 아는지를 묻는 것이다. 물론 기본적인 세그먼트 트리를 활용하는 것도 할 줄 알아야 한다. (ex. 합세그를 이용하여 k번째 원소 찾기) **따라서 세그를 이용해서 문제를 풀겠다는 접근과, 분할 정복 풀이가 가능하니 세그를 이용한다는 2가지 방향의 접근법을 모두 기억하면 좋을 것이다.**
