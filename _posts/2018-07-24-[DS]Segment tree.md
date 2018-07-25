---
layout: post
category: Alogrithm
title: "[자료구조]세그먼트 트리"
---

**[백준님의 설명](https://www.acmicpc.net/blog/view/9)을 공부하며 정리한 글이므로 모든 출처는 백준님께 있습니다.**

<br>

세그먼트 트리는 **구간합을 구하는데 가장 많이 사용**되는 자료구조로, 시간복잡도를 상당히 줄일 수 있다는 장점이 있다. 예를 들어 다음과 같은 경우를 생각해보자.

* 구간 l~r이 주어졌을 때 A[l]+A[l+1]+...+A[r]을 구하는 문제
* A[k]를 업데이트 시키는 문제

연산횟수가 최대 M번이라고 하고 배열의 크기가 N이라고 하면 1번 문제는 $O(NM)$이 걸리고 2번은 $O(M)$이 걸리게 된다. N과 M이 작으면 상관없지만 커질 경우 시간복잡도도 굉장히 커지기 때문에 어떻게 하면 시간을 줄일 수 있을지를 생각해봐야 한다.

<br>

# 개념

세그먼트 트리는 이진트리를 이용해 구간을 쪼개는 자료구조이다. 이걸 이용하면 위에서 보았던 1번과 2번 연산 모두 $O(lgN)$ 만에 구할 수 있다. 그러면 어떤 방식으로 동작하는지 보아야 하므로 노드가 가지는 특성을 알아보자.

* 리프 노드 : 배열의 그 수 자체
* 다른 노드 : 왼쪽 자식 + 오른쪽 자식

기존 이진트리와 동일하게 왼쪽자식은 x2를 해주고 오른쪽 자식은 x2+1을 해주면 된다. 만약 N=10이라면 아래와 같은 그림이 세그먼트 트리의 노드가 어떤 역할을 하는지 보여준다.

<img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/blog/seg1.png">

리프노드를 제외하고는 모두 구간이 정해져있는 것을 확인할 수 있다. 루트인 0~9는 인덱스 0부터 9까지의 합을 의미하며 결국 모든 원소의 합을 말한다. 리프노드의 각 숫자는 인덱스 자체를 가리킨다. 따라서 그 값은 아래와 같다.

<img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/blog/seg2.png">

<br>

# 초기작업

먼저 N이 2가지 경우로 나뉘는데 2의 제곱꼴인 경우와 아닌 경우이다. 이것에 따라 높이가 달라지기 때문에 세그먼트 트리의 생성을 이해하기 위해선 높이에 대해 이해해야 한다.

* **N이 2의 제곱꼴인 경우**

이건 예를 들면 이해하기 쉽다. N=8인 경우를 생각해보면 루트는 0~7, 그 아래는 0~3과 4~7, 그 아래는 0~1, 2~3, 4~5, 6~7이고 마지막 리프는 0,1,2,3,4,5,6,7로 쪼개진다. 결국 리프노드까지 모든 노드가 존재하는 perfect binary tree임을 알 수 있다. 노드의 개수는 공비가 2인 등비수열로 증가하기 때문에 리프노드의 개수는 8이다. 이 때 높이는 3이므로 $lgN=h$가 성립함을 알 수 있다. 좀 더 엄밀하게 증명하자면 높이가 0일 때 노드의 개수가 1, 1일 때 2, 2일 때 4, h일 때 $2^h$이다. $2^h=N$이므로 $lgN=h$가 되는 것이다. 

* **N이 2의 제곱꼴이 아닌 경우**

리프노드가 N보다 작은 경우이지만 높이는 N인 경우와 같으므로 올림을 해주어야 한다. 따라서 $\lceil lgN \rceil = h$임을 알 수 있다.

1번째 경우는 리프노드가 N이라고 했을 때 2*N-1이 총 노드의 개수이지만 2번째 경우는 2\*(h+1)-1이 총 노드의 개수다. 2번째 경우로 1번째 경우를 해결할 수 있으므로 2\*(h+1)-1로 구하는 것이 맞다. (이건 등비수열의 합 공식으로 구할 수 있다.)

이제 벡터를 사용해서 트리를 생성하면 아래와 같은 방식으로 생성할 수 있게 된다.

```c++
int h = (int)ceil(log2(n));
int tree_size = (1 << (h+1));
vector<long long> tree(tree_size);
```

<br>

# 생성

부분합을 구할 배열을 a라고 하면 다음과 같이 생성할 수 있다.

```c++
typedef long long ll;
ll init(vector<ll> &a, vector<ll> &tree, int node, int start, int end)
{
    if(start==end) return tree[node]=a[start]; // 리프노드
    else return tree[node] = init(a, tree, node*2, start, (start+end)/2) 
        + init(a, tree, node*2+1, (start+end)/2+1, end);
}
```

전체적으로 세그먼트 트리를 만드는데 리프노드일 경우엔 배열값을 바로 넣어주고 아니면 왼쪽 자식과 오른쪽 자식을 합쳐주면 된다. node는 세그먼트 트리의 노드 번호를 의미한다. 여기서 노드번호와 해당하는 값은 다른 것이다.

<br>

# 부분합 찾기

노드가 담당하는 구간이 [start, end]이고 부분합을 구하는 구간이 [left, right]일 경우 4가지로 나뉜다.

* **[left, right]과 [start,end]가 겹치지 않는 경우**

이 경우는 left>end와 start<end로 나타낼 수 있으며 더 이상의 탐색은 무의미하므로 0을 리턴한다.

* **[left, right]이 [start, end]를 완전히 포함하는 경우**

이 경우는 left<=start와 right>=end로 나타낼 수 있고 더 이상의 탐색할 필요 없이 tree[node]를 리턴해주면 된다.

* **[start, end]가 [left, right]을 완전히 포함하는 경우**
* **[start, end]가 [left, right]과 겹친 경우**

위 2가지 경우는 안쪽을 더 깊이 탐색해봐야 하는 경우이므로 왼쪽 자식과 오른쪽 자식을 루트로하는 서브트리를 다시 한번 호출해주어야 한다.

```c++
ll sum(vector<ll> &tree, int node, int start, int end, int left, int right)
{
    if(left>end || right<start) return 0;
    if(left<=start && right>=end) return tree[node];
    return sum(tree, node*2, start, (start+end)/2, left, right) 
        + sum(tree, node*2+1, (start+end)/2+1, end, left, right);
}
```

<br>

# 업데이트 하기

배열 a의 어떤 값을 수정했다면 세그먼트 트리에 반영되어야 하므로 그 부분을 구현해주어야 한다. 인덱스 i번째 수를 val로 변경했다면 그 차이만큼 더해주면 된다. 차이는 diff = val - a[i]로 구할 수 있다. 인덱스를 넘겨줄 때 해당 인덱스가 start와 end에 포함될 수도 있고 아닐 수도 있기 때문에 포함되지 않는 경우는 굳이 계산할 필요 없다.

```c++
void update(vector<ll> &tree, int node, int start, int end, int index, ll diff)
{
    if(index>=start && index<=end) {
        tree[node] += diff;
        if(start != end){ // 리프노드가 아닐 경우
            update(tree, node*2, start, (start+end)/2, index, diff);
            update(tree, node*2+1, (start+end)/2+1, end, index, diff);
        }
    }
}
```

처음에 봤던 세그먼트 트리에서 5를 변경하는 과정은 0~9부터 시작해서 다음 그림과 같이 진행된다.

<img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/blog/seg8.png">



# 마무리

이렇게 세그먼트 트리는 구간합을 구할 때 아주 유용하지만, 합 뿐만 아니라 구간에 대한 문제가 주어졌는데 시간복잡도가 상당히 커질경우에도 사용할 수 있다. 꼭 기억하도록 하자!