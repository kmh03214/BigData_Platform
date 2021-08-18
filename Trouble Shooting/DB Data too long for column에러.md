# Data too long for column Error 대응

해당 에러에 관해 정리.

- 에러상황 : python 에서 pymysql을 통해 데이터를 Insert를 하는 과정에서 발생한 에러

    - 해당 에러에 관해서 해결하는 방법은 여러가지가 있지만, 구글링에서 접하는 에러 해결방법중에, sql_mode 변경하라는 방법이 주로 소개 된다. 하지만, 해당 방법으로 문제를 해결할 시, 더 큰 장애상황을 맞이 할 수 있다.
    왜냐하면 
    ~~~md
    1. 글로벌하게 적용하는 옵션이기 때문에, 내가 관리하는 테이블 뿐 아닌 다른 테이블까지 영향을 미칠 수 있다.
    2. 해당 옵션을 적용하게 되면, varchar(length) 의 length 보다 큰 값이 들어갈 경우, 자동으로 string의 길이가 조절되어 잘려 나가는 상황이 발생될 수 있다.  <데이터 무결성에 심각한 영향을 미칠 수 있음>  
    등... 의 이유들이 있다.
    ~~~

    따라서 아래의 sql_mode를 변경하는 건 추천하지 않는다.
    ~~~sql
    # sql_mode 조회
    select @@global.sql_mode; 
    # 해결방법
    # sql_mode의 Strict mode 비활성화
    ~~~

- 해결방법
    - 가장 기본적인 해결방법으로는 내가 삽입하려고 하는 column의 Varchar length를 확인 후, 늘려주는 방법이다.

    ~~~sql
    # columns 길이 조회
    show columns from <table명>;
    # columns 길이 수정
    alter table <table명> modify <column명> varchar(변경할 길이);
    ~~~


