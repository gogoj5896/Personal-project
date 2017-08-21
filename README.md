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
###  - 문제의 정의
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

- **GE-9block**은 GE의 CEO였던 Jack Welch가 **발전가능성(동기부여-x축)**, **결과(성과-y축)**를 사용하여 9가지 block을 구성하고 **상위 10%**는 핵심인재로서 지속적인 **육성**, **하위 10%**는 **방출**하는 인력관리 방법이다.
![9-block.png](attachment:9-block.png)

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
