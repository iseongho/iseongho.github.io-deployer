---
title: Cron Expression - Cron 예약 표현식 배우기
intro: Cron job을 돌리기 위해 예약 표현식에 작성법에 대해 알아봅시다.
date: 2018-09-01 18:32:19
tags: [cron, expression, job, schedule]
category: expression
---
작업 스케줄러(Scheduler)를 구현하다 보면 Cron 표현식(Cron Expression)을 사용하는 경우가 있는데요 한번 알아봅시다.

# Cron 식

```none
cron(분 시 일 월 요일 연도)
```

총 6가지의 필드를 가지는 형태입니다. 각 필드 당 Space를 통해 구분하며, 모든 필드는 필수 필드이고 시간대는 UTC만 지원됩니다.

각 필드 당 무슨 값을 입력해야 하는지 아래의 표를 보고 알아봅시다.

| 필드 | 값 | 와일드카드 |
|:--------|:--------|:--------|
| 분 | 0~59 | , - * / |
| 시 | 0~23 | , - * / |
| 일 | 1~31 | , - * ? / L W |
| 월 | 1-12 또는 JAN-DEC | , - * / |
| 요일 | 1-7 또는 SUN-SAT | , - * ? / L # |
| 연도 | 1970~2199 | , - * / |

해당하는 필드에 알맞은 값, 와일드카드를 넣으면 됩니다만 아직 와일드카드가 뭔지 모르실 겁니다. 아래의 표를 보고 알아봅시다.

| 문자 | 정의 | 예 |
|:--------|:--------|:--------|
| / | 증분 지정 | [분] 필드에 0/15를 입력하면 15분마다 실행이 됩니다. |
| L | "마지막" 지정 | [일] 필드에서 월이 마지막 날을 지정합니다. [요일] 필드에서 주의 마지막 날(토요일)을 지정합니다. |
| W | 평일 지정 | 5/W 같이 날짜와 함께 사용될 때 해당 월의 다섯 번째 평일에 가장 가까운 날을 지정합니다. 다섯 번째 날이 토요일인 경우에는 금요일에 실행이 됩니다. 다섯 번째 날이 일요일인 경우에는 월요일에 실행이 됩니다. |
| # | 해당 월의 두 번째 또는 n번 째 날 지정 | 3#2를 입력하면 해당 월의 두 번째 화요일(화요일은 주 7일 중 세 번째 날)이 지정됩니다. |
| * | 모든 값 지정 | [일] 필드에서 사용할 경우, 해당 월의 모든 날이 지정됩니다. |
| ? | 지정된 값 없음 | 또 다른 지정 값과 함께 사용됩니다. 예를 들어 특정 날짜가 지정되면 해당 주에서 어떤 요일에 해당하는지 신경 쓸 필요가 없습니다. |
| - | 범위 지정 | 10-12은 10, 11 및 12를 의미 |
| , | 추가 값 지정 | SUN, MON, TUE은 일요일, 월요일 및 화요일을 의미 |
| / | 증분 지정 | 5/10은 5, 15, 25, 35 등을 의미 |

# Cron 예시

저의 경우 매일 한국 시각으로 9시와 16시에 예약을 걸어야 했습니다. 이걸 기준으로 배워봅시다.

## 매일 오전 9시와 오후 4시(한국 시간 UTC+9)에 호출

| 필드 | 값 | 이유 |
|:--------|:--------|:--------|
| 분 | 0 | 정각에 부릅니다. |
| 시 | 0,7 | 2가지의 시간에 호출하기에 쉼표(,) 와일드카드를 씁니다. 또한 UTC만 지원함으로 한국 시간에서 9시간을 빼줍니다. |
| 일 | * | 모든 요일에 부릅니다. |
| 월 | * | 모든 달에 부릅니다. |
| 요일 | ? | 요일은 상관이 없습니다. 별(\*)로 해도 된다고 생각하시겠지만 [일]과 [요일] 모두 별(\*)로 하면 에러가 납니다. |
| 연도 | * | 모든 년도에 부릅니다. |

```none
cron(0 0,7 * * ? *)
```

## 매일 오전 10시(UTC)에 호출

```none
cron(0 10 * * ? *)
```

## 매일 오후 12시 15분(UTC)에 호출

```none
cron(15 12 * * ? *)
```

## 매주 월요일부터 금요일까지 오후 6시(UTC)에 호출

```none
cron(0 18 ? * MON-FRI *)
```

## 매월 첫째 날 오전 8시(UTC)에 호출

```none
cron(0 8 1 * ? *)
```

## 월요일부터 금요일까지 10분마다 호출

```none
cron(0/10 * ? * MON-FRI *)
```

## 월요일부터 금요일까지 오전 8시부터 오후 5시 55분(UTC) 동안 5분마다 호출

```none
cron(0/5 8-17 ? * MON-FRI *)
```

## 매월 첫 번째 월요일 오전 9시(UTC)에 호출

```none
cron(0 9 ? * 2#1 *)
```

# 주의 사항

- 비율이 1분 이내인 cron 표현식은 지원되지 않습니다.
- [일] 또는 [요일] 값 중 하나는 반드시 물음표(?)여야 합니다.