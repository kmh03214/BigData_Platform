from hdfs3 import HDFileSystem로 HDFS 불러올 때 Import 에러가 발생하는 현상

[에러내용]

> ImportError: Can not find the shared library: libhdfs3.so


[해결방법]

다음 명령어 복사 붙여넣기 후 실행하여 conda update 및 패키지 install 최신화
~~~bash
conda update -y conda && \
conda install -c conda-forge protobuf && \
pip install py4j && \
pip install --upgrade pip && \
pip install hdfs && \
conda install -y hdfs3 libhdfs3 -c conda-forge
~~~