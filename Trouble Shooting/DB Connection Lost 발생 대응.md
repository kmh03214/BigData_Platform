# MySQL DDL 실행 시, Connection Lost 발생 Trouble Shotting

## MySql에서 특정 테이블에 대해 변형을 일으키는 DDL(Data Definition Language)이 적용되지 않는 상황에 대해 트러블 슈팅.

### 에러내용
> Operation failed: There was an error while applying the SQL script to the database.
ERROR 2013: Lost connection to MySQL server during query
SQL Statement:
ALTER TABLE ...


~~~sql
# 다음 명령어로 프로세스리스트 확인
show full processlist;

# 쿼리를 막고 있는 프로세스 제거
kill {process id};

# Transaction 확인
SELECT * FROM information_schema.INNODB_TRX;

# 트랜잭션에서 trx_mysql_thread_id 확인 후 이 Id값과 mysql의 프로세스 id값 비교하여 원인파악
# 삭제
kill {process id};
~~~