# * Index
#### 0. 문제의 정의(problem definition) 및 관련 hr이론
#### 1. data 분포 살펴보기
#### 2. our best and most experienced employees에 대한 정의 - GE 9 block
#### 3. Left reason(3_cluster) - PCA분석 및 graph
#### 4. Predict which valuable employees will leave next - cosine similarity
#### 5. 결론 및 추가 논의사항

<br />
<br />
<br />

# 0 . 문제의 정의(problem definition) 및 관련 hr이론 

<br />

### - 문제의 정의

<br />

Kaggle에 있는 문제 소개 중

"Why are our best and most experienced employees leaving prematurely?" 

"Have fun with this database and try to predict which valuable employees will leave next." 
<br />

이 문제의 포인트를 요약해보면,

- 첫번째, **our best and most experienced employees**에 대한 **정의**가 선제되어야 한다.

- 두번째, predict가 핵심 키워드가 아니라 valuable employees will **leave next**이 핵심 키워드이다.

predict를 핵심 키워드로 생각해서 **classification**으로 접근하게 되면, accuracy가 높더라도 **현재인원**에 대한 분류이다. 

또한 **새로운data**가 들어왔을 때, 어떤 category에 속하는 가이다.

<br />

하지만 predict which valuable employees will leave next에서 precidt는 hr에서 단순히 **예측**이 아닌, 미리 알아서 이직을 **예방**등과 같은 조치를 취하겠다는 의미이다.

따라서 이직한 인원들의 특징을 파악해서 이와 **현시점에서 이와 유사한 사람**들이 누가있는지 파악해야 한다.

<br />
<br />

### - 관련 hr이론
- **인적자원계획**은 현재 및 장래의 각 시점에서 기업이 필요로 하는 **인원의 수를 예측**하고 이에 대한 사내의 **인력공급을 계획**해서 인력의 수급을 조정하는 계획활동이다.

<br />

- **적정이직률**은 기업이 부담해야 하는 이직비용과 인력보유비용의 합이 최소가 되는, 즉 **이직비용이 최저**가 되는 곳이 존재한다

![1.png](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/1.png?raw=true)

<br />

- **GE-9block**은 GE의 CEO였던 Jack Welch가 **발전가능성(동기부여-x축) 결과(성과-y축)**를 사용하여 9가지 block을 구성하고 **상위 10%**는 핵심인재로서 지속적인 **육성, 하위 10%는 방출**하는 인력관리 방법이다.

![2](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/2.png?raw=true)

- 위 그림에서 9,8,6 등과 같이 green 영역에 속한 인재는 **육성**을 하였으며, 7,5,3과 같은 인재는 **유지**를, 4,2과 같은 인재는 **주의**를, 1번과 같은 인재는 **방출**을 하였다.

- 또한 인력 matrix간 인원에서 단기간에는 **한 칸씩 이동**이 가능하다고 가정하여 trainging & develpoment를 시행한다.
    (예를 들어서 9번으로는 8번 6번 영역의 인재들이 이동가능하다고 가정)

<br />
<br />

### - 문제의 정의 및 관련 hr이론을 고려한 앞으로의 전개과정

1. 인적자원계획을 설정하기 위해서 현재 **기업 수요인원과 공급인원**을 살펴본다.
2. 적정이직률과 GE-9block에 의하면 모든 인원이 나가지 않는 것이 좋은 것이 아니다. 때로는 조직에 해가 되는 인원은 조직의 순환을 위해 도움이 된다. 이러한 것을 살펴보기 위해 **GE-9block**으로 인원들을 **segmentaion**해보도록 하겠다.
3. GE-9block으로 나눈 후에는 **이직이 많이 발생하는 집단의 특징**을 살펴본다.
4. 이 특징과 **유사한 현재 인원**을 찾아본다.

<br />
<br />
<br />

# 1. Data의 분포

<br />

### 데이터 설명
이 데이터는 총 14999의 행과 10개의 열을 가지고 있다. 열의 의미는 아래와 같다.

- Satisfaction Level : 만족수준을 의미하며, 0-1사이의 실수값이다.
- Last evaluation : 최신 평가를 의미하며,0-1사이의 실수값이다.
- Number of projects : 조사를 했을 때, 맡고 있던 프로젝트의 개수를 의미하며 정수값이다.
- Average monthly hours : 월별 평균 근로시간을 의미하며 정수값이다.
- Time spent at the company : 근속년수를 의미하며 정수값이다.
- Whether they have had a work accident : 근무 중 사고를 입었는지 여부(산재여부)(카테고리)
- Whether they have had a promotion in the last 5 years : 최근 5년 간 승진한 적이 있는 지 여부(카테고리)
- Departments (column sales) : 속하는 부서(카테고리)
- Salary : 연봉을 의미하여 high,low, medium으로 카테고리 값이다.
- Whether the employee has left : 퇴사여부를 의미하며 0,1로 이루어진 카테고리 값이다.

<br />

### 데이터의 분포의 특징 살펴보기

![3](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/3.png?raw=true)

- 데이터 분포를 살펴보면 number_project의 수는 4개가 최고점을 이루고 있으며,
- 회사 근속년수는 3년이 최고점을 이루고 있으며, 1년차는 없는 것으로 봐서 저번년도는 **채용이 없던 것**으로 확인할 수 있다.
- 이직에 대한 인원이 **많은 비중**을 차지고 있었다.(계산 결과 23%)
- 이직에 대한 인원의 비중과 비교했을 때, 최근 5년간 승진여부는 확연한 차이를 확인 할 수 있으며(**승진에 대한 불만사항**이 예측가능하며)
- 월급의 순위는 low-medium-high 순이었으나, **high의 비중이 극히 일부분**이었다. 미국은 **직무급**을 기반으로 시행되기 때문에, 높은 월급을 받는 다는 것은 높은 직무에서 근무한다고 볼 수 있을텐데, high의 비중이 적다는 것과 **승진이 적다**는 것은 연결해볼 수 있는 문제로 볼 수 있다.
- 구성원의 만족도는 0.8쯤에서 최고점을 이루고 있어서 높은편이라 볼 수 있으며,
- 최신 평가결과와, 월 평균 근로시간은 두개의 극대값을 형성하는 모형이다. 두 개가 유사한 모양을 띄고 있어서 **상관관계**가 있는지 확인해보도록 하겠다.

또한, **인적자원계획** 입장에서 보면 인력의 공급은 작년에 1명도 있지 않았지만 **올해는 23% 인원**이 나갔기에 엄청난 **공급의 부족**이 예상되며
퇴사의 추세가 이대로 유지된다면 기업의 위기를 초래할 수도 있다고 본다.

<br />

### feature들 간 상관관계가 있는지 여부


![4](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/4.png?raw=true)

상관관계를 살펴본 결과,
- 프로젝트의 개수, 최신 평가의 결과, 월 평균 근로시간은 서로 양의 관계가 나타나 있는 것으로 나타났으며,
- 이직과 만족도는 음의 상관관계 있는 것으로 나타났다.

강한상관관계가 아닌 약한 상관관계가 나타나므로, **데이터의 분포를 더 자세히 확인**하기 위해서 이하에서는 **퇴사인원과 잔류인원**을 구분하여 pairplot을 그려보도록 하겠다.

<br />

### 퇴사인원과 잔류인원의 pairplot

![5](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/5.png?raw=true)

퇴사인원의 특징을 설명하려면, 특정그룹으로 묶어낸 다음에 이들의 특징을 살펴보면 될 것이다.

- 위의 pairplot을 보면 **최신평가**와 **월 평균근로시간**은 **두 그룹의 분포**가 나타나며, 
- **만족도와 월 평균근로시간**과 **만족도와 최신평가**은 세 그룹의 분포가 나타난다.

이 그룹을 자세히 살펴보고, **our best and most experienced employees**를 찾기 위해서 이하에서는 **GE-9block의 관점**에서 데이터를 살펴보도록 하겠다.

<br />
<br />
<br />

# 2. our best and most experienced employees에 대한 정의 - GE 9 block

<br />

GE-9block에서 x축은 발전가능성 y축은 현재성과를 의미하므로 X축을 만족도, Y축을 최신평과로 두면 GE-9block의 관점에서 data를 해석할 수 있다.

![2](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/2.png?raw=true)
![6](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/6.png?raw=true)

- 모든 인원의(잔류인원+퇴사인원) 경우 9-block에 의하면 2,3,4,5,8,9의 인원들이 많이 나타났다.

![7](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/7.png?raw=true)

- 퇴사인원의 경우 9-block에 의하면 1,7,9의 인원들이 많이 나타났다.

즉 우리가 찾는 **our best and most experienced employees는 9번의 인원**에 해당함을 알 수가 있다.

**1번의 경우**에는 **퇴사를 하여도 오히려 조직의 입장에서는 도움이 되는** 인원들이며,

**7번의 경우**에는 **현재 조직이 처한 상황**에 따라 가치가 달라질 수 인원이다.(1목차에서 살펴본 인적자원 계획에 의하면 작년채용은 없었고 대거 이직을 했으므로 7번의 경우는 중요한 인원들이다.)

<br />

### 이직자들의 cluster별 묶어주기


![8](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/8.png?raw=true)

- 각 cluster별의 경계값을 찾아주기 위해 위와 같이 시각화를 해주었다. 
- cluster 기법인 kmeans 등을 쓰지 않는 이유는 **해당 범위외의 data가 묶이지 않기** 위해서이다.

<br />
<br />
<br />

# 3. Left reason(3_cluster) - PCA분석 및 graph

<br />

### PCA 분석
![9](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/9.png?raw=true)


보통의 주성분이 실수형으로만 이루어진 경우에는 PCA 주성분에 이름(naming)을 붙인 뒤에 의미를 해석한다.

하지만 이 경우에는 연속형 자료가 아닌 카테고리 및 정수 값이 주성분에 크게 나온것을 알 수가 있다.(실수형 보다 **데이터의 분산정도**가 커지기 때문에) 따라서 주성분을 naming하기 보다는 clsuter별 **원성분이 주성분에 차지하는 비중**으로 cluster간 비교를 하도록 하겠다.


첫번째 주성분에서는 cluster별로 3개구간 밖에 없는 salary의 차지하는 비중이 제일 컷으며,
두 번째 주성분에는 2개구간이 존재하는 Work_accident의 차지하는 비중이 제일 컷다.

**cluster1과 cluster3**의 세번째 주성분에서는 **number_project, average_montly_hours,time_spend_company**순으로 차지하는 비중이 컸으나,
**cluster 2**에서는 **승진의 여부**가 가장 큰 차지를 하고 있었다. 좀 더 구체적으로 살펴보기 위해서 PCA biplot을 그려보았다.

<br />

### PCA biplot 그리기

![12](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/12.png?raw=true)

**number_project, average_montly_hours,time_spend_company**등이 cluster_1, cluster_3에서 크게 그려지고 있으나, cluster_2에서는 
**승진여부**가 크게 그려지고 있다. 즉 cluster_2에서는 **number_project, average_montly_hours,time_spend_company**등이 유사한 집단인 것을 알 수 있다. 이하에서는 클러스터 별 구분이 되는 요소인 number_project, average_montly_hours,time_spend_company에 대해 **시각화** 해보도록 하겠다.

<br />

### 각 cluster 별 원인 진단(위에서 추론한 것을 시각해보기)

![15](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/15.png?raw=true)
![16](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/16.png?raw=true)
![17](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/17.png?raw=true)
![18](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/18.png?raw=true)

**radar chart**와 **distplot**등을 종합해보면, 

**cluster_1**의 경우에는 월 평균근로시간이 2번째로 크며, 프로젝트의 개수도 2번째이나 근속년수는 제일 컷으며,

**cluster_2**의 경우에는 월 평균근로시간 및 프로젝트의 개수 및 근속년수도 제일 작았다.

**cluster_3**의 경우에는 월 평균근로시간이 제일 크고, 프로젝트의 개수도 제일 많았으며, 근속년수는 2번째 였다.


이를 종합해보면
**cluster_1**의 경우에는 업무량등이 **적당한 편이어서 만족**하고 있는 편이나, 근속년수에 따른 **승진**이 안되고 있는 것에 불만인 것으로 원인을 찾을 수 있다.(cluster_1 890명 중에 5년간 승진인원은 1명이다.) 
**cluster_2**의 경우에는 **입사초기 인원**으로 적은 업무를 담당하고 있으나 업무에 대해 적응을 하지 못한 인원으로 볼 수 있다.(일명 **조기퇴사자**)
**cluster_3**의 경우에는 적당한 근속년수에도 불구하고 많은 일을 담당하고 있어 **과중한 업무량** 퇴사한 인원으로 볼 수 있다.(직무 불일치 등)


GE-9Block의 육성 및 방출 원칙을 근거로 해결책을 생각해보면, 
cluster_1의 문제를 예방하려면 직급을 세분화하거나 한 단계 더만들거나 역량급의 도입을 고려해볼 수 있으며(기존 3단계보다 좀 더 세분화된 임금체계가 필요하다. )

cluster_2의 문제는 굳이 해결할 필요가 없으나(조직의 환기를 위해서) 새로운 인력이 필요하다면, 조기 적응프로그램 도입 및 직무 순환 프로그램의 도입이 가능하다.
cluster_3의 문제는 업무량 조절 등이 필요한 상태이다.

이와 같은 **해결책**이 있다고 하더라도 **어떠한 인원들을 타케팅(targeting)** 해서 도입해야되는지 알아보도록 하겠다.

<br />
<br />
<br />

# 4. Predict which valuable employees will leave next - cosine similarity

<br />

### 유사도 구해주기(cluster_1의 경우를 예로 들어서)
- cluster_1의 경우로 이동가능한 인원들은 **9-block에서 6,8,9**에 속한 인원들이다.
- 또한 해당 유사도를 더 세분화해주기 위해서 **sales와 salary**별로 구분해서 구해주도록 하겠다.

<br />

### 코사인 유사도로 해준 이유는?
- 유사도를 구하는 방식은 유클리디안 점수기반 알고리즘, 코사인 기반, 자카드 계수, 피어슨 상관계수 등 다양한 종류가 있다.
- 하지만 코사인 유사도로 해준 이유는 **방향성**으로 유사도를 측정하기 때문이다.
- 즉 프로젝트의 개수, 월 평균 근로시간, 근속년수 등이 퇴직자와 비슷한 비율로 나타난다면 이 인원이 퇴직할 가능성이 높다고 생각하였다.

<br />

### 코사인 유사도가 85% 이상인 인원의 분포
![19](https://github.com/gogoj5896/Personal-project/blob/master/read_me_image/19.png?raw=true)

즉 위와 같이 해당 클러스터와 유사도를 기준으로 인원을 뽑아낸다음에 이들을 타케팅해서 이직 방지의 program을 운영할 수 있다.

<br />
<br />
<br />

# 5. 결론 및 추가 논의사항

<br />

### 결론

- 데이터를 **countplot**등으로 전반적인 상황을 살펴본 뒤에,

- 퇴사자와 잔류자를 구분하여 **pairplot**으로 cluster의 형태를 살폈다.

- 그룹핑 시킬때는 GE-9block을 이용해서 구분해주었으며 cluster내의 특징으로 각 **클러스터별 문제점**을 PCA 등으로 진단 및 해결책을 제시하였다.

- 어떤 인원이 퇴사할지에 대해서는 classification이 적용가능한 머신러닝(딥러닝) 이론을 적용하기보다는 **cosine 유사도**로 찾아내었다.

<br />

### 추가 논의사항

- kaggle에 있는 많은 사람들이 classification으로 예측을 하였는데, 어떤 것을 예측하였는지 이해하기 힘들었다. 
- 이 부분에 대해서는 classification 등을 다시 공부하고 무엇이 옳은것인지 판단해야될 부분이다.

