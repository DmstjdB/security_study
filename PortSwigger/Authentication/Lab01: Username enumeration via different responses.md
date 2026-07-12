# Lab: Username enumeration via different responses 
## Difficulty 
Apprentice

## 취약점
Username Enumeration

## 취약점 설명

로그인 실패 시 사용자 존재 여부에 따라 서로 다른 응답 메시지를 반환한다.
이를 통해 공격자는 존재하는 Username을 추측할 수 있다.

## 취약점 발생 원인

로그인 실패 상황에서 모든 사용자에게 동일한 응답을 반환하지 않고,
존재하지 않는 Username과 잘못된 Password에 대해 서로 다른 오류 메시지를 제공했기 때문이다.

## 발생 가능한 위험

- 유효한 사용자 계정 식별
- Brute Force 공격 성공률 증가
- Credential Stuffing 공격에 악용 가능
- 계정 정보 노출

## 사용한 도구
- Burp Suite Repeater

## 실습 과정
1. 로그인 페이지에서 임의의 Username과 Password로 로그인 시도
2. Burp Suite > proxy > HTTP History에서 `POST /login` 요청 확인
3. Request를 Repeater로 전송
4. Password는 고정하고 Username만 변경하며 Response 비교

![Default Request](../../images/Authentication/Lab01_request_default.png)

5. 존재하지 않는 Username → `Invalid username`

![Invalid Username](../../images/Authentication/Lab01_repeater_invalid_username.png)

6. 존재하는 Username → `Incorrect password`

![Valid Username](../../images/Authentication/Lab01_repeater_valid_username.png)

7. 응답 차이를 통해 유효한 Username 확인
8. 이후 Password를 변경하며 올바른 비밀번호 확인
9. 로그인 성공

![result](../../images/Authentication/Lab01_login_success.png)


## 대응 방안

- 로그인 실패 시 항상 동일한 오류 메시지를 반환한다.
- 응답 길이와 응답 시간도 동일하게 유지한다.
- 로그인 시도 횟수를 제한(Rate Limiting)한다.
- 계정 잠금(Account Lockout)을 적용한다.
- 다중인증(MFA)를 사용한다.

## 배운 점

이번 Lab을 통해 단순히 응답 메시지가 다르다는 것만으로도
사용자 계정을 추측할 수 있다는 점을 알게 되었다.

취약점 진단에서는 Response의 작은 차이(메시지, 길이, 상태 코드)를
비교하는 습관이 중요하다는 것을 배웠다.
