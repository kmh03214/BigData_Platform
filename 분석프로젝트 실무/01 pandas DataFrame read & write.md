# pandas DataFrame S3에 직접 저장하기
python3를 이용하여 DataFrame을 csv형식으로 s3에 다이렉트로 저장하는 방법 두가지를 소개하겠습니다.
물론, 로컬에 csv를 저장 후, Drag in Drop이나 aws명령어를 사용하여 올릴 수도 있지만, 불편함이 존재 합니다.

첫번째는 boto3 라이브러리를 사용하는 겁니다.

일단, boto3를 설치해야 합니다. 없다면 아래 pip3명령어로 설치해주시면 됩니다.
```bash
pip3 install boto3
```
