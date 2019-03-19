---
title: 자바스크립트의 특징인 이벤트 루프 이해하기
intro: 단일 스레드인 자바스크립트가 동시성을 지원하는 방법
date: 2019-03-17 21:25:48
tags: [js, javascript, eventloop, singlethread, settimeout, promise]
category: javascript
---
자바스크립트(Javascript)의 특징 중 하나는 '단일 쓰레드(Single Thread)'라는 것입니다.

# 단일 쓰레드의 단점

'단일 스레드'는 동시에 하나의 작업만 처리가 가능합니다. '콜 스택(Call Stack)'에서 작업이 실행되고 있는 상태를 '블록킹(Blocking)' 상태라고 하는데, 이 상태에선 브라우저가 렌더링할 수 없을 뿐 더러 다른 코드를 수행할 수 없습니다.

해당 문제가 과하게 노출이 된다면 사용자가 느리다고 느끼거나, 브라우저가 뻗어버리는 문제가 있습니다.

# 자바스크립트가 동시성을 지원하는 방법

동시에 여러 작업을 수행하기 위해 '큐(Queue)'와 '이벤트 루프(Event Loop)'라는 개념이 등장합니다.

## 큐

실행 대기 중인 함수가 모여있는 공간입니다. 모든 비동기 API들은 작업이 끝나면 이곳에 콜백 함수를 추가해둡니다.

## 이벤트 루프

'틱(Tick)'이라는 단위로 단순 반복하며 큐와 콜 스택을 확인합니다. 콜스택이 비어 있으면 우선 순위가 높은 큐 하나를 콜 스택으로 옮겨주는 역할을 합니다.

```js
while (queue.waitForMessage()) {
  queue.processNextMessage();
}
```

위 코드처럼 작동을 한다고 보시면 됩니다.

이벤트 루프는 자바스크립트 엔진이 아닌 Web APIs의 영역에 정의되어 있습니다. 개발자가 접근할 수 없는 영역이며 오로지 호출(DOM, AJAX, setTimeout 등등)만 가능합니다.

# setTimeout은 정확하지 않다

setTimeout은 여러 과정을 동해 수행되기 때문에 지정한 시간에 수행될 것이라는 보장이 없습니다.

```js
setTimeout(() => { console.log('Hello, World!'); }, 5000);
```

이 간단한 코드가 Hello, World!를 출력하기 까지 어떤 과정을 거칠까요? 5000ms 후에 Hello, World!라는 로그를 찍는 코드를 연구해봅시다.

1. setTimeout 함수가 콜 스택에 등록됩니다.
2. Web APIs에서 setTimeout의 콜백 함수를 가지는 타이머를 생성합니다.
3. 콜 스택에서 setTimeout 함수가 제거됩니다.
4. 5000ms가 지나면 큐에 setTimeout의 콜백 함수를 넣고 타이머를 종료합니다.
5. 이벤트 루프가 콜스택이 비워져있는지 계속해서 확인하고, setTimeout의 콜백 함수를 콜 스택으로 옮깁니다.

이런 긴 과정을 거칩니다. 5000ms를 넘어서 수행될 수 있기 때문에 시간을 보장할 수 없다는 것이고, 5번의 과정의 경우 콜스택은 하나의 작업밖에 수행할 수 없기 때문에 수행 중인 콜스택이 있는 경우 해당 콜스택이 끝날 때 까지 기다려야 합니다.

## 이해를 돕는 예제

```js
(() => { console.log(1); })()
setTimeout(() => { console.log(2); }, 0);
(() => { console.log(3); })()
setTimeout(() => { console.log(4); }, 0);
```

setTimeout에 시간 설정을 0으로 해뒀습니다만, 일반 함수는 콜스택에서 순서대로 처리한다는 것을 알고, setTimeout은 다소 복잡한 로직을 거쳐서 결과가 나온다는 사실을 압니다.

여기까지 잘 이해하셨다면 결과가 아래와 같을 것을 예상할 수 있습니다.

```console
1
3
2
4
```

## setImmediate

setTimeout은 최소 지연 단위를 거쳐서 결과가 도출된는 문제가 있습니다. 그것을 해결하기 위해 지연 단위를 거치지 않고 바로 콜 스택에 콜백을 추가하는 setImmediate라는 API가 제안됩니다. 하지만 표준이 되지 못해서 IE, Node.js 0.10+ 에서만 동작합니다.

# 마이크로 태스크

이벤트 루프의 큐에도 우선순위가 존재합니다. 마이크로 태스크(Micro Task)라는 개념이 존재하는데, 마이크로 태스크 큐는 일반 큐보다 높은 우선순위를 지녀서 먼저 불립니다.

## 프라미스

프라미스(Promise)는 마이크로 태스크에 추가됩니다.

```js
setTimeout(() => { console.log(1); }, 0);
Promise.resolve()
  .then(() => { console.log(2); })
  .then(() => { console.log(3); })
```

Promise에 then이 두 번 붙어있는 로직입니다. 이벤트 루프는 마이크로 태스크 큐를 먼저 가져오고, 마이크로 태스트가 빈 것을 확인하면 일반 태스크 큐를 콜 스택으로 옮기게 되어 있습니다.

그래서 아래와 같은 결과가 나옵니다.

```console
2
3
1
```

# rAF

마이크로 태스크가 끝나고 브라우저 렌더링과 동시에 불리는 리퀘스트 애니메이션 프레임(requestAnimationFrame)라는 큐가 존재합니다.

정말로 그런지 코드를 통해 알아봅시다.

```js
setTimeout(() => { console.log(1); }, 0);
requestAnimationFrame(() => {
  console.log(2);
});
Promise.resolve()
  .then(() => { console.log(3); })
  .then(() => { console.log(4); })
```

위 코드가 돌고, 아래의 결과가 나옵니다.

```console
3
4
2
1
```

예상과 잘 맞으셨다면 잘 배우셨다고 생각합니다.
