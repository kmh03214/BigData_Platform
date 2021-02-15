# AWS EMR에서 master / core / task 노드를 생성했을 때, task node에 labeling 달아주기.

- 개요
    EMR에서 클러스터를 구성하게 되면, 마스터/코어/테스크노드가 기본적으로 구성이 된다.
    TASK 노드는 Default로 레이블링이 되어있고, 코어는 CORE로 레이블링이 되어있다.
    여기서 TASK 노드의 label을 바꿔주기 위한 글이다.

- 상황
    일단, Master 1 / Core 3 / Task 1 개를 띄워서 spark 잡을 수행하는 상황이었는데 Task노드는 분석용샌드박스(Jupyter notebook)로 쓰고싶었기 때문에,
    spark 잡에 스케쥴링을 걸어 놓고, Core 노드에서만 수행되게 하고 싶었다. 근데, Task node가 labeling이 Default로 되어있었기 때문에, spark job을 수행하게 되면, Default로 labeling된, task node에서도 자원을 끌어다 쓰게 된다.

    따라서, tasknode의 label을 바꿔줄 필요가 있었는데, 이거 때문에 몇 주 간 삽질을 했다... (검색해도 자료가 없었기 때문에...)

- 해결 방안
    1. Tasknode에 접속한 뒤 yarn-site.xml 파일을 찾아 vi 편집기로 다음 내용을 수정한다.
        ~~~
        # 터널링하여 접속
        $ ssh "{pem.key}" <계정>@<테스크노드ip>
        
        # 관리자계정으로 변경
        $ sudo -i
        ~~~

        테스크노드의 파티션을 구성하여 yarn-site.xml에 해당 파티션 레이블을 추가해야한다. 일단 파티션을 구성한다. 그리고 CORE의 exclusive 옵션을 true로 변경 해준다. exclusive 옵션은 해당 큐에서 잡이 스케쥴링 될 때, 클러스터 내에 자원이 여유가 있어도 지정한 파티션만 이용하여 작업한다는 뜻이다.
        즉 , CORE(exclusive=true) 이면, CORE 파티션에 잡이 배정된건 다른 파티션에서 작업하지 않는다는 뜻이다.

        [그림]
        ~~~
        # TASK 파티션을 만들어준다.
        $ yarn rmadmin -addToClusterNodeLabels "TASK"

        # yarn rmadmin -removeToClusterNodeLabels

        # 해당 파티션 레이블이 추가되었는지 확인한다.
        $ yarn cluster --list-node-labels
        ~~~

        
        아래 내용을 추가한다. 기본적으로 AWS EMR은 master / core 노드만 아래 설정이 되어있기 때문에, task노드는 default 레이블로 잡힌다. <a href = 'https://docs.aws.amazon.com/ko_kr/emr/latest/ManagementGuide/emr-plan-instances-guidelines.html'>참고링크</a>
        
        [그림]
        ~~~
        # yarn-site.xml 편집 -> 보통 /etc/hadoop/conf 경로에 있다.
        $ vi /etc/hadoop/conf/yarn-site.xml

        # 아래 내용을 추가한다.
        <property>
            <name>yarn.nodemanager.node-labels.provider</name>
            <value>config</value>
        </property>

        <property>
            <name>yarn.nodemanager.node-labels.provider.configured-node-partition</name>
            <value>TASK</value>
        </property>
        ~~~

        yarn node manager을 재시작 해준다.(yarn-site.xml 적용)
        ~~~
        # nodemanager 프로세스 검색
        $ ps -ef | grep node

        # 위에서 나온 결과의 nodemanager PID로 프로세스를 죽인다. (다시 살아남)
        $ kill -9 <nodemanager PID>
        ~~~

        다음과 같이 8088(RM 모니터링) 포트에서 TASK node에 tasknode가 정상적으로 배정된것을 확인할 수 있고, job 실행시 tasknode가 아닌 core노드에서만 실행 되는것을 확인 할 수 있다.


