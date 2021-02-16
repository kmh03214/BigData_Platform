
1. 클러스터 내에 sample.csv를 생성하여 
2. S3 버킷(bucket_name)에 올려놓을 수 있습니다.

다음 코드를 참고해 주세요

```python
from pathlib import Path
import boto3

# S3 클라이언트 생성.
s3 = boto3.client('s3')

# 클러스터내 로컬에 sample.csv 생성
subprocess.call("touch sample.csv" , shell=True)

# s3에 업로드
# 첫번째 매개변수 : 로컬에서 올릴 파일이름
# 두번째 매개변수 : S3 버킷 이름
# 세번째 매개변수 : 버킷에 저장될 파일 이름.
s3.upload_file("sample.csv", "bucket_name", "sample.csv")
```