> 이미지 url 참조가 제대로 되지 않아서, 전체 본문은 [블로그 포스트](https://jaygo-dev.tistory.com/3)에서 확인하실 수 있습니다.

---

# 원시 타입의 메서드(Method) 사용
## **원시 타입과 메서드**

JavaScript의 데이터 타입은 원시 타입과 참조 타입, 크게 두 가지로 나뉜다. 그 중 원시 타입에는 문자열(`String`), 숫자(`Number`), 불리언(`Boolean`; 논리 연산의 결과를 True 또는 False로 표현), `Undefined`(값이 할당되지 않은 변수에 엔진이 자동으로 부여하는 값), `Null`(명시적으로 값이 없다는 것을 나타냄), 심볼(`Symbol`; ES6에서 추가된 타입으로, 보통 객체의 프로퍼티 키를 고유하게 생성하기 위해 사용)이 있다. JavaScript에서 원시 타입을 제외한 나머지 모든 데이터는 객체(`Object`)다([원시 타입과 참조 타입](https://jaygo-dev.tistory.com/2) 참고).

다시 말해, 원시 타입은 객체가 아니다(개념 상 구분된다). 하지만, 일부 원시 타입에는 사용 가능한 메서드가 있다. 예를 들어, 문자열 타입의 데이터에는 길이를 확인하기 위해 `length` 프로퍼티, 대문자 변환을 위한 `toUpperCase`, 배열 인덱싱을 위한 `slice` 등의 활용이 가능하다.

```
let myStr = 'Hello';

console.log(myStr.length); // 5
console.log(myStr.toUpperCase) // 'HELLO'
console.log(myStr.slice(0, 4)); // 'Hell'
```

숫자와 불리언 데이터 타입의 값도 `.toString`을 사용하면 할당된 값이 문자열로 변환된다. 이때 사용한 `.toString`도 객체에 선언된 메서드다.

```
let myNum = 10;
let myBool = true;

console.log(myNum.toString()); // '10'
console.log(typeof myNum.toString()); // string

console.log(myBool.toString()); // 'true'
console.log(typeof myBool.toString()); // string
```

메서드는 객체 안에서 선언된 프로퍼티에 할당된 함수를 의미한다. 그렇기 때문에, 객체가 아닌 원시 타입 데이터들에서 메서드를 사용할 수 있는 건 이상한 일이다(객체가 아닌데 왜 사용 가능한 메서드가 있는 거지?). 알아본 결과, 원시 타입 데이터에 메서드를 쓰면 JavaScript 엔진이 원시 타입 데이터가 메서드를 사용할 수 있도록 하기 위한 특별한 절차를 수행한다.

## **원시 타입에서 메서드 사용이 가능한 이유**

참조 타입인 객체는 보다 많은 정보를 담을 수 있기 때문에 다른 원시 타입 데이터보다 무겁다(메모리 리소스를 좀 더 많이 잡아먹는다). 그래서 JavaScript의 성능을 위해 자주 사용되는 원시 타입 데이터는 객체가 아닌 자료형으로 만들어졌다(JavaScript의 태생적인 특징 중 하나다). 하지만, 메서드를 사용하면 보다 데이터를 효과적으로 다룰 수 있고, 객체 형태가 아닌 원시 타입도 보다 효과적으로 다루기 위해 일부 객체의 메서드를 사용하도록 하는 게 유리했다. 무겁지 않은 데이터 타입을 정의하는 것과, 개발의 편의를 위해 일부 메서드를 사용할 수 있도록 하는 것. 이러한 두 마리 토끼를 모두 잡기 위해 탄생한 개념이 '래퍼 객체(Wrapper Object)'이다.

원시 타입 데이터는 평상시엔 객체가 아닌 고유한 데이터 타입으로 관리된다. 그러다 해당 원시 타입 데이터가 참조 가능한 특정 메서드를 호출하면 JavaScript 엔진 내에서 '래퍼 객체(Wrapper Object)'가 생성된다. 래퍼 객체는 쉽게 얘기하면 '원시 타입을 감싸는(Wrapping) 객체'다(래퍼 객체가 원시 타입 데이터를 감싸도록 엔진에서 자동에서 처리해 주는 과정을 '오토 박싱(Auto Boxing)'이라고 한다). 원시 타입인 데이터는 감싸진 래퍼 객체의 내부 슬롯(문자열은 \[\[StringData\]\], 숫자는 \[\[NumberData\]\])에 등록되고, 등록 이후엔 래퍼 객체에 선언되어 있는 메서드를 프로퍼티 이름으로 접근해 활용할 수 있다. 그리고, 생성됐던 래퍼 객체는 메서드 사용이 끝나면 자동으로 Garbage Collector에 의해 메모리에서 해제된다.

해당 작업을 JavaScript 엔진이 내부 동작으로 자동 처리해주기 때문에, 원시 타입을 감싸는 래퍼 객체에 프로그래머가 직접 접근할 순 없다. 하지만, 래퍼 객체는 각 원시 타입의 프로토타입의 상속받기 때문에, 원시 타입의 프로토타입 속성 목록을 보면 사용 가능한 메서드들을 확인할 수 있다. 아래는 숫자 타입 데이터의 프로토타입인 `Number.prototype`의 속성들이다.

[##_Image|kage@lsWiF/btsJYD1EDop/o1udMkKk4o2L5XLUgBQBN1/img.png|CDM|1.3|{"originWidth":2032,"originHeight":1006,"style":"alignCenter"}_##]

`Number.prototype`은 `Object.prototype`을 상속하면서, 고유한 일부 메서드들이 추가로 존재한다. 숫자 타입의 데이터를 실행해서 정의된 메서드를 활용하면 `Number`의 프로토타입을 상속받는 래퍼 객체가 원시 타입을 감싸고, 필요한 메서드를 사용할 수 있게 해준다. 그리고, 메서드 사용이 완료되면 래퍼 객체는 Garbage Collector에 의해 메모리에서 해제된다.

원시 타입 중 문자열(`String`), 숫자(`Number`), 불리언(`Boolean`), 심볼(`Symbol`)은 각각의 프로토타입 객체가 존재하며, 메서드를 사용하면 원시 타입별로 각자의 프로토타입 객체를 상속받는 래퍼 객체가 생성되어 원시 타입 데이터를 감싼다. 참고로, 원시 타입 중 `null`과 `undefined`는 프로토타입 객체가 없으며, 실행 과정 중 래퍼 객체 생성도 되지 않고, 메서드 사용도 불가하다.

## **문자열 메서드와 배열 메서드**

아래는 원시 타입 중 문자열 데이터의 프로토타입 객체 속성이다. 한눈에 보기에도 다른 원시 타입 데이터의 프로토타입 객체 속성 대비 굉장히 많은 속성을 가지고 있는 것 같다. 물론 문자열에만 고유하게 적용되는 속성들도 많이 있지만, 문자열이 유사 배열이라는 특징 때문에 배열에 적용되는 속성들이 다수 포함되어 있다는 특징이 있다.

[##_Image|kage@BwZTU/btsJX9NeP0C/3XWKOPNHtmmuLgyiKcKS6k/img.png|CDM|1.3|{"originWidth":2324,"originHeight":1630,"style":"alignCenter"}_##]

유사 배열이란, 배열은 아니지만 배열과 유사한 특징을 가진 데이터들을 의미한다(이름이 상당히 직관적이다). 유사 배열이 되기 위해선 아래 두 가지 조건을 충족해야 한다.

-   길이를 확인하기 위한 프로퍼티인 `length`를 가진다.
-   인덱싱이 가능해야 하고, 인덱스는 0번부터 순차적으로 올라간다.

`String.prototype`을 보면 `length` 속성이 있는 것이 확인된다. 아래와 같이, 문자열을 변수에 할당하고 `length` 프로퍼티로 접근해보면 문자열의 길이가 확인된다.

```
let myStr = 'Hello';
myStr.length; // 5
```

또한, 문자열은 인덱스를 활용해 n번째 문자에 접근하는 게 가능하다. 인덱스만을 활용한 문자열 Slicing은 어렵지만, `String` 프로토타입 객체 메서드인 `slice`를 활용하면 문자열을 나누는 것도 가능하기 때문에, 유사 배열로의 조건을 충분히 갖추고 있다.

```
let myStr = 'Hello';

console.log(myStr[2]) // 'l'
console.log(myStr.slice(0, 4));  // 'Hell'
```

실제로 `String` 프로토타입과 `Array` 프로토타입의 속성을 비교해 보면, 총 8개의 프로퍼티가 양쪽 모두 동일하게 있는 것으로 확인된다. 그리고, 리턴 값이 문자열과 배열로 일부 차이가 있긴 하지만, 메서드가 동작하는 방식이 거의(또는 완전히) 유사한 것이 확인된다.

> at: 특정 인덱스에 있는 값을 확인

```
let myStr = 'Hello Everyone';
let myArr = ['H', 'e', 'l', 'l', 'o', ' ', 'E', 'v', 'e', 'r', 'y', 'o', 'n', 'e'];

consoloe.log(myStr.at(8), myArr.at(8)) // 'e' 'e'
```

> concat: 매개 변수로 전달된 값을 기존 값 뒤에 연결

```
let myStr1 = 'Hello ';
let myStr2 = 'Everyone';

let myArr1 = ['H', 'e', 'l', 'l', 'o'];
let myArr2 = ['E', 'v', 'e', 'r', 'y', 'o', 'n', 'e'];

concatStr = myStr1.concat('', myStr2);
concatArr = myArr1.concat('', myArr2);

console.log(concatStr, concatArr); // 'Hello Everyone' ['H', 'e', 'l', 'l', 'o', '', 'E', 'v', 'e', 'r', 'y', 'o', 'n', 'e']
```

> includes: 문자열에선 특정 문자(열), 배열에선 특정 요소를 포함하고 있는지 확인

```
let myStr = 'Hello Everyone';
let myArr = ['Hello', 'Every', 'One'];

console.log(myStr.includes('Every')); // true
console.log(myStr.includes('Every')); // true
```

> indexOf: 문자열에선 특정 문자(열), 배열에선 특정 요소가 있는 인덱스 위치를 확인(중복 시 가장 첫 인덱스를 리턴)

```
let myStr = 'Hello';
let myArr = ['H', 'e', 'l', 'l', 'o'];

console.log(myStr.indexOf('l')); // 2
console.log(myArr.indexOf('l')); // 2
```

> lastIndexOf: 문자열에선 특정 문자(열), 배열에선 특정 요소가 있는 마지막 인덱스 위치를 확인(중복 시 가장 마지막 인덱스를 리턴)

```
let myStr = 'Hello';
let myArr = ['H', 'e', 'l', 'l', 'o'];

console.log(myStr.lastIndexOf('l')); // 3
console.log(myArr.lastIndexOf('l')); // 3
```

> length: 문자열, 또는 배열의 길이(메서드가 아닌, 일반 프로퍼티와 값으로 저장됨)

```
let myStr = 'Hello';
let myArr = ['H', 'e', 'l', 'l', 'o'];

console.log(myStr.length); // 5
console.log(myArr.length); // 5
```

> slice: 문자열, 또는 배열을 원하는 구간에서 자르기

```
let myStr = 'Hello';
let myArr = ['H', 'e', 'l', 'l', 'o'];

console.log(myStr.slice(0, 4)); // 'Hell'
console.log(myArr.slice(0, 4)); // ['H', 'e', 'l', 'l']
```

> toString: 값의 타입을 문자열로 변환, 배열의 경우 요소 사이에 , 가 들어가도록 형 변환

```
let myStr = 'Hello';
let myArr = ['H', 'e', 'l', 'l', 'o'];

console.log(myStr.toString()); // Hello (원래 문자열 값을 그대로 반환)
console.log(myArr.toString()); // H,e,l,l,o
```

## **결론**

원시 타입에도 필수적인 메소드를 사용 가능하게 엔진에서 자동으로 처리해 준다니, 참 감사한 일이다. JavaScript를 만든 브레던 아이크 님에게 감사하는 마음으로 잘 사용해야겠다.
