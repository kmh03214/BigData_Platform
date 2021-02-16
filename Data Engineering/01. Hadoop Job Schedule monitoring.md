# Spark Job monitoring 하기
# 관련 기술 AWS / EMR / Haddop / Yarn / Spark

하둡에 Yarn이 관리하는 Job들이 실행되고 있는 프로세스를 모니터링하는 방법에 대해서 알아보자.

일단, AWS의 EMR 클러스터 (elastic Map Reduce) 서비스를 이용하면서, 클러스터 ip의 8088 포트로 접속을 하면 (즉, [ip:8088]) 다음과 같은 화면이 뜨게 된다.

<img src = "./img/01 Hadoop Job monitoring/img1.png"></img>

여기서 현재 EMR에서 실행되고 있는 Application의 리소스 현황이나 어떤잡이 어느정도 완료 됐는지 스케쥴은 어떻게 잡혀있는지 등의 정보들을 모니터링 할 수 있다. 이는 Application이 실행이 안될 때 같은 트러블 슈팅을 할 경우 중요하고, 1차적으로 손쉽게 찾아볼 수 있도록 도와준다.

<img src = "./img/01 Hadoop Job monitoring/img2.png"></img>
위 그림의 분홍색 박스에 들어있는 요소들을 살펴보면 직관적으로 이해하기 쉽다.

- Apps Submitted : 제출된 어플리케이션 개수
- Apps Pending : 대기중인 어플리케이션 개수
- Apps Running : 실행중인 어플리케이션 개수
- Apps Completed : 완료된 어플리케이션 개수

이다, 여기서 Pending을 자주 보는데, Spark를 쓰게 되면, 각 파이프라인의 노드에 대해 리소스를 할당하여 분산처리를 하게 되는데, 이 때 총 리소스를 다 할당하거나, 리소스를 할당 받은 노드의 프로세스가 계속해서 돌면서(무한루프같은) 해당 리소스를 해제시키지 않고 계속 보유하는 경우에 생길 수 있다. 그렇다면, 이 부분을 해결하면서 현재 갖고있는 자원을 최적화 시켜야 될 것이다.

이 부분을 하기 위해 뒤쪽에 
- Memory Used
- Memory Total
- Memory Reserved
- VCores Used
- Vcores Total

과 같은 현황들이 등장하게 되는 것이다. 이런 부분을 보면서 내가 어느정도 크기의 EMR 인스턴스를 사용해야 할지 결정하면서 클라우드에 지불하는 비용을 최적화 시킬 수 있다.

<img src = "./img/01 Hadoop Job monitoring/img3.png"></img>

그리고 왼쪽 탭목록에서 Scheduler를 누르게 되면 현재 큐에 스케쥴링 된 잡들이 자원을 어떻게 사용하고 있는지 현황이 화면에 추가되어 등장하게 된다.

그 아래부분은 Submit된 Application들의 현황을 자세하게 나타내주는 현황판인데, search 기능을 통해 문제가 있을 것 같은 application을 검색하여 볼 수 있다.

# < Trouble Shooting >

이 서비스를 이용하면서 가장 먼저 부딪혔던 트러블은, Application이 제출됐는데 완료가 안되는 상황에서 Application Frontend에서도 종료가 안되는 상황이었다.

해당 상황에서는, Application Job은 계속해서 실행 되는데, 리소스를 계속 낭비하고 있으니 문제가 되고, 새로운 워크플로우를 실행하거나 파이프라인을 실행 할 때, 내가 설정한 클러스터의 최대자원을 활용할 수 없게 되므로 클러스터를 다시 내렸다가 올리는(대략 20~30분소요 + 비용부담은 덤) 최악의 경우가 생길 수 있다.

이 때에는, 클러스터 내부로 들어가 현재 Application 실행을 담당하는 Yarn 매니저를 통해 이를 컨트롤해주면 된다.

ssh 터널링을 통해 클러스터로 들어간다.
```zsh
$ ssh <id>@<clustip>
```
<img src = "./img/01 Hadoop Job monitoring/img4.png"></img>
그럼 위와 같은 화면이 나오고 여기서 다음명령어를 통해 yarn이 실행중인 application list가 나오게 된다.
```zsh
$ yarn application -list
```
<img src = "./img/01 Hadoop Job monitoring/img5.png"></img>
보면 맨 첫 열에 Application ID가 있는데 다음 명령어를 통해 프로세스를 킬할 수 있다.
```zsh
$ yarn application -kill <application id>
```
프로세스가 종료되면서 해당 노드가 가지고 있던 리소스도 해제되고 어플리케이션이 종료가 되게 된다.