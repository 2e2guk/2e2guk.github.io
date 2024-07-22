---
title: "Project-Selection"
excerpt: "flow network modeling technique"

categories:
  - combinational-optimization
tags:
  - [tag1, tag2]

permalink: /combinational-optimization/Project-Selection/

toc: true
toc_sticky: true

date: 2024-07-21
last_modified_at: 2024-07-22
---

## 🦥 본문

### Project Selection 문제 소개

[Problem selection](https://codeforces.com/blog/entry/101354)

여기에 설명되어 있는 내용과, 

[Algorithm Design](https://github.com/aforarup/interview/blob/master/Data%20Structures%20and%20Algorithm/Algorithm%20Books/Algorithm%20Design%20by%20Jon%20Kleinberg,%20Eva%20Tardos.pdf)

Jon Klienberg 의 Algorithm Design 의 내용을 참조하였다. 

Project Selection 문제는 다음과 같이 정의된다. 

You are given a set of 𝑁 projects and 𝑀 machines. The 𝑖-th machine costs 𝑞𝑖. The 𝑖-th project yields 𝑝𝑖 revenue. Each project requires a set of machines. If multiple projects require the same machine, they can share the same one. Choose a set of machines to buy and projects to complete such that the sum of the revenues minus the sum of the costs is maximized.

N개의 프로젝트, M개의 기계가 있고, 각 프로젝트는 수익을 내며, 각 기계를 작동시키려면 비용이 발생한다. 각 프로젝트를 작동시키기 위한, 기계의 집합이 존재한다. 
만약, 여러 프로젝트가 동일 기계를 요구하면, 동일한 기계를 공유할 수 있다. 이때, **수익의 합 - 비용의 합** 의 값을 최대화하라. 

이 문제를 보고, 조합론적 최적화 분야의 최적화 문제라는 것은 어렵지 않게 알 수 있다. 한정된 자원 + 최적화 이기 때문. 

이 문제를 접근하는 방식은, 일단 모든 프로젝트에 대한 수익을 일괄 정산받은 후, 이후에 완료하지 못한 프로젝트에 대해 페이백하는 식이다. 당연히 기계 작동 비용은 빠지는 값이다. 

고로, 수익의 합을, **모든 프로젝트에 대한 수익의 합 - 절대 못하는 프로젝트의 수익의 합** 으로 쪼개는 것이고, 결국 최종 식은 다음과 같다. 

**모든 프로젝트의 수익의 합 - (절대 못하는 프로젝트 + 기계 작동비용)** 

절대 못하는 프로젝트란, 아무리 방법을 강구해도, 진행하면 무조건 손해일 수 밖에 없는 프로젝트이다. 

### Mincut 문제로 바꿔 풀기 

우리는 이 Project Selection 문제를, **최소 컷** 문제로 바꾸어 생각할 수 있는데, 위의 수식 **모든 프로젝트의 수익의 합 - (절대 못하는 프로젝트 + 기계 작동비용)** 

을 최대화하는 것이 목적이니, 결국 이는 **(절대 못하는 프로젝트 + 기계 작동비용)** 을 최소화하는 것과 동일한 문제이다. 

아이러니하게도 이는 **최대 유량 최소 컷 정리** 에 의해서, 최대 유량을 구함으로 해결할 수 있다. 

### Flow network modeling

Flow network 를 모델링해 보자. N개의 프로젝트와 M개의 기계가 있으므로, 

**소스 - N개의 프로젝트 - M개의 기계 - 싱크**

로 네트워크를 모델링하고, 

소스 - 프로젝트의 가중치는, 각 프로젝트를 완료하면 발생하는 수익 

프로젝트 - 기계의 가중치는, 무한대

기계 - 싱크의 가중치는, 각 기계를 작동시킬 때 필요한 비용

프로젝트 - 기계의 연결은, 프로젝트를 완료하는 데 필요한 기계로 연결하는 방식을 사용한다. 

이렇게 모델링한 후, 이 그래프의 **최소 컷** 을 구하면, 이것이 의미하는 것은, **(프로젝트 수익 + 기계 작동비용)** 의 최솟값 인데, 

이때 프로젝트 수익이 왜 들어가는가? 위에서 설명했듯이, 여기에 포함되는 프로젝트 수익은, 진행했을 때, **이익을 볼 수는 없는** 프로젝트 수익이다. 

왜? 플로우 네트워크의 구조를 생각해 보자. mincut에 포함되었다는 것은, 소스 - 프로젝트 간선이 포화되었다는 말인데, 이 말은

**이 프로젝트를 진행해서 발생하는 수익 <= 이 프로젝트를 하기 위해 작동시켜야 하는 기계의 비용의 총합**

이라는 의미이다. 이 작업이 의미하는 것은, 절대 못하는 프로젝트의 수익은 페이백하고, 반드시 필요한 기계만을 사는 것이다. 

그리고, 이 flow network의 최대 유량을 구하면, 이 그래프의 "최소" 컷을 구해 주기 때문에, 이는 최적화되어 있음이 보장되어 있다. 

고로, 이런 류의 문제에서는, 구성한 flow network 상에서,

**(소스-정점으로의 가중치 총합) - 최대 유량** 으로 해결할 수 있음을 알 수 있다. 

### 적용된 문제의 예시와 풀이

1. BOj 30947 Cakes

ICPC > Regionals > Europe > Central European Regional Contest > CERC 2023 C번

link : [boj30947](https://www.acmicpc.net/problem/30947)

solution : 

2. Codeforces 1082G

Educational Codeforces Round 55 [Rated for Div. 2] G번

link : [codeforces 1082G](https://codeforces.com/problemset/problem/1082/G)

solution : [1082G sol](https://2e2guk.github.io/combinational-optimization/codeforces-1082G/)





