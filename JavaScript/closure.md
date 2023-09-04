### 요약

클로저란 함수가 선언된 렉시컬 환경과 함수의 조합이며 함수가 선언된 환경의 변수를 기억하고 사용할 수 있다.

## 렉시컬 환경

특정 코드가 작성, 선언된 환경

## 렉시컬 스코프

중첩된 함수 그룹에서 내부 함수가 상위 범위의 변수 및 리소스에 접근 할 수 있음을 나타내는 범위 규칙

- 정적 스코프라고도 불림
- **함수 정의가 평가되는 시점에 함수가 정의된 환경(= 렉시컬 환경)에 의해 결정됨**
- 함수 내부에서 변수를 접근할 때, 접근을 시도한 함수 내에서 변수를 찾아본다.
  - 변수가 존재하지 않다면 렉시컬 스코프에 의해 렉시컬 환경인 상위 함수에서 다시 변수를 탐색한다.

# 클로저

> 함수와 그 함수가 선언된 렉시컬 환경과의 조합

- 함수가 선언된 **환경 외부에서 실행**되더라도, 함수가 선언된 **환경의 변수를 기억하고 사용**할 수 있다.
- 클로저가 생성된 시점의 유효 범위 내에 있는 모든 지역 변수들로 구성된다.
- 함수는 선언문이 존재하는 곳의 렉시컬 환경에 함수 객체로 저장된다.
- 함수 객체에 [[Environment]] 내부 슬롯에는 실행중인 실행 컨텍스트의 **렉시컬 환경(상위 스코프의 참조)**이 들어가며, 이후 함수가 호출될 때 함수 실행 컨텍스트의 렉시컬 환경에서 **“외부 렉시컬 환경에 대한 참조”**에 [[Environment]] 내부 슬롯의 값이 할당된다.

## 클로저 특징

1. 함수 내부에 중첩된 함수가 있을 때 발생
2. 중첩된 함수는 외부 함수의 변수에 접근할 수 있음(렉시컬 스코프)
3. **외부 함수가 종료**된 후에도 **중첩된 함수**가 **외부 함수의 변수에 접근**할 수 있음
4. JavaScript의 모든 함수는 기본적으로 클로저의 특징을 띈다.
   - 상위 스코프(렉시컬 환경)을 저장하고 접근할 수 있기 때문이다.
   - 그러나 일반적으로는 **“중첩 함수가 상위 스코프의 식별자(자유 변수)를 참조하고 있고 중첩 함수가 외부 함수보다 더 오래 유지되는 경우”** 를 클로저라고 부른다.

## 클로저와 렉시컬 환경 예시

```jsx
const x = 1;

function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  };
  return inner;
}

const innerFunc = outer();
innerFunc();
```

1. const innerFunc = outer(); 에서 innerFunc 변수에 함수 객체 할당
2. outer()가 할당됨과 동시에 실행되어 내부 함수인 inner()가 반환
3. inner() 함수의 [[Environment]] 내부 슬롯에 렉시컬 환경인 outer()가 할당
4. innerFunc() 함수 실행 시, inner() 함수가 동작되며 console.log(x); 를 통해 변수 x를 렉시컬 환경에서 탐색
5. 10 출력
6. 즉, outer()가 동작한 후 실행 컨텍스트에서는 pop 되었으나 innerFunc()에 할당된 inner() 함수의 [[Environment]] 내부 슬롯에 할당되어 있어 가비지 컬렉터에 의해 렉시컬 환경이 삭제되지 않는다.

### 클로저 활용

```jsx
const calculator = () => {
  let result = 0;

  const add = (x, y) => {
    result = x + y;
    return result;
  };

  const subtract = (x, y) => {
    result = x - y;
    return result;
  };

  const getResult = () => result;

  return {add, subtract, getResult};
};

const calc = calculator();

console.log(calc.add(5, 6)); // 11
console.log(calc.subtract(9, 2)); // 7
console.log(calc.getResult()); // 7
console.log(calc.result); // undefined
```

1. 모듈 패턴화
   - 클로저를 이용해 접근이 가능한 리소스와 접근이 불가능한 리소스를 구분하여 접근을 제한할 수 있다.
   - calculator 함수에서 getResult로 마지막으로 계산된 값은 접근이 가능하지만, 변수 result는 접근할 수 없다.

```jsx
const add = (x) => {
  return (y) => {
    return (z) => {
      return x + y + z;
    };
  };
};

const num1 = add(1);
const num2 = num1(2);
const num3 = num2(3);

console.log(num3); // 6

const new_num3 = num2(6);

console.log(new_num3); // 9
```

1. 커링
   - 함수를 호출할 때 인수의 일부만으로 새로운 함수를 반환하고 이를 차례대로 적용하는 기법
   - 클로저를 활용해 전달받은 인수들을 저장하고, 모든 인수를 전달받으면 최종 결과를 반환한다.
   - 인수를 입력 받은 함수들은 값을 저장하고 있기 때문에, 중간 함수에 새로운 값을 전달하여 최종 결과를 수정할 수 있다.
2. 메모이제이션
   - 클로저는 함수 실행 결과를 자유 변수에 저장하고 동일한 계산이 요청되었을 때, 이전 결과를 반환할 수 있다.
   - 반복된 계산을 피하여 성능을 향상시킬 수 있다.
