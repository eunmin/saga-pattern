# Applying the Saga Pattern - Caitie McCaffrey

https://www.youtube.com/watch?v=xDuwrtwYHu8

말이 엄청 빠르시다 ㅠㅜ

- 줄세우기를 가정하는건가?

## 2PC

- 코디네이터가 Prepare 단계와 Commit 단계를 나눠서 관리한다.
- 코디네이터는 단일 장애점이다.
- 메시지가 많이 필요하다 O(n^2)
- 스루풋이 감소한다.

## Spanner

- 구글 분산 데이터?
- TrueTime API?
- 비싸다. 동기화 문제를 풀 수 없다.

## Sagas

- 1987 논문으로 소개
- 롱 라이브 트랜잭션
- 섞을 수 있는 트랜잭션의 시퀀스
- T1, T2 ... Tn
- 시퀀스 안에 있는 트랜잭션은 완정 성공이거나 일부만 적용된 트랜잭션과 보상 트랜잭션
- C1, C2 ... Cn
- 의미적으로 Cn은 Tn를 취소한 것
- T1, T2 ... Tn 또는 T1, T2 .. Tj, Cj, ... C2, C1
- Saga는 실패 관리 패턴이다

## Saga 실행 코디네이터 (SEC)

- Saga Log
- Begin Saga, End Saga, Abort Saga , Start Tn, End Tn, End Saga
- 실패한 Saga
  - Begin Saga, Start T1, End T1, Abot Saga, Start C1 ... End Saga

## Saga 분산환경

- SEC는 Saga 로그를 관리한다.
- 필요하면 보상트랜잭션을 적용한다
- 서비스는 Start Saga 로그를 만들면 SEC는 다음 로그를 생성해 가며 적용한다.

## 보상 트랜잭션 적용하기

- 에러가 발생한 경우 Abort 로그를 쓰고 보상 트랜잭션을 시작한다.
- 보상 트랜잭션이 실패한 경우
- SEC가 실패하는 경우
  - 트랜잭션이 Start, End 로그가 다 있으면 안전
  - Abort된 다음에 보상 트랜잭션이 적용 되었으면 안전
  - Start는 있는데 End가 없는 경우 안전하지 않다. 어떻게 하라는거지?
- 보상 트랜잭션은 멱등원
