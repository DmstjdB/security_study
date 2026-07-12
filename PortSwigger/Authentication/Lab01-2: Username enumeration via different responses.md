# Lab: Username enumeration via different responses 
## Difficulty 
Apprentice

## 취약점
Username Enumeration

## 사용한 도구
- Burp Suite Intruder
- 저번 시간에 Repeater를 이용해 해결하였던 문제를 Intruder를 이용해 다시 시도하였다.

## Intruder와 Repeater의 차이?
- Repeater는 요청을 수동으로 수정하여 전송하고, Intruder는 Payload를 자동으로 전송하여 테스트한다.

## 공격 방식
1. Sniper: ID/PW 중 1개만 변경
2. Pitchfork: 둘 다 변경
3. Cluster Bomb: 모든 조합 생성

## 실습 과정
1. 로그인 페이지에서 임의의 Username과 Password로 로그인 시도
2. Burp Suite > proxy > HTTP History에서 `POST /login` 요청 확인
3. Request를 Intruder로 전송
4. Sniper attack 선택
5. Payload에 id를 수정할 부분으로 추가
6. Paylaod type: Simple list로 하고 Candidate username list 복사/붙여넣기
7. Start attack
8. Status Code와 Length 차이 관찰하여 유일하게 다른 length 찾음
9. payload pw로 바꾸어 5-8다시 진행
10. status code 302 & length 다른 값 찾음
11. 로그인 성공

![result](../../images/Authentication/Lab01_2_result.png)



## 배운 점
brute force를 할 때 변수를 하나씩 입력하지 않아도 해결할 수 있는 방법이 있다는 것을 알았다. 
