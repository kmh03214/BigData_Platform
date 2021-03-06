# SK Accuinsight

- 개요  
Accuinsight에는 굉장히 유용한 기능이 하나가 있다. 글로벌 변수라는 것인데, 이 기능의 역할을 알아본다.


## 글로벌 변수의 필요
Accuinsight+ Pipeline서비스에는 mlTrain이라는 노드가 있다.
해당 노드는, 말 그대로 Machine Learning Model을 Train 하는 역할을 한다.  
<img src = "./img/02 pipeline model/img0.png"></img>


글로벌 변수 기능은 mltrain노드에서 같은 모델을 여러개 생성해야 할 때, 굉장히 유용하게 쓰일 수 있다.

여기서 의문이 하나 들 수 있는데, 왜 같은 모델을 여러개 생성해야 하는가? 이다.

> 분석 프로젝트를 하다보면, 같은 데이터 컬럼을 쓰는데, 카테고리 별로 학습을 시키고 싶을 때가 있다.  
예를 들면, 유통사 프로젝트의 경우 상품 별로, 해당 모델을 만들어서 해당 상품에 대한 예측률을 높이고 싶을 때가 있다.  
신발, 옷, 모자, 바지, 악세서리 같은 카테고리로 나누어 모델을 학습시켜 모델 파라미터를 공유하지 않도록 말이다.

|name|category|model|
|---|---|---|
| | 신발 |RF_model_신발 |
| | 옷 |RF_model_옷 |
| | 모자|RF_model_모자 |
| | 바지|RF_model_바지|
| | ... | ... |

위와 같이 카테고리별로 같은모델을 여러개 생성한다는 말이다.

<img src = "./img/02 pipeline model/img3.png"></img>

위와같이 신발, 옷, 모자, 바지 카테고리가 4개라면, mltrain 노드 4개를 두어 학습을 시키면 된다. 가능하다.  
하지만, 카테고리가 90개 100개가 넘어가면 어떻게 할 것인가? 상식적으로 일일이 Drag & Drop으로 하나씩 넣고 데이터 등록하고 할 수는 없는 노릇이다.


## 글로벌 변수
이를 글로벌 변수를 통해 쉽게 구성할 수 있는 방안에 대해 설명하고자 한다.

<img src = "./img/02 pipeline model/img1.png"></img>

오른쪽 상단 위, 설정 -> 변수 관리 탭에 들어가면, 추가라는 버튼을 통해 변수 등록을 할 수 있다.

<img src = "./img/02 pipeline model/img2.png"></img>

위와 같이, 변수추가라는 버튼을 클릭하게 되면 아래의 표에 변수명 column이 생기고 현재 그림은 brand라는 변수명을 지정하여 만들었을 때의 예이다.  

use_yn 컬럼은 해당 변수들을 사용할지 말지 결정하는 플래그이다.

> __변수 추가버튼 오른쪽은 반복버튼인데, 해당 반복버튼을 누르면 반복 column이 1개씩 늘어나며, 해당 인덱스마다 새로운 brand변수를 지정해 줄 수 있으며, 해당 변수를 파라미터로 받아, 코드가 실행된다. 이 부분은 노드가 객체가 해당 반복개수만큼 생성되는 것이라고 볼 수 있다.__

즉 위 그림에 따르면, brand라는 글로벌 변수를 쓰게되면 노드는 각 4개씩 생성되어 총 파이프라인이 4개가 실행된다.

<img src = "./img/02 pipeline model/img4.png"></img>

근데, 우리는 model만 4개를 생성시켜서 각 모델이 예측데이터셋에 대해 Prediction 하기를 원한다.
따라서, 모델부분이 아닌 나머지 노드들이 4번씩이나 반복 실행된다는건 매우 큰 자원 낭비이다. (그림에서 박스 친 부분 또한 4번씩 실행되게 된다.)

그렇기 때문에, 나머지 노드를 제어할 글로벌 변수를 선언하여, 처리하는 방법이 있다.
즉, 해당 노드는 실행 되지만, 실행하지 말라는 변수를 보게 되면, 해당 코드를 실행하지 않고 자원을 반납하게 만든다. 파이프라인에는 변수를 통해 제어하는 두 가지 방법이 있는데 원리는 같다.

### 1) 변수관리 목록에서 execute변수를 추가하는 방법
<img src = "./img/02 pipeline model/img5.png"></img>
위 그림을 보면 execute라는 변수컬럼이 하나 더 생성 된 것을 볼 수 있다. 
<img src = "./img/02 pipeline model/img6.png"></img>
그 뒤에는, 실행하고 싶지 않은 노드 코드에 조건문으로 해당 변수를 사용하면, True 경우에만 실행되므로, 한 번만 실행되는 결과를 가지고 올 수 있다.

### 2) Parameter에서 글로벌 변수 선언하는 방법
<img src = "./img/02 pipeline model/img7.png"></img>
위 그림을 보면 오른쪽 설정창에 Parameter탭이 있다. 해당 탭에보면 Key/value를 적는 칸이 있는데,
Key에는 사용할 변수명을 value에는 변수가 가질 값을 적으면 된다.
<img src = "./img/02 pipeline model/img8.png"></img>
위와 동일하다.

이상.












# 자원 최적화 하기.

어떤 노드가 100개의 객체가 형성되어 시작을 하게 된다면, 리소스를 가지고 반납하지 않는 경우가 생기게 된다.

글로벌 변수를 통해 99개의 노드를 유령노드로 1개의 노드를 실행노드로 만들었을 때, 해당노드가 계속실행 될 때, 또는 실행 후 다음노드로 넘어갔을 때에도 자원을 반납하지 않으므로 pending이 걸리게 된다.

이 때, 유령노드에게는 자원을 최소화로 주고, 우리가 실행시키고 싶은 1개의 노드에만 자원을 할당해주는 방법이 있다.

파이프라인 Pyspark 노드 UI에는 Spark Option이라는 설정UI가 존재하는데, 여기에 해당 노드에 할당할 자원이나 옵션들을 설정할 수 있다.

다만, 글로벌변수를 두어 100개의 객체를 실행할때, 모든 노드에 같은 자원을 할당할 것인지는 생각해 봐야한다.

우리가 실제로 실행이 될 노드에서만 자원을 할당 받으면 되니깐 말이다.

이 때는, 노드 UI에 설정값을 동일하게 작게 준 뒤(유령노드에 적용하고싶은 스파크 옵션 값)

실제 스파크 코드 안에서는 글로벌 변수값에 의해 실행되는 코드에서 spark option을 주는 코드를 생성하면 된다.


