# 요약

- 프로토타입은 객체지향 프로그래밍의 근간을 이루는 객체 간 상속을 구현하기 위해 사용되는 기능이다.
    - 어떤 객체의 상위 객체의 역할을 하는 객체로서 다른 객체에 공유 프로퍼티를 제공
    - 프로토타입을 상속받은 하위 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 사용가능
- 모든 객체는 하나의 프로토타입을 갖는다.
- 모든 프로토타입은 생성자 함수와 연결되어 있다.
- JS에서 기존 데이터 타입을 제외한 모든것이 객체, 객체가 만들어지기 위해서는 자신을 만드는데 사용된 원형인 프로토타입 객체를 이용하여 객체를 만듭니다.

<br />

<img width="708" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/1019ac86-cabb-47c2-ab22-e6750ed3a79f">


<br />

## 함수와 객체의 내부 구조

JS에서는 함수를 정의하고 파싱단계에 들어가면 함수 멤버로 prototype 속성이 생기게 되는데 다른곳에 생성된 함수이름의 프로토타입 객체를 참조하게 된다.

프로토타입 객체의 멤버인 constructor 속성은 함수를 참조하는 내부구조를 가짐.

<img width="750" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/9a550972-c911-41f2-91da-9478da330bd5">


```jsx
function Person(){}
```

속성이 하나도 없는 Person이라는 함수가 정의되고, 파싱단계에 들어가면 Person 함수 Prototype 속성은 프로토타입 객체를 참조

프로토타입 객체 멤버인 constructor 속성은 Person 함수를 참조하는 구조를 가짐

→ Person 함수의 prototype 속성이 참조하는 프로토타입 객체는 new라는 연산자와 Person함수를 통해 생성된 모든 객체의 원형이 되는 객체

<img width="773" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/eecad87e-dadf-4de7-9ba6-b3e92c54135f">

```jsx
function Person() {}

var joon = new Person();
var jisoo = new Person();
```

사용자가 정의한 함수도 객체, new라는 연산자를 통해 생성된것도 객체.

객체 안에는 proto 속성이 있고 객체가 만들어지기 위해 사용된 원형인 프로토타입 객체를 숨은 링크로 참조하는 역할을 함.

<br />

## 프로토타입 객체

함수를 정의하면 다른 곳에 생성되는 프로토타입 객체는 자신이 다른 객체의 원형이 되는 객체 모든 객체는 프로토타입 객체에 접근 가능

<img width="755" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/5876f5a1-ae28-4152-a74d-15fc5ccdaf2a">

```jsx
function Person(){}

var joon = new Person();
var jisoo = new Person();

Person.prototype.getType = function (){
	return '인간';
};

console.log(joon.getType()) // 인간
console.log(jisoo.getType()) // 인간

joon.getType = function (){
	return '사람';
};

console.log(joon.getType()); //사람
console.log(jisoo.getType()) // 인간
```

prototype 속성을 이용하여 멤버를 추가

같은 프로토타입을 이용하여 생성된 joon과 jisoo 객체는 getType()을 사용가능

→ 프로토타입 객체에 멤버를 추가, 수정, 삭제할 때는 함수안의 prototype 속성을 사용해야 함

joon 객체를 이용하여 getType() 리턴 값을 사람으로 수정

생성된 객체를 이용하여 프로토타입 객체의 멤버를 수정하면 프로토타입 객체에 있는 멤버를 수정하는 것이 아니라 자신의 객체에 멤버를 추가하는것.

→ joon 객체를 사용하여 getType()을 호출하면 프로토타입 객체의 getType()을 호출한 것이 아님. joon 객체에 추가된 getType()을 호출한것.

<br />

## 프로토타입

JS에서 기본 데이터 타입을 제외한 모든것이 객체

객체가 만들어지기 위해서는 자신을 만드는 데 사용된 원형인 프로토타입 객체를 이용하여 객체를 만듬.

이때 만들어진 객체 안에 `__proto__` 속성이 자신을 만들어낸 원형을 의미하는 프로토타입 객체를 참조하는 숨겨진 링크가 생김. 

이 숨겨진 링크를 프로토타입이라고 정의함.

<img width="734" alt="image" src="https://github.com/novice-hero/TikTechTalk/assets/88226519/90e6e186-adc5-4f45-8dfe-c952241edd59">

```jsx
function Person(){}

var joon = new Person();
```

joon 객체의 멤버인 `__proto__` 속성이 프로토타입 객체를 가리키는 숨은 링크가 프로토타입이라고 함.

→ JS에는 숨겨진 링크가 있어 프로토타입 객체 멤버에 접근 가능

프로토타입 링크를 사용자가 정의한 객체에 링크가 참조되도록 설정하면
코드의 재사용과 객체 지향적인 프로그래밍 가능
