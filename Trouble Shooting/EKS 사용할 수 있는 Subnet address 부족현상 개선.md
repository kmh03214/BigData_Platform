# EKS 사용할 수 있는 Subnet address 부족현상
[Error]
Amazon AutoScaling was unable to launch instances because there are not enough free addresses in the subnet associated with your AutoScaling group(s).

- Public EKS subnet

    [해결방안]
    1. 아래 명령어로 가용서브넷 선점을 조절한다.
    
    ~~~bash
    # 아래 설정으로 실험
    kubectl set env ds aws-node -n kube-system WARM_IP_TARGET=1
    kubectl set env ds aws-node -n kube-system MINIMUM_IP_TARGET=15
    ~~~

    1개는 노드에 할당된 주소 16개는 파드가 8개가 뜨는데 오류로 종료되면 다시 생성될 여유분 1개씩 더 잡고 있는듯 8*2 = 16

    - 워커노드 1개당 8개의 파드가 생성 사용가능한 address가 17개씩 줄어든다.

        | node개수 | subnet1 | subnet2|
        |-------|-------|-------|
        | 7 |  163 | 156 |
        | 20 | 60  | 39 |
        | 21 | 60  | 22 |
        | 22 | 43  | 22 |
        | 23 | 26  | 22 |
        | 30 | 0   | 0  |

    2. 서브넷 CIDR 대역폭을 늘린다.

    3. 서브넷을 추가로 구성한다(?)
        - 서브넷 4개 달린 accu-public-modeler3 컴퓨팅그룹 추가 -> 새로 붙인 컴퓨팅그룹에서는 파드가 안뜸