## 타입 변환과 단축 평가

#### 타입 변환
- **명시적 타입 변환** 또는 **타입 캐스팅**: 개발자가 의도적으로 값의 타입을 변환
- **암묵적 타입 변환** 또는 **타입 강제 변환**: 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환

#### 암묵적 타입 변환

```
'10' + 2    // '102'

5 * '10'    // 50

!0  // true
if(1) { }
```

- 문자열 타입 변환: 피연산자 중 하나 이상이 문자열일 때
- 숫자 타입 변환: -, *, / 등 산술 연산자 사용됐을 때
- 불리언 타입 변환: Truthy 값 또는 Falsy 값으로 구분

#### 명시적 타입 변환

- 표준 빌트인 생성자 함수(Number, Boolean)를 new 연산자 없이 호출 또는 빌트인 메서드 사용 또는 앞서 살펴본 암묵적 타입 변환 이용

1. 문자열 타입으로 변환
```
String(1);  // new 연산자 없이 호출

(1).toString(); // Object.prototype.toString 메서드 사용

1 + ' ';    // 문자열 연결 연산자

```

2. 숫자 타입으로 변환
```
Number('0');

parseInt('0');

+'0';

'0' * 1;
```

3. 불리언 타입으로 변환
```
Boolean('x');   // true
Boolean('');    // false

Boolean(0);     // false
Boolean(1);     // true

// 이외에도 NaN, null, undefined는 false
```

#### 단축 평가

1. 논리 연산자를 사용한 단축 평가
```
// 논리합(||) 연산자
false || 'Dog'; // 'Dog'
// 논리곱(&&) 연산자
false && 'Dog'  // false
```

- 객체가 가리키기를 기대하는 변수가 null 또는 undefined가 아닌지 확인하고 프로퍼티를 참조할 때 유용

```
var elem = null;
var value = elem.value; // TypeError: Cannot read property 'value' of null
```
이 때 단축 평가 사용 시 에러 발생하지 않음
```
var elem = null;

// elem이 null이나 undefined와 같은 Falsy 값이면 elem으로 평가되고
// elem이 Truthy 값이면 elem.value로 평가된다.
var value = elem && elem.value;
```

- 함수 매개변수에 기본값을 설정할 때

```
function getStringLength(str) {
    str = str || '';
    return str.length;
}
// ES6의 매개변수 기본 값 설정
function getStringLength(str = '') {
    return str.length;
}
```

2. 옵셔널 체이닝 연산자
- ES11에서 도입된 옵셔널 체이닝 연산자 ?.는 좌항의 피연산자가 null 또는 undefined인 경우 undefined를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.

```
var elem = null;

// elem이 null 또는 undefined이면 undefined를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.
var value = elem?.value;
console.log(value); // undefined
```

3. null 병합 연산자
- ES11에서 도입된 null 병합 연산자 ??는 좌항의 피연산자가 null 또는 undefined인 경우 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다. null 병합 연산자 ??는 변수에 기본값을 설정할 때 유용

```
var foo = null ?? 'default string';
console.log(foo);
```
논리 연산자 ||를 사용한 단축 평가의 경우 만약 Falsy 값인 0이나 ''도 기본값으로서 유효하다면 예기치 않은 동작이 발생할 수 있다.

```
var foo = '' || 'default string';
console.log(foo);   // 'default string'
```

하지만 null 병합 연산자 ??는 좌항의 피연산자가 false로 평가되는 Falsy 값이라도 null 또는 undefined가 아니면 좌항의 피연산자를 그대로 반환한다.

```
var foo = '' ?? 'default string';
console.log(foo)    // ''
```
