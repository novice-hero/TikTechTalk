# 요약

- this는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수다.
- this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조할수 있다.
- 자바스크립트의 this는 함수가 호출되는 방식에 따라 this에 바인딩될 값, 즉 this 바인딩이 동적으로 결정된다.
    - 바인딩이란 식별자와 값을 연결하는 과정을 의미한다. this바인딩은 this와 this가 가리킬 객체를 바인딩 하는것이다.
    (추가적으로 변수선언은 변수 이름과 확보된 메모리 공간의 주소를 바인딩 하는것이다.)

<br />

# 렉시컬 스코프와 this 바인딩의 차이

- 렉시컬 스코프 : 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프 결정
- this 바인딩 : 함수 호출 시점에 상위 스코프를 결정

<br />

# 함수 호출방식과 this바인딩

- 일반함수 호출
    - 일반함수로 호출하면 함수 내부의 this에는 전역 객체가 바인딩 된다.
    객체를 생성하지 않는 일반함수에서의 this는 의미가 없으므로 strict mode에서는 undefined가 바인딩 된다.
- 메서드 호출
    - 메서드 내부의 this는 프로퍼티로 메서드를 가리키고 있는 객체와는 관계가 없고 메서드를 호출한 객체에 바인딩 된다.
- 생성자 함수 호출
    - 생성자 함수 내부의 this에는 생성자 함수가 생성할 인스턴스가 바인딩 된다.
- Function.prototype.apply/call/bind 메서드에 의한 간접 호출
    - Funtion.prototype.apply/call/bind 메서드에 첫번째 인수로 전달한 객체가 바인딩 된다.
    - **apply와 call 메서드의 본질적인 기능은 함수를 호출하는 것이다**

<br />

# Function.prototype.apply/call/bind 메서드에 의한 간접호출

<img width="701" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/bc672e87-efaf-45b3-8dc1-7634fd658bc0">

apply, call, bind 메서드는 Function.prototype의 메서드이므로 모든 함수가 상속받아서 사용할수 있다.

<br />

### apply, call 메서드

```jsx
function getThisBinding() {
	console.log(arguments);
	return this;
}

// this로 사용할 객체
const thisArg = { a : 1};

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, [1, 2, 3]));
```
<img width="770" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/f0b7b186-9d2f-4e63-ab3f-cb2c255379a9">

이처럼 apply와 call 메서드는 호출할 함수에 인수를 전달하는 방식만 다를 뿐 this로 사용할 객체를 전달하면서 함수를 호출하는 것은 동일하다.

<br />

### apply와 call 메서드의 대표적인 용도

위의 예시코드의 arguments 객체와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우.

arguments 객체는 배열이 아니기 때문에 Array.prototype.slice 같은 배열의 메서드를 사용할수 없으나 apply와 call 메서드를 이용하면 가능하다.

```jsx
function converArgsToArray() {
	console.log(arguments);

	// arguments 객체를 배열로 변환
	// Array.prototype.slice를 인수 없이 호출하면 배열의 복사본을 생성한다.
	const arr = Array.prototype.slice.call(arguments);
	// const arr = Array.prototype.slice.apply(arguments);
	console.log(arr)

	return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

<br />

### bind 메서드

Function.prototype.bind 메서드는 apply와 call 메서드와 달리 함수를 호출하지 않는다. 첫번째 인수로 전달한 값으로 this 바인딩이 교체된 함수를 새롭게 생성해 반환한다.

```jsx
function getThisBinding() {
	return this;
}

// this로 사용할 객체
const thisArg = { a : 1 };

// bind 메서드는 첫번째 인수로 전달한 thisArg로 this 바인딩이 교체된 getThisBinding 함수를 새롭게 생성해 반환한다.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
console.log(getThisBinding.bind(thisArg())); // { a : 1 }
```

<br />

### bind 메서드의 용도

bind 메서드는 메서드의 this와 메서드 내부의 중첩 함수 또는 콜백 함수의 this가 불일치하는 문제를 해결하기 위해 유용하게 사용된다.

```jsx
const person = {
	name : 'Lee'
	foo(callback) {
		// 1
		setTimeout(callback, 100)
};

person.foo(function () {
	console.log(`Hi! my name is ${this.name}.`); // 2
	// 일반 함수로 호출된 콜백 함수 내부의 this.name은 브라우저의 window.name과 같다.
});
```

person.foo의 콜백함수가 호출되기 이전인 1번의 시점에서 this는 foo메서드를 호출한 객체, 즉 person 객체를 가리킨다.

그러나, person.foo의 콜백함수가 일반 함수로서 호출된 2번의 시점에서 this는 전역 객체 window를 가리킨다. 서로 가리키는게 달라지므로 문맥상에서 문제가 발생한다.

<br />

따라서 콜백 함수 내부의 this를 외부 함수 내부의 this와 일치시켜야 한다.

이때 bind 메서드를 사용하여 this를 일치시킬 수 있다.

```jsx
const person = {
	name : 'Lee'
	foo(callback) {
		// bind 메서드로 callback 함수 내부의 this 바인딩을 전달
		setTimeout(callback.bind(this), 100)
};

person.foo(function () {
	console.log(`Hi! my name is ${this.name}.`);
});
```

<br />

## 참고

모던자바스크립트 딥다이브 this p_342 ~ 358
