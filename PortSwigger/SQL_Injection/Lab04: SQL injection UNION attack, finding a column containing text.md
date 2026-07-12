# Lab: SQL injection UNION attack, finding a column containing text

![title](../../images/SQL_Injection/Lab04_title.png)

## Difficulty

Practitioner

---

## 취약점

- SQL Injection (UNION Attack)

---

## 취약점 설명

UNION SELECT를 이용한 SQL Injection 공격에서는 기존 SELECT 문의 컬럼 개수뿐만 아니라, 문자열을 허용하고 실제 화면에 출력되는 컬을 찾아야 한다.
출력되는 컬럼을 확인한 후 해당 위치에 원하는 데이터를 삽입하면 데이터베이스의 다른 테이블에 있는 정보도 조회할 수 있다.


## SQL Query
기존 Query

```sql
SELECT name, description, price
FROM products
WHERE category='Gifts';
```

공격 Query

```sql
SELECT name, description, price
FROM products
WHERE category='Gifts'

UNION

SELECT NULL,'test',NULL--;
```


---

## 발생 가능한 위험

- 다른 테이블의 데이터를 화면에 출력할 수 있다.
- 사용자 계정 및 비밀번호 등 민감한 정보를 조회할 수 있다.
- 데이터베이스 구조를 파악하여 추가적인 SQL Injection 공격에 활용할 수 있다.
- 관리자 계정 탈취 및 개인정보 유출로 이어질 수 있다.

---

## 사용한 도구

- Burp Suite Repeater

---

## 실습 과정
1. Burp Suite에서 GET /filter?category= 요청을 Repeater로 전송
2. `ORDER BY`와 `UNION SELECT NULL`을 이용하여 컬럼 개수가 3개임을 확인
3. 문자열이 출력되는 컬럼을 찾기 위해 아래 Payload 순서대로 테스트<br/>

![result](../../images/SQL_Injection/Lab04_result.png)<br/>

```text
'+UNION+SELECT+'test',NULL,NULL--

'+UNION+SELECT+NULL,'test',NULL--

'+UNION+SELECT+NULL,NULL,'test'--
```

4. 두 번째 컬럼에서 문자열이 화면에 출력되는 것과 200 Ok 반환하는 것 을 확인
5. 이를 통해 2번째 컬럼이 문자열을 허용하며 실제 화면에 출력되는 컬럼임을 확인


---

## Payload
```text
'+UNION+SELECT+NULL,'test',NULL--
```


---

## 대응 방안

- Prepared Statement(Parameterized Query)를 사용하여 사용자 입력과 SQL Query를 분리한다.
- 사용자 입력을 SQL Query에 직접 연결하지 않는다.
- 데이터베이스 오류 메시지를 사용자에게 노출하지 않는다.
- 최소 권한 원칙을 적용하여 불필요한 데이터 조회를 제한한다.
- UNION을 이용한 SQL Injection이 가능하지 않도록 입력값을 안전하게 처리한다.

---

## UNION Attack으로 정보가 유출되는 이유

1. UNION은 두 개의 SELECT 결과를 하나로 합치는 SQL 문법이다.
2. 공격자는 화면에 출력되는 컬럼을 찾은 후, 문자열 대신 데이터베이스의 컬럼(`username`, `password` 등)을 조회하여 화면에 출력시킬 수 있다.

예시

기존 Query

```sql
SELECT name, description, price
FROM products;
```

공격 Query

```sql
SELECT name, description, price
FROM products

UNION

SELECT NULL, username, NULL
FROM users;
```

결과적으로 상품 목록과 함께 `users` 테이블의 `username`이 화면에 출력되어 민감한 정보가 노출될 수 있다.

> **UNION 공격 순서**
> 1. 컬럼 개수 확인 (`ORDER BY`, `UNION SELECT NULL`)
> 2. 출력 가능한 컬럼 확인
> 3. 테이블 이름 확인
> 4. 컬럼 이름 확인
> 5. 원하는 데이터(username, password 등) 조회

---

## 배운 점
실습을 하면서 UNION Attack으로 웹페이지에 출력되지 않아야 할 데이터를 출력하게 할 수 있다는 것은 이해하였지만, 어떻게 데이터베이스에 있는 정보가 어떻게 유출되는 이해할 수 없었다. 
그래서 이 부분에 대하여 더 찾아보았고, 그 결과 SQL Injection의 목적은 단순히 내가 입력한 문자열을 출력하는 것이 아니라, 데이터베이스에 존재하는 테이블과 컬럼을 조회하여 그 안의 데이터를 애플리케이션의 응답에 포함시키는 것이라는 점을 이해하였다. 
또한 실제 데이터를 조회하기 위해서는 먼저 테이블과 컬럼의 이름을 알아야 한다는 것을 알게 되었으며, 다음 Lab에서는 이를 확인하는 과정에 집중하여 학습해 보고자 한다.
