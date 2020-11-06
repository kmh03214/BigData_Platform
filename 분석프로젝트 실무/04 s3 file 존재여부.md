# s3 내 파일 존재여부 (Python)
s3에 파일이 존재하는지 여부는 s3fs (s3 file system)을 통해서 알 수 있다. 

```python
import s3fs

s3 = s3fs.S3FileSystem()

print(s3.exists('s3://df-dap-prd-bucket/pur_mh/data_test.py'))
```