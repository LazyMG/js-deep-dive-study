# 16장. 프로퍼티와 어트리뷰트

-   프로퍼티엔 데이터 프로퍼티와 접근자 프로퍼티를 갖는다. 데이터 프로퍼티는 객체의 일반적인 프로퍼티로, 프로퍼티에 할당된 특정한 값을 갖는다.
-   접근자 프로퍼티는 값을 갖지 않는다. 대신, 객체 내부에 있는 다른 프로퍼티의 값을 읽거나 저장하는 목적을 가진 접근자 함수를 갖는다.
-   데이터 프로퍼티의 프로퍼티 어트리뷰트로(디스크립터 객체)는 `[[Value]]`, `[[Writable]]`, `[[Enumerable]]`, `[[Configurable]]`까지 총 네 개의 값을 가진다. 기본 설정은 전부 다 `ture`다(`[[Value]]`는 `true`는 아니지만 값을 갖는게 Truthy이기 때문에 `true`인 셈이다).
    -   `[[Value]]`: 프로퍼티를 통해 접근할 수 있는 값
    -   `[[Writable]]`: 프로퍼티 값의 변경 가능 여부, `false`일 경우 읽기 전용 프로퍼티가 됨
    -   `[[Enumerable]]`: 열거 가능 여부. 반복문 등을 통해 객체의 프로퍼티를 나열할 때(`key ... in ...`, `Object.keys`) 포함할지 여부
    -   `[[Configurable]]`: 재정의 가능 여부. `false`더라도 `Writable`이 `true`일 경우 값을 변경하거나 `Writable` 값을 `false`로 바꾸는 건 허용됨.
-   접근자 프로퍼티는 프로퍼티 어트리뷰트(디스크립터 객체)로 `[[Get]]`, `[[Set]]`, `[[Enumerable]]`, `[[Configurable]]`을 갖는다.
    -   `[[Get]]`: 데이터 프로퍼티를 조회(GET)하기 위한 접근자 함수(`getter` 함수)가 저장됨
    -   `[[Set]]`: 데이터 프로퍼티를 설정/수정(SET)하기 위한 접근자 함수(`setter` 함수)가 저장됨
    -   `[[Enumerable]]`: 데이터 프로퍼티의 `Enumerable`과 같음
    -   `[[Configurable]]`: 데이터 프로퍼티의 `Configurable`과 같음
-   데이터 프로퍼티와 접근자 프로퍼티의 어트리뷰트는
-   접근자 프로퍼티는 값을 갖지 않으며, 설정을 해두더라도 객체의 값을 찍어볼 때 확인되지 않는다. 단지, 객체 내부에 있는 접근자 프로퍼티를 조회 또는 수정하기 위한 기능적인 역할만 할 뿐이다.

```javascript
const person = {
    firstName: 'Jay',
    lastName: 'Ko',

    get fullName() {
        return `${this.firstName} ${this.lastName}`;
    },

    set fullName(name) {
        [this.firstName, this.lastName] = name.split('');
    },
};

console.log(person); // {firstName: 'Jay', lastName: 'Ko}
```

-   접근자 프로퍼티를 사용하면 객체의 값에 접근할 때 유효성 검증 등 추가적인 로직을 구현할 수 있다. 때문에, 객체의 값을 조금 더 예측 가능하고 안전하게 사용하기 위해 접근자 프로퍼티를 활용하는 게 좋다.

```javascript
let myAddress = {
    country: 'Korea',
    city: 'Seoul',

    get FullAddress() {
        return `${this.country}, ${this.city}`;
    },

    set FullAddress(address) {
        if (address.includes(' ')) {
            [this.country, this.city] = address.split(' ');
        } else {
            alert('country와 city 사이를 공백으로 구분하세요');
        }
    },
};

myAddress.FullAddress = 'FranceParis'; // alert 발생 및 setter 함수 실행 중지
```

# 17장. 생성자 함수에 의한 객체 생성

-   생성자 함수는 객체를 생성하는 하나의 방법이다. 생성자 함수를 사용하면 객체의 틀(프로토타입)을 사전에 정의해두고, 해당 틀에 기반하여 인스턴스를 생성하는 방식으로 객체를 만들어낼 수 있다(생성자 함수에 의해 만들어진 객체를 인스턴스라고 한다).
-   자바스크립트는 다양한 데이터를 정의하기 위한 빌트인 생성자 함수들을 제공한다(`Object`, `Number`, `String`, `Boolean`, `Function`, `Array`, `Date`, `RegExp`, `Promise` 등).
-   그 중 원시 타입을 생성하는 생성자 함수는 원시 타입의 메서드 사용 시 생성되는 래퍼 객체이다.
-   객체 리터럴 방식은 동일한 프로퍼티를 갖는 객체를 여러 개 만들 때 매번 값을 하나씩 입력해줘야 한다는 번거로움이 있다. 하지만, 생성자 함수로 객체의 원형(구조)을 미리 정의해두고, 해당 틀을 본떠서 인스턴스를 만들면 같은 프로퍼티를 갖는 객체들을 쉽게 여러 개 만들 수 있다.
-   생성자 함수를 생성자 함수로 호출하려면 앞에 `new` 키워드를 붙여야 한다. `new` 없이 생성자 함수를 호출하면 일반 함수로 동작하고, 리턴 값이 없으면 `undefined`가 된다.
-   생성자 함수는 인스턴스를 `retrun`하지 않는다. 생성자 함수에 의해 만들어지는 인스턴스는 함수 내부에서의 리턴 값이 아니라, `new` 키워드가 사용됐을 때 자바스크립트 엔진에서 런타임 이전에 암묵적으로 처리해주는 동작이다.
-   만약에 생성자 함수 내부에 `return`이 있다면 자바스크립트 엔진이 처리해주는 동작에 의해 만들어진 인스턴스가 아니라 리턴 값이 반환된다(함수로 동작한다).
-   함수는 객체이며, 객체가 갖는 내부 슬롯을 동일하게 갖는다(프로토타입 상속 때문일 듯). 거기에, 추가적으로 함수만의 고유한 일부 내부 슬롯을 갖는다. 그 중에 `[[Call]]`과 `[[Constructor]]`가 함수가 일반 함수로 동작할지, 아니면 생성자 함수로 동작할지의 역할을 구분해준다.
-   함수가 `new` 연산자 없이 단독으로 호출되면 내부 슬롯 중 `[[Call]]`이 호출된다. 즉, `[[Call]]`에는 일반 함수로의 기능이 바인딩되어있는 것이다.
-   반면, `new` 연산자와 함께 호출된 함수는 내부 슬롯 중 `[[Constructor]]` 메서드가 실행된다.
-   모든 함수 객체는 `[[Call]]` 내부 슬롯을 갖는다(Callable하다, 호출될 수 있따). 반면, `[[Constructor]]` 내부 슬롯은 모든 함수가 갖는 건 아니다. 함수 객체는 생성자일수도 있고 아닐 수도 있다.
-   `[[Constructor]]` 내부 슬롯은 함수 표현식, 함수 선언문, 클래스 등의 방식으로 선언된 함수만 값을 갖는다. 메서드나 화살표 함수로 선언된 함수는 생성자 함수가 될 수 없다.
-   생성자 함수는 반드시 `new` 키워드와 함께 선언돼야하고, 이를 구분짓기 위해 파스칼 케이스로 이름을 짓는 게 일반적이다. 하지만, 파스칼 케이스로 이름을 지어도 실수를 할 수 있고, 이를 방지하기 위해 `new.target`이라는 메타 프로퍼티가 제공된다. `new.target`을 사용하면 `new` 키워드와 함께 호출됐는지를 점검해서 재귀적으로 생성자 함수로 호출하게 할 수 있다.

```javascript
function Circle(radius) {
    // new.target을 이용해 new 연산자와 함께 호출되지 않은 경우 함수 내부에서 생성자 함수로 재귀적 호출
    if (!new.target) {
        return new Circle(Radius);
    }

    ...
}
```

# 18장. 함수와 일급 객체

-   아래 조건을 만족하는 객체를 일급 객체라고 한다.
    -   무명의 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
    -   변수나 자료구조(객체, 배열 등)에 저장할 수 있다.
    -   함수의 매개변수에 전달할 수 있다.
    -   함수의 반환값으로 사용할 수 있다.
-   자바스크립트의 함수는 위의 조건을 모두 만족하는 일급 객체다.
-   함수는 값으로 평가되고, 런타임에 평가될 수 있다.
-   함수는 함수의 매개변수로 전달할 수 있으며, 이는 함수형 프로그래밍을 가능케 하는 자바스크립트의 장점 중 하나이다.
-   자바스크립트는 함수의 매개변수와 인수의 개수가 일치하는지 확인하지 않고, 따라서 함수 호출 시 매개변수의 개수를 꼭 지키지 않아도 에러가 발생하지 않는다.
-   정해진 매개 변수의 개수보다 적게 인수가 전달되면 모자란 매개변수들에는 `undefined`가 자동으로 초기화된다.
-   정해진 매개 변수 개수보다 초과된 인수는 `arguments` 객체의 프로퍼티로 보관된다. 필요하다면 `arguments` 객체를 통해 접근 가능하다.
-   `arguments` 객체를 사용하면 전달 받은 인수에 대한 다양한 정보를 활용할 수 있지만, 유사 배열 객체이고 배열이 아니기 때문에 다양한 배열의 메서드들을 사용할 수 없다.
-   대체하기 위해 Rest 파라미터가 도입됐다. `...args` 형태로 매개변수를 전달하면 전달받은 `args`는 배열 형태로 사용이 가능하다. 또, Rest 파라미터를 사용하면 사전에 필요한 매개 변수의 개수나 순서도 따로 지정해두지 않아도 돼서 편리하다.
-   함수 객체의 `length` 프로퍼티는 함수를 정의할 때 선언한 매개 변수의 개수를 가리킨다. 참고로, `arguments` 객체의 `length`는 실제 함수에 인수로 전달된 값들의 개수를 의미하고, 함수 객체의 `length`와 일치하지 않을 수 있다(인수가 덜 들어오거나 더 들어올 수 있다).
-   생성자 함수로 호출할 수 있는 함수 객체, 즉 constructor만이 `prototype` 프로퍼티를 갖는다. 이는 인스턴스가 가리킬 생성자 함수의 프로토타입을 의미한다.
