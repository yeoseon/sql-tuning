# 친절한 SQL 튜닝  

## # SQL 파싱과 최적화  
> SQL은 결과집합을 구조적(structured), 집합적(set-based)으로 선언하지만 그 결과집합을 만드는 과정은 절차적(procedural)이다.  
> 즉, 프로시저가 필요하며 이 프로시저를 만들어내는 것이 바로 SQL 옵티마이저이다.  
> 프로시저를 작성하고 컴파일 해서 실행 가능한 상태로 만드는 전 과정을 SQL 최적화라고 한다.
- SQL 파서와 로우 소스 생성기와 마찬가지로 별도 프로세스가 아니라, 서버가 가지는 기능일 뿐
- 옵티마이저가 선택한 실행계획대로 수행하고 싶지 않다면, HINT 처리를 통해 변경할 수 있다.  
- 별일 없다면 HINT는 빈틈없이 기술하자.  
- 자주 사용하는 힌트 목록은 27p 참고, [여기](https://github.com/yeoseon/tip-archive/issues/273) 를 봐도 좋고  

1. SQL 파싱  
- SQL문을 이루는 구성요소를 분석하여 파싱트리 생성  
- Syntax 체크, Semantic 체크 수행  

2. SQL 최적화  
- SQL 옵티마이저: 미리 수집한 통계를 바탕으로 다양한 실행계획을 세우고, 가장 효율적인 하나 선택  
    - 실행계ㅈ획을 찾아낸다.  
    - 데이터 딕셔너리에 수집해둔 오브젝트 ㅓㅇ보 등을 이용해 예상비용을 산정한다. 
    - 최저비용 실행계획을 선택한다.  
3. 로우 소스 생성  
- 로우 소스 생성기(Row-Source Generator)가 실행가능한 코드 또는 프로시저로 포매팅  

## # SQL의 공유 및 재사용  

1. 소프트파싱과 하드파싱  
- SQL 파싱 -> 최적화 -> 로우소스 생성 과정을 거쳐 생성한 내부 프로시저를 '라이브러리 캐시'에 저장하여 재사용  
- SQL 파싱 후에 라이브러리 캐시에 해당 SQL이 존재하는지 여부를 판단  
- 존재하면 해당 캐시를 사용 -> 소프트파싱  
- 존재하지 않으면 최적화, 로우 소스 생성 단계 진행 -> 하드파싱  

2. 바인드 변수의 중요성  
- SQL은 ID가 없다. SQL 전체 텍스트 자체가 ID  
- 작은 부분이라도 수정되면 그 순간 다른 객체가 새로 탄생하여 저장되는 구조  
- 따라서 바인드 변수를 파라미터로 하나 받는 프로시저 하나를 공유해서 사용함으로써 재사용  
```
...
String SQLStmt = "SELECT * FROM * CUSTOMER WHERE LOGIN_ID = ?";
PreparedStatement st = con.prepareStatement(SQLStmt);
st.setString(1, login_id);
ResultSet rs = st.executeQuery();
...
```
이렇게 바인드 변수를 이용하면, 라이브러리 캐시를 확인했을 때 아래와 같은 하나의 SQL만 저장된다.  
```aidl
SELECT * FROM CUSTOMER WHERE LOGIN_ID = :1
```

# Table Full Scan VS Index Range Scan

1. Table Full Scan  
-  
2. Index Range Scan  
