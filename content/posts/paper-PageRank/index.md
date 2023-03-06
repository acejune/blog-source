---
draft: false

title: "[논문] The PageRank Citation Ranking: Bringing Order to the Web"
subtitle: ""
description: ""

date: 2023-03-01

categories: ["Paper"]
tags: ["Graph", "Computer Science"]
series: []

toc: 
    enable: true
    auto: true
---

<!--more-->

<br>

[The PageRank Citation Ranking Bringing Order to the Web (1998)](http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf) 논문을 정리해보겠습니다.

---

## 1 Introduction

### 1.1 배경

ㅤ구글 이전의 검색 엔진은, 사용자가 입력한 키워드에 의존하여 웹 페이지를 보여주었습니다. 이러한 검색 엔진에서는, 악성 웹 페이지들이 검색 엔진을 속이고, 사용자들을 자신의 페이지로 오게 만들기 쉬웠습니다.

ㅤ예를 들어, 운동화 판매자가 운동화 판매 페이지에 운동화와 관련 없는 내용(e.g., 영화, 음악, …)들을 잔뜩 적어놓을 수 있습니다. 그렇다면, 검색 엔진은 영화를 키워드로 검색해도, 이 운동화 판매 페이지를 보여주게 됩니다.

ㅤ키워드에 의존하는 검색 엔진은, 사용자의 검색 키워드와 상관없는 페이지를 보여주기 쉬웠습니다. 이러한 문제점을 해결하기 위해, PageRank 기술이 나왔습니다.

### 1.2 Web graph

ㅤPageRank는 웹 그래프(web graph)를 이용하는 기술입니다. 먼저 웹 그래프를 알아본 다음, PageRank를 살펴보겠습니다.

<br>

ㅤ**웹 그래프(web graph)** 는 World Wide Web을 그래프로 표현한 것입니다. 이 그래프는 directed graph이며, node는 웹 페이지이고, edge는 하이퍼링크입니다.

|  | Web graph |
|:---:|:---:|
| Node | 웹 페이지 |
| Edge | 하이퍼링크 |

<br>

ㅤ[그림 1]에서 `A 페이지`에서 `C 페이지`로 가는 `하이퍼링크`가 있다면, 이것은 `A-C directed edge`로 표현합니다.

{{< image src="./Fig_01_web_graph.png" caption="[그림 1] 웹 그래프 <br>출처: [The PageRank Citation Ranking Bringing Order to the Web (1998)](http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf)" width=300px >}}

### 1.3 PageRank란?

ㅤ우리가 PageRank를 통해 하고 싶은 것은, 네이버, 구글과 같은 검색 엔진에서, 어느 웹 페이지를 먼저 보여주어야 할지를 구하는 것입니다.

ㅤ우리가 구하고 싶은, `웹 페이지의 점수(중요성, 신뢰성)`를 **rank**라고 하겠습니다. 우리가 PageRank를 통해 하고 싶은 것을 다시 표현하면, 우리는 PageRank를 통해 모든 웹 페이지의 rank를 계산하고 싶습니다.

### 1.4 PageRank의 가정

ㅤPageRank가 각 웹페이지의 rank를 계산하기 위해서는, 어느 웹 페이지에 점수를 더 주어야 할지를 정해야 합니다. 이러한 규칙을 논문에서는 가정(underlying assumption)이라고 했습니다. 규칙은 아래와 같습니다.

- 참조가 많이 된 페이지는 참조가 적게 된 페이지보다 중요하다.
- 중요한 페이지가 참조한 페이지는 중요하다.

<br>

ㅤPageRank의 가정에 대한 예시를 [그림 2]를 통해 살펴보겠습니다.

{{< image src="https://upload.wikimedia.org/wikipedia/commons/thumb/f/fb/PageRanks-Example.svg/1920px-PageRanks-Example.svg.png" caption="[그림 2] PageRank 예시 <br>출처: [Wikipedia - PageRank](https://en.wikipedia.org/wiki/PageRank)" width=400px >}}

- `웹 페이지 B`
    - B는 다른 웹 페이지들로부터 참조가 많이 됐기 때문에, rank가 가장 높습니다.
- `웹 페이지 C`
    - C는 참조가 1번밖에 안 됐지만, rank가 가장 높은 B가 참조하고 있으므로, 2번째로 rank가 높습니다.
- `보라색 페이지들`
    - 참조가 없지만, PageRank의 어떤 기법 (teleport) 때문에, rank가 0이 아닙니다.
    - (이 부분은 글 아래에서 확인하겠습니다.)

---

## 2 PageRank를 바라보는 관점

ㅤ이번 장에서는 2가지 관점으로 PageRank를 이해해보겠습니다. 

1. 투표 관점
1. Random Walk 관점

### 2.1 `투표` 관점

ㅤPageRank는 투표를 통해 중요한 웹 페이지를 찾습니다. 웹 그래프에서, 웹 페이지(node)는 투표를 받는 대상이고, 하이퍼링크(edge)는 투표된 표를 의미합니다.

<br>

ㅤ[그림 1]을 보며, 투표에 대해 이해해보겠습니다. `A 페이지`는 `하이퍼링크`를 통해 `C 페이지`에 투표했다고 할 수 있습니다.

{{< image src="./Fig_01_web_graph.png" caption="[그림 1] 웹 그래프 <br>출처: [The PageRank Citation Ranking Bringing Order to the Web (1998)](http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf)" width=300px >}}

<br>

ㅤ다음과 같이 표현을 정리할 수 있습니다.

- **웹 페이지 A가 웹 페이지 B를 참조한다**
- == 웹 페이지 A에서 웹 페이지 B로 가는 하이퍼링크가 있다
- == **웹 페이지 B에는 웹 페이지 A로부터 오는 backlink (in-edge, in-link)가 있다**
- == 웹 페이지 A가 웹 페이지 B를 투표했다
- == **웹 페이지 A의 작성자가 웹 페이지 B를 신뢰할 수 있다고 판단했다**

<br>

ㅤ즉, 어떤 페이지에 backlink (in-edge, in-link)가 많을수록, 그 페이지를 신뢰할 수 있다고 말할 수 있습니다.

#### 2.1.1 투표 방법

ㅤ우리는, 모든 투표 후에, 신뢰가 가는 페이지가 다른 페이지들보다 투표 점수(rank)가 높기를 바랍니다. 어떻게 투표가 이루어지는지 살펴보겠습니다.

<br>

ㅤ**투표 방법** 은 아래와 같습니다. 

1. 모든 웹 페이지는 투표 점수를 갖고 있습니다. (보통, 맨 처음에는 모두 같은 투표 점수를 갖고 있습니다.)
1. 웹 페이지는 자신의 투표 점수를, 투표하려는 웹 페이지에 똑같이 나누어서 투표합니다.
1. 투표가 끝난 뒤, 각 웹 페이지는 자신이 받은 투표 점수를 모두 더하여, 자신의 새로운 투표 점수를 계산합니다.

ㅤ이러한 투표 방법을 도입한 이유는, 악성 웹 페이지들이 서로를 참조하여, backlink 수를 높게 만드는 조작을 하지 못하게 하기 위해서입니다. 단순하게 각 페이지의 backlink 수를 세지 않고, 이러한 투표 방식을 도입했습니다.

<br>

ㅤ**구체적인 예시** ([그림 3])를 통해 어떠한 방식으로 투표가 이루어지고, 계산되는지 알아보겠습니다.

1. 빨간색 node인 페이지 $j$는, 자신의 투표 점수(rank) $r_j$를 out-degree 수로 나눈 $\displaystyle \frac{r_j}{3}$를, out-edge가 가리키는 페이지에 보냅니다.
1. 빨간색 node인 페이지 $j$의 새로운 투표 점수(rank)는, in-edge를 통해 받은 투표 점수들의 합입니다.
    - $\displaystyle r_j = \frac{r_i}{3} + \frac{r_k}{4}$

{{< image src="./Fig_03_vote.png" caption="[그림 3] 웹 그래프에서의 투표 방법 <br>출처: [Stanford CS224W - 4. Link Analysis: PageRank](http://web.stanford.edu/class/cs224w/slides/04-pagerank.pdf)" width=300px >}}

<br>

ㅤ위의 예시를 이해했다면, 어떠한 웹 페이지($j$)의 rank($r_j$)는 다음과 같이 정의할 수 있습니다.

- $d_i$: 페이지 $i$의 out-degree
- $\displaystyle r_j = \sum_{i \rightarrow j}^{} \frac{r_i}{d_i}$

### 2.2 `Random Walk` 관점

ㅤrandom walk를 통해, 웹을 서핑하는 웹 서퍼가 있다고 가정해보겠습니다. 이 웹 서퍼는 현재 웹 페이지에 있는 여러 하이퍼링크 중 하나를 uniform한 확률로 선택해서 이동합니다. 이러한 웹 서퍼의 행동을 수학적으로 나타내보겠습니다.

<br>

ㅤ$p (t)$ 벡터가 있다고 가정해보겠습니다. $p (t)$의 $i$번째 값인 $p_i (t)$는, 웹 서퍼가 $t$ 번째 방문한 곳이 웹 페이지 $i$일 확률입니다. 그렇다면, $p (t)$는 웹 페이지에 대한 확률 분포가 됩니다.

ㅤ$p (t)$로부터 $p (t+1)$을 계산할 수도 있습니다.

$$
p_j (t+1) = \sum_{i \rightarrow j}^{} \bigg ( p_i(t) \times \frac{1}{d_i} \bigg ) = \sum_{i \rightarrow j}^{} \frac{p_i (t)}{d_i}
$$

<br>

ㅤ$p (t)$로부터 $p (t+1)$을 계산하고, $p (t+1)$로부터 $p (t+2)$를 계산하는 과정을 무한히 반복하고 나면 (즉 $t$가 무한히 커지면), 확률 분포 $p(t)$는 수렴하게 됩니다. 즉, $p(t) = p(t+1) = p$가 성립하게 합니다. 수렴한 확률 분포 $p$는 stationary distribution이라고 부릅니다.

ㅤ이렇게 되면, 식을 다음과 같이 바꿀 수 있습니다.

$$
p_j (t+1) = \sum_{i \rightarrow j}^{} \frac{p_i (t)}{d_i} \quad \red{\Rightarrow} \quad p_j = \sum_{i \rightarrow j}^{} \frac{p_i}{d_i}
$$

### 2.3 관점 정리

ㅤ앞에서 살펴본 투표 관점과 random walk 관점은 모두 같은 내용을 담고 있습니다. 논문에서 제시하는 rank의 수식과 투표 관점에서 정의한 rank와 random walk 관점에서의 stationary distribution은 모두 같습니다.

<br>

**< 논문에서 제시하는 rank의 수식 >**

- $\displaystyle R(u) = c \sum_{v \in B_u}^{} \frac{R(v)}{N_v}$

- $u$: 웹 페이지
- $B_u$: 페이지 $u$를 가리키는 페이지의 집합
- $N_u$: 페이지 $u$가 가리키는 페이지의 수
    - $N_u = \|F_u\|$
    - $F_u$: 페이지 $u$가 가리키는 페이지의 집합
- $c$: normalization factor
    - 모든 웹 페이지의 총 rank가 일정하게 유지되도록 하는 요소

<br>

**< 투표 관점에서 정의한 rank >**

- $d_i$: 페이지 $i$의 out-degree
- $\displaystyle r_j = \sum_{i \rightarrow j}^{} \frac{r_i}{d_i}$

<br>

**< random walk 관점에서의 stationary distribution >**

- $d_i$: 페이지 $i$의 out-degree
- $\displaystyle p_j = \sum_{i \rightarrow j}^{} \frac{p_i}{d_i}$

---

## 3 PageRank 문제점

ㅤ우리는 모든 웹 그래프에서 PageRank 알고리즘이 잘 작동되는지 궁금합니다. 크게 2가지 질문을 던질 수 있습니다.

1. rank 수렴이 보장되나요?
1. "합리적인" rank로 수렴하는 것이 보장되나요?

### 3.1 Spider trap

ㅤ**rank 수렴이 보장되나요?** 에 대한 질문부터 다뤄보겠습니다.

<br>

ㅤ모든 그래프가 rank의 수렴을 보장하지는 않습니다. spider trap이 있는 그래프는 수렴을 보장하지 않습니다. **spider trap**은 들어오는 edge는 있지만, 나가는 edge는 없는 **node 집합**을 말합니다.

<br>

ㅤspider trap으로 인해 문제가 발생하는 경우는 [그림 4]에 나와 있습니다.

ㅤ{B, C, D} node 집합으로 들어오는 edge는 있지만, 나가는 edge는 없습니다. 이러한 {B, C, D} node들을 spider trap이라고 합니다. A node의 rank는 0으로 수렴하고, {B, C, D} node들의 rank는 수렴하지 않는 문제가 발생합니다.

{{< image src="./Fig_04_spider_trap.png" caption="[그림 4] Spider trap 예시 <br>출처: [The PageRank Citation Ranking Bringing Order to the Web (1998)](http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf)" width=600px >}}

ㅤspider trap으로 인해 발생하는 문제의 해결 방법은 [3.3 해결 방법 - Teleport](#33-해결-방법---teleport)에서 알아보겠습니다.

### 3.2 Dead end

ㅤ다음으로, **"합리적인" rank로 수렴하는 것이 보장되나요?** 에 대한 질문을 다뤄보겠습니다.

<br>

ㅤ모든 그래프가 "합리적인" rank로 수렴하는 것을 보장하지는 않습니다. dead end가 있는 그래프는 "합리적인" rank로의 수렴을 보장하지 않습니다. **dead end**는 들어오는 edge는 있지만, 나가는 edge는 없는 **node**를 말합니다.

ㅤdead end 문제는 dangling link 문제라고도 합니다.

<br>

ㅤdead end로 인해 문제가 발생하는 경우는 [그림 5]에 나와 있습니다.

ㅤB node를 보면, 들어오는 edge는 있지만, 나가는 edge는 없습니다. 이러한 B node를 dead end라고 합니다. [그림 5]에 있는 그래프의 rank는 모두 0으로 수렴합니다. 이는 "합리적인" rank가 아닙니다.

{{< image src="./Fig_05_dead_end.png" caption="[그림 5] Dead end 예시 <br>출처: [Stanford CS224W - 4. Link Analysis: PageRank](http://web.stanford.edu/class/cs224w/slides/04-pagerank.pdf)" width=500px >}}

ㅤdead end로 인해 발생하는 문제의 해결 방법은 [3.3 해결 방법 - Teleport](#33-해결-방법---teleport)에서 알아보겠습니다.

<br>

{{< admonition note "논문에서는 dead end를 어떻게 해결했을까?" >}} 
- 논문에서는 dead end를 모두 제거한 후에, rank를 계산했습니다.
- 논문의 **2.7 Dangling Links**을 보면, 다음과 같이 나와 있습니다.
    > dangling links는 다른 페이지의 rank에 직접적인 영향을 미치지 않기 때문에, 모든 PageRank가 계산될 때까지 시스템에서 dangling links를 제거하기만 하면 됩니다.
    > <br><br>
    > 제거된 dangling links와 같은 페이지에 있는 다른 edge의 normalization은 약간 변하지만, 큰 영향은 없을 것입니다.
{{< /admonition >}}

### 3.3 해결 방법 - Teleport

ㅤ위에서 살펴본 2가지 문제(spider trap, dead end)는 모두 **teleport (순간 이동, 점프)** 로 해결할 수 있습니다.

<br>

ㅤrandom walk 관점에서 가정했던 웹 서퍼의 행동에 teleport를 적용해보겠습니다.

1. 현재 웹 페이지에 하이퍼링크가 없다면, 랜덤한 웹 페이지로 teleport 합니다.
2. 현재 웹 페이지에 하이퍼링크가 있다면, 
    - $\alpha$의 확률로, 하이퍼링크 중 하나를 uniform한 확률로 선택해 이동합니다.
    - $(1 - \alpha)$의 확률로, 랜덤한 웹 페이지로 teleport 합니다.
    - $\alpha$를 **damping factor**라고 부르며, $[0.8,\ 0.9]$ 정도를 사용합니다.
- teleport를 하는 경우엔, 전체 웹 페이지 중에 하나를 uniform한 확률로 선택합니다.

<br>

ㅤteleport에 의해서, spider trap이나 dead end에 갇히는 일이 없어집니다.

<br>

ㅤteleport를 도입함으로써, rank를 계산하는 식은 아래와 같이 바뀝니다.

<br>

**< 기존의 PageRank 식 >**

$$\displaystyle r_j = \sum_{i \rightarrow j}^{} \frac{r_i}{d_i}$$

<br>

**< 새로운 PageRank 식 >**

$$\displaystyle r_j = \sum_{i \rightarrow j}^{} \bigg( \alpha \frac{r_i}{d_i} \bigg) + (1-\alpha) \frac{1}{N}$$

- $N$은 총 웹 페이지(node)의 수를 의미합니다.
- 식 $\displaystyle \sum_{i \rightarrow j}^{} \bigg( \alpha \frac{r_i}{d_i} \bigg)$ 는 하이퍼링크를 따라 node $j$에 도착할 확률을 의미합니다.
- 식 $\displaystyle (1-\alpha) \frac{1}{N}$ 은 teleport를 통해 node $j$에 도착할 확률을 의미합니다.

---

## 4 PageRank 계산

ㅤ이번 장에서는 PageRank를 계산하는 수학적인 이야기를 살짝 하도록 하겠습니다. [4장](#4-pagerank-계산)에는 spider trap과 dead end 문제가 없다고 가정하고, 이야기하도록 하겠습니다. [5장](#5-pagerank-계산-예시)에서는 spider trap과 dead end 문제가 있는 그래프의 구체적인 계산 과정을 살펴보겠습니다.

### 4.1 Matrix formulation

ㅤ기존의 PageRank 식은 아래와 같이 matrix 형태로 나타낼 수 있습니다.

$$r = M \cdot r$$

- $r$: rank 벡터
- $M$: column stochastic adjacency matrix
    - $\displaystyle M_{u, v} = \begin{cases} \frac{1}{N_u}, & \text{if there is an edge from } v \text{ to } u \\\\\\ 0, & \text{otherwise} \end{cases}$
    - $N_u$: node $u$가 가리키는 node의 수

{{< admonition note "Column stochastic matrix" >}} 
- column stochastic matrix는
    - matrix의 모든 값이 음수가 아닙니다.
    - 각 column의 합이 1입니다.
{{< /admonition >}}

<br> 

ㅤ예시를 통해서, 어떻게 PageRank 식을 matrix 형태로 나타낼 수 있는지 보겠습니다.

{{< image src="./Fig_06.png" caption="[그림 6] 그래프 예시 <br>출처: [Stanford CS224W - 4. Link Analysis: PageRank](http://web.stanford.edu/class/cs224w/slides/04-pagerank.pdf)" width=250px >}}

1. 각 페이지가 얻는 rank를 연립 방정식으로 나타냅니다.
    - $\displaystyle r_a = \frac{1}{2} r_a + \frac{1}{2} r_b$
    - $\displaystyle r_b = \frac{1}{2} r_a + r_c$
    - $\displaystyle r_c = \frac{1}{2} r_b$
1. (1)의 연립 방정식을 matrix 형태로 변환합니다.
$$
    \begin{bmatrix}
        r_a \\\\\\
        r_b \\\\\\
        r_c
        \end{bmatrix}
        = 
        \begin{bmatrix}
        \frac{1}{2} & \frac{1}{2} & 0 \\\\\\
        \frac{1}{2} & 0 & 1 \\\\\\
        0 & \frac{1}{2} & 0
        \end{bmatrix}
        \begin{bmatrix}
        r_a \\\\\\
        r_b \\\\\\
        r_c
    \end{bmatrix}
$$
1. (2)의 matrix 식은 간단하게 $r = M \cdot r$로 나타낼 수 있음. 
    - 이때, $M$은 column stochastic matrix입니다.
$$
    r = 
    \begin{bmatrix}
    r_a \\\\\\
    r_b \\\\\\ 
    r_c
    \end{bmatrix}
    , \quad
    M
    = 
    \begin{bmatrix}
    \frac{1}{2} & \frac{1}{2} & 0 \\\\\\
    \frac{1}{2} & 0 & 1 \\\\\\
    0 & \frac{1}{2} & 0
    \end{bmatrix}
$$

### 4.2 Power iteration

ㅤ[4.1](#41-matrix-formulation)에서 본 $r = M \cdot r$ 식을 다시 쓰면, $\ M \cdot r = 1 \cdot r$ 입니다. $M \cdot r = 1 \cdot r$ 식은, $M$의 `eigenvalue`가 1이고, `eigenvector`가 $r$이라고 할 수 있습니다. eigenvector $r$을 구하기 위해, **power iteration**을 사용합니다.

<br>

**< power iteration 3단계 >**

1. 각 웹 페이지 $i$의 rank $r_{i}^{(0)}$를 동일하게 $ \displaystyle r_{i}^{(0)} = \frac{1}{\text{웹 페이지의 수}}$ 로 초기화합니다.
1. 아래 식을 이용하여, 각 웹 페이지의 rank를 갱신합니다.
    
    $$r_{j}^{(t+1)} = \sum_{i \rightarrow j}^{} \frac{r_i^{(t)}}{d_i}$$
    
1. rank가 수렴하면 ($r^{(t)} \approx r^{(t+1)}$) 종료하고, 아니면 (2)로 돌아갑니다.

<br>

ㅤrank가 수렴하면, $r^{(t)} \approx r^{(t+1)}$을 rank 벡터로 사용합니다.

---

## 5 PageRank 계산 예시

ㅤ[그림 7]과 같은 웹 그래프가 있을 때, 그래프에 PageRank 알고리즘을 적용해보겠습니다.

{{< image src="./Fig_07_rank_calc.png" caption="[그림 7] Spider trap과 Dead end가 모두 있는 웹 그래프" width=400px >}}

### 5.1 Adjacency matrix

ㅤ먼저, 웹 그래프의 adjacency matrix $A$ 를 구합니다. $A$는 row node에서 column node로 가는 edge가 있으면 1, 없으면 0입니다.

<br>

ㅤ[그림 7]의 adjacency matrix $A$는 아래와 같습니다.
    
$$
    A = 
    \begin{bmatrix}
    0 & 1 & 0 & 1 \\\\\\
    0 & 0 & 1 & 0 \\\\\\
    0 & 1 & 0 & 0 \\\\\\
    0 & 0 & 0 & 0
    \end{bmatrix}
$$
    
### 5.2 Adjacency matrix transpose

ㅤ다음으로, $A$를 transpose 한 matrix $A^T$ 구합니다. $A^T$는 column node에서 row node로 가는 edge가 있으면 1, 없으면 0입니다.

<br>

ㅤ[그림 7]의 adjacency matrix $A$를 transpose 한 $A^T$는 아래와 같습니다.
    
$$
    A^T = 
    \begin{bmatrix}
    0 & 0 & 0 & \color{red}{0} \\\\\\
    1 & 0 & 1 & \color{red}{0} \\\\\\
    0 & 1 & 0 & \color{red}{0} \\\\\\
    1 & 0 & 0 & \color{red}{0}
    \end{bmatrix}
$$

### 5.3 Hyperlink matrix

ㅤ다음으로, $A^T$를 hyperlink matrix $H$로 변환합니다. hyperlink matrix는 $A^T$의 값들을, column 별로, column의 합으로 나눈 matrix입니다.

<br>

ㅤ[그림 7]의 hyperlink matrix인 $H$는 아래와 같습니다.

$$
    H = 
    \begin{bmatrix}
    0 & 0 & 0 & \color{red}{0} \\\\\\
    \frac{1}{2} & 0 & 1 & \color{red}{0} \\\\\\
    0 & 1 & 0 & \color{red}{0} \\\\\\
    \frac{1}{2} & 0 & 0 & \color{red}{0}
    \end{bmatrix}
$$

### 5.4 Column stochastic matrix

ㅤPageRank의 수렴을 보장하기 위해 column stochastic matrix가 필요합니다. 따라서, $H$를 column stochastic matrix인 $M$으로 만들어줍니다. column stochastic matrix를 만들어줌으로, **dead end 문제가 해결**됩니다.

{{< admonition note "Column stochastic matrix" >}} 
- column stochastic matrix는
    - matrix의 모든 값이 음수가 아닙니다.
    - 각 column의 합이 1입니다.
{{< /admonition >}}

<br>

ㅤ[그림 7]의 column stochastic matrix인 $M$은 아래와 같습니다.
    
$$
    M = 
    \begin{bmatrix}
    0 & 0 & 0 & \color{red}{\frac{1}{4}} \\\\\\
    \frac{1}{2} & 0 & 1 & \color{red}{\frac{1}{4}} \\\\\\
    0 & 1 & 0 & \color{red}{\frac{1}{4}} \\\\\\
    \frac{1}{2} & 0 & 0 & \color{red}{\frac{1}{4}}
    \end{bmatrix}
$$

### 5.5 Google matrix

ㅤ$M$을 이용하여, PageRank를 구하는 Google matrix $G$ 구합니다. **spider trap 문제는 teleport를 도입함으로써 해결**됩니다.

ㅤ$\alpha$는 보통  $[0.8,\ 0.9]$ 정도를 사용합니다.

<br>

ㅤ[그림 7]의 Google matrix $G$는 아래와 같습니다.
    
$$
    G = \alpha
    \begin{bmatrix}
    0 & 0 & 0 & {\frac{1}{4}} \\\\\\
    \frac{1}{2} & 0 & 1 & {\frac{1}{4}} \\\\\\
    0 & 1 & 0 & {\frac{1}{4}} \\\\\\
    \frac{1}{2} & 0 & 0 & {\frac{1}{4}}
    \end{bmatrix}
    +
    (1-\alpha)
    \begin{bmatrix}
    \frac{1}{4} & \frac{1}{4} & \frac{1}{4} & \frac{1}{4} \\\\\\
    \frac{1}{4} & \frac{1}{4} & \frac{1}{4} & \frac{1}{4} \\\\\\
    \frac{1}{4} & \frac{1}{4} & \frac{1}{4} & \frac{1}{4} \\\\\\
    \frac{1}{4} & \frac{1}{4} & \frac{1}{4} & \frac{1}{4}
    \end{bmatrix}
$$

<br>

ㅤ$\alpha = 0.8$로 설정하고, [그림 7]의 Google matrix $G$를 계산하면 아래와 같습니다.

$$
    G = 
    \begin{bmatrix}
    0.05 & 0.05 & 0.05 & 0.25 \\\\\\
    0.45 & 0.05 & 0.85 & 0.25 \\\\\\
    0.05 & 0.85 & 0.05 & 0.25 \\\\\\
    0.45 & 0.05 & 0.05 & 0.25
    \end{bmatrix}
$$

### 5.6 Rrank 벡터 

ㅤpower iteration을 이용하여, $r = G \cdot r$ 식을 풀어, 최종 rank 벡터 $r$을 구할 수 있습니다.

<br>

ㅤ[그림 7]의 최종 rank 벡터 $r$은 아래와 같습니다.
    
$$
    r 
    \approx 
    \begin{bmatrix}
    0.07 \\\\\\
    0.42 \\\\\\
    0.41 \\\\\\
    0.10
    \end{bmatrix}
$$

{{< image src="./Fig_07_rank_calc.png" caption="[그림 7] Spider trap과 Dead end가 모두 있는 웹 그래프" width=350px >}}

---

## 6 논문의 결론

ㅤ논문에서는 PageRank가 매우 큰 데이터에서도 매우 잘 확장될 것임을 시사했습니다. [그림 8]에서 이 내용을 확인할 수 있습니다.

{{< image src="./Fig_08_convergence.png" caption="[그림 8] Rates of convergence<br>출처: [The PageRank Citation Ranking Bringing Order to the Web (1998)](http://ilpubs.stanford.edu:8090/422/1/1999-66.pdf)" width=500px >}}

- 3억 2,200만 개의 edge → 52번의 iteration을 통해 수렴
- 1억 6,100만 개의 edge → 45번의 iteration을 통해 수렴

---

## 7 질문

### 7.1 구글은 지금도 PageRank를 사용할까?

ㅤPageRank 논문은 1998년에 나왔지만, 구글은 현재도 검색 엔진의 품질 향상을 위해 PageRank를 사용하는 것으로 알려져 있습니다.

{{< image src="./Fig_09_tweet.png" caption="[그림 9]<br>출처: 구글의 Search Advocate, John의 트위터" width=450px >}}

--- 

## 8 Reference

- [Stanford University - CS224W 4. Link Analysis: PageRank](http://web.stanford.edu/class/cs224w/slides/04-pagerank.pdf)
- [boostcourse - 그래프와 추천 시스템](https://www.boostcourse.org/ai211)
- [고려대학교 산업경영공학부 DSBA 연구실 - [Paper Review] The PageRank Citation Ranking: Bringing Order to the Web](https://youtu.be/2CWnZfBSj0Q)