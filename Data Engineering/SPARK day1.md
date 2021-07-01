머신러닝 언어가 점점 python화.

파이썬 -> 명령어주면 결과가 바로 나옴
자바 -> 풀 컴파일 해야 결과가 나옴

Hive -> 대용량(peta byte)의 데이터 처리
- 맵리듀스 사용했었음.
- Hive on Spark 엔진 생김

Pig -> Transformation
- 사라지는 추세

EMR -> Public Cloud에 Hadoop 서비스 제공

Cloudera -> CDP (integrated Platform)


저장 / 처리 

연습할 때.
Databricks -> community edition

# SPARK - General process engine for large-scale data processing
Master / Slave
Dirve / executor


- Written in [Scala]
    - flow chart
        폰 노이만 구조 (2000년도 까지 문제 없음)
        - 분산처리 병렬처리
            State 유지가 어려움 (Asynchronize)
        - Functional Programming이 활발해짐.

    - Functional Programming
        immutable(변화 불가능 / No-state) - 모든 state 저장

    - 의문
        인모메리 / immutable ? -> OOM 발생 안할까? -> 메모리관리가 아키텍쳐부분에서 중요한 요소
- Spark shell

- Spark applications

- RDD
    Spark fundamental unit of data in Spark
    - Resilient
    - Distributed
    - Dataset

- Creating an RDD
    - From a file or set offiles
    - From data in memory
    - From another RDD