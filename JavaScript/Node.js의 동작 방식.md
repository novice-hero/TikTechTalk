### 요약

Node.js는 V8 엔진을 기반으로 브라우저 외부에서 JavaScript를 실행하기 위한 비동기 이벤트 주도 JavaScript 런타임이다.

- 비동기는 논블로킹 I/O 방식으로 수행한다.
  - 논블로킹 I/O 방식은 OS 커널이나 libuv의 스레드 풀에서 I/O 블로킹에 관련된 작업을 처리하고 이벤트 루프를 통해 콜백 함수를 동작시키는 방식이다.
- 이벤트 주도는 이벤트가 발생할 때 미리 지정해둔 작업을 수행하는 방식을 말한다.

# Node.js

JavaScript를 브라우저 밖에서도 실행할 수 있도록 하는 JavaScript 런타임

비동기 이벤트 주도 JavaScript 런타임으로 확장성 있는 네트워크 애플리케이션을 제작할 수 있도록 설계되었다.

## Node.js의 특징

- Node.js는 싱글스레드 논블로킹 모델로 구성되어 있으며, 하나의 스레드로 동작하지만 비동기 I/O 작업을 통해 동시에 많은 요청들을 비동기로 수행함으로써 싱글 스레드임에도 논블로킹이 가능하다.
- 클러스터링을 통해 프로세스를 fork하여 멀티스레드 처럼 사용할 수 있다.
- Node.js는 JavaScript가 메인스레드 단일로만 실행되기 때문에 싱글스레드이지만, 일부 블로킹 작업들은 libuv의 스레드 풀에서 수행되기 때문에 완전한 싱글 스레드는 아니다.

## 이벤트 주도(Event-driven)

이벤트가 발생할 때 미리 지정해둔 작업을 수행하는 방식

Node.js는 이벤트 주도 방식을 사용하며 이벤트 리스너에 등록해둔 콜백 함수를 실행하는 방식으로 동작한다.

이벤트 루프를 통해 이벤트 주도 방식에서 이벤트에 따라 호출되는 콜백 함수를 관리한다.

## libuv

Node.js에서 비동기 I/O 작업을 지원하는 핵심 라이브러리이다.

이벤트 루프는 libuv에서 구현되며 논블로킹 비동기 I/O 작업의 스레드 풀을 관리한다.

## Node.js 내부 구조

![](https://velog.velcdn.com/images/player1552/post/2a03d0d5-3635-402c-bba5-6bcb1ad00d9f/image.png)

- Node.js는 내장 라이브러리, V8엔진, libuv로 구성되어 있다.
- Node.js는 C++과 JavaScript로 구현되어 있고 libuv는 100% C++로 구현되어 있다.
  그러나, V8엔진을 통해 JavaScript를 C++ 언어로 변환해주어 사용자가 C++을 몰라도 Node.js 사용이 가능하다.
- Node.js에서 동작하는 이벤트 루프는 libuv에서 구현된다.
  그러나, JavaScript의 스레드와 libuv의 이벤트 루프 스레드가 구분되어 있는 것은 아니다.
  Node.js는 싱글스레드이기 때문에 하나의 스레드에서 모든 처리가 진행된다.

## Node.js 이벤트 루프

![](https://velog.velcdn.com/images/player1552/post/7bdb4ee5-c212-48ed-8ba4-608d89249f8b/image.png)

- 각 단계별로 실행할 콜백의 FIFO Queue를 가진다.
- 해당 단계에서 한정된 작업을 수행하고 큐를 모두 소진하거나 콜백의 최대 개수를 실행할 때 까지 해당 단계의 큐에서 콜백을 실행한다.
- 큐를 모두 소진하거나 콜백 제한에 이르면 다음 단계로 넘어간다.
- 이러한 동작 방식을 라운드 로빈 방식이라 한다.
  각각의 단계들을 순차적으로 계속해서 순회하며 동작하는 방식이다.

### 1. timers

setTimeout(), setInterval()과 같은 timer 함수를 처리한다.

이벤트 루프가 timer 단계에 오면 처리할 수 있는 timer 함수들을 확인 후 콜백 함수를 실행한다.

timer 함수의 입력된 지연 시간은 콜백 함수가 실행되는 정확한 시간값이 아니며, 해당 지연 시간 이후에 실행된다는 의미의 시간값이다.

### 2. pending callbacks

이전 루프에서 동작했던 I/O 작업이 완료될 때 결과값이 해당 단계의 큐에 저장된다.

완료된 I/O 작업 블록의 내부에 있는 콜백 함수들을 poll 단계의 큐로 넘긴다.

TCP 오류와 같은 시스템 작업의 콜백 함수를 실행한다.

### 3. idle, prepare

libuv의 내부 작업을 위해 사용되며 일반적으로 개발자들은 이 단계와 상호작용 할 일이 없다.

idle: libuv가 사용하는 내부 작업을 처리하며 Node.js 개발에서는 거의 사용되지 않는다.

prepare: 다음 타이머의 실행 시간을 확인하고 설정하며 poll 단계를 위한 준비를 한다.

### 4. poll

I/O와 연관된 콜백을 실행한다.

timers 단계에서의 실행 시간 제어를 담당하며 poll 큐에 쌓인 콜백 함수들을 한도가 넘지 않을때까지 모두 동기적으로 실행한다.

만약 한도가 넘거나, 콜백 함수가 없을 때는 다음과 같은 규칙에 따라 동작한다.

- 다음 단계인 check 단계를 검사하여 setImmediate()가 있는지 확인
- setImmediate()가 존재한다면 check 단계로 이동
- 존재하지 않는다면 timers 단계에 실행할 함수가 있는지 확인
- timer 함수를 실행할 수 있는 시간까지 대기한 후, timers 단계로 이동
  이 과정에서 poll 단계의 큐에 콜백 함수가 생기면 즉시 실행

### 5. check

setImmediate() 콜백 함수가 실행된다.

### 6. close callbacks

close 이벤트에 따른 콜백 함수를 실행한다.

## 논블로킹 비동기 I/O

Input과 Output이 관련된 작업(http, CRUD) 등의 블로킹 작업들을 OS 커널 혹은 libuv의 스레드 풀에서 수행하고 이를 이벤트 루프의 pending callbacks에 전달하는 것을 의미

libuv는 작업 종류에 따라 OS 커널 혹은 스레드 풀로 전달한다.

작업의 결과를 이벤트 루프에게 알려주고 이를 pending callbacks의 큐에 등록한다.

libuv의 스레드 풀은 멀티스레드로 OS 커널이 지원하지 않는 작업들을 맡아서 처리한다.

### 마이크로테스크 큐

JavaScript의 Promise resolve() 혹은 reject()가 호출되거나 process.nextTick() 함수에 의해 예약된 콜백들을 담는 큐

이벤트 루프의 각 단계가 종료될 때마다 마이크로테스크 큐를 확인하여 콜백 함수가 존재한다면 해당 큐가 완전히 비워질때까지 다음 이벤트 루프 단계로 넘어가지 않는다.

Promise는 JavaScript의 내장 객체로 비동기 작업의 결과값을 나타내는데, .then이나 .catch에 등록된 콜백 함수들이 마이크로테스크 큐에 추가된다.

process.nextTick() 함수는 Node.js 환경에서 제공하는 전역 process 객체의 메소드로 콜백을 인자로 받아 현재 실행중인 작업이 완료된 후에 즉시 실행하도록 예약한다.
하지만 위의 기능은 nextTick()이 CPU 사이클을 모두 차지하여 다른 연산들을 대기하게 만드는 문제를 야기할 가능성이 있고 일반 개발자들은 직접 사용할 일이 거의 없다.

따라서 Node.js는 V8 엔진을 기반으로 브라우저 외부에서 JavaScript를 실행하기 위한 비동기 이벤트 주도 JavaScript 런타임이며 libuv의 이벤트 루프를 통해 이벤트 주도 처리를 담당하며 이러한 처리 큐들을 매크로 태스크 큐라고 부르고 비동기 I/O 처리는 libuv의 스레드 풀과 OS 커널을 통해 처리하고 Promise와 같은 JavaScript 비동기 작업의 결과 처리를 마이크로테스크 큐에서 담당하기 때문에 결론적으로는 두 개의 다른 작업 스케줄링 시스템을 이용하는 런타임이라고 할 수 있다.

### 출처

https://mniyunsu.github.io/node-loop/
[https://medium.com/@vdongbin/node-js-동작원리-single-thread-event-driven-non-blocking-i-o-event-loop-ce97e58a8e21](https://medium.com/@vdongbin/node-js-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC-single-thread-event-driven-non-blocking-i-o-event-loop-ce97e58a8e21)
