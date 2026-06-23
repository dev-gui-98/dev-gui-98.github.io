---

title: "ORA-01861 오류 해결 과정"
date: 2026-06-23 09:00:00 +0900
categories: [Troubleshooting]
tags: [Oracle, SQL, Error]
--------------------------

# ORA-01861 오류 해결 과정

## 문제 상황

데이터 조회 기능을 테스트하던 중 다음과 같은 오류가 발생하였습니다.

```sql
ORA-01861: literal does not match format string
```

처음에는 SQL 문법 문제라고 생각했지만 쿼리를 확인해도 특별한 이상이 보이지 않았습니다.

---

## 원인 분석

확인 결과 Oracle DATE 타입 컬럼과 문자열(String)을 비교하면서 발생한 문제였습니다.

예를 들어 다음과 같은 SQL을 사용하고 있었습니다.

```sql
SELECT *
FROM TB_NOTICE
WHERE REG_DT = '2026-06-23';
```

REG_DT는 DATE 타입인데 문자열과 직접 비교하고 있었기 때문에 Oracle이 날짜 형식을 해석하지 못해 오류가 발생하였습니다.

---

## 해결 방법

TO_DATE 함수를 사용하여 문자열을 날짜 형식으로 변환하였습니다.

```sql
SELECT *
FROM TB_NOTICE
WHERE REG_DT = TO_DATE('2026-06-23', 'YYYY-MM-DD');
```

또는 시간 정보를 제외한 날짜만 비교해야 하는 경우 다음과 같이 수정할 수 있습니다.

```sql
SELECT *
FROM TB_NOTICE
WHERE TRUNC(REG_DT) = TO_DATE('2026-06-23', 'YYYY-MM-DD');
```

---

## 배운 점

이번 문제를 통해 Oracle의 DATE 타입은 단순한 날짜가 아니라 시간 정보까지 포함하고 있다는 점을 다시 한번 확인할 수 있었습니다.

또한 데이터 타입을 정확하게 이해하지 못하면 단순한 조회 기능에서도 오류가 발생할 수 있다는 점을 경험하였습니다.

---

## 정리

### 오류 원인

* DATE 타입과 문자열 직접 비교

### 해결 방법

* TO_DATE 함수 사용
* 필요 시 TRUNC 함수 활용

### 핵심 포인트

* Oracle DATE 타입은 시간 정보 포함
* 데이터 타입을 고려한 SQL 작성 필요
* 날짜 비교 시 형식 일치 여부 확인
