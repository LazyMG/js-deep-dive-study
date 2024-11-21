> 이미지 url 참조가 제대로 되지 않아서, 전체 본문은 [블로그 포스트](https://jaygo-dev.tistory.com/4)에서 확인하실 수 있습니다.

---

# **얕은 복사(Shallow Copy)와 깊은 복사(Deep Copy)**

JavaScript의 데이터 타입은 크게 원시 타입(Primitive Type)과 참조 타입(Reference Type)으로 나눌 수 있다. 원시 타입은 확보된 스택 메모리에 값을 직접 저장하지만, 참조 타입은 값을 힙 영역에 저장하고, 스택에는 데이터가 저장된 힙 메모리 영역의 첫 주소값이 저장된다. 값을 스택에 직접 저장하는지 참조할 주소를 저장하는지에 따라 차이가 있으며, 그 차이로 인해 데이터 복제 방법에서 차이가 생긴다([원시 타입과 참조 타입](https://jaygo-dev.tistory.com/2) 참고).

원시 타입 데이터는 변수로 데이터를 복제하면 값이 저장된 동일한 메모리 주소를 가리킨다. 원시 타입의 값은 불변이기 때문에 복제한 변수에 다른 값을 재할당하면 가리키던 메모리 공간에 저장된 값을 변경하는 게 아닌, 새로운 메모리 공간을 재할당해서 값을 저장한다. 즉, 두 변수는 재할당 이후 서로 다른 값을 가지게 된다.

```
let myStr1 = 'Hello';
let myStr2 = myStr1;

myStr2 = 'Hi';
console.log(myStr1, myStr2) // Hello Hi
```

[##_Image|kage@er7rzE/btsJ06CouZa/QTXYRkXB35KevJWHnzUmrK/img.jpg|CDM|1.3|{"originWidth":1280,"originHeight":720,"style":"widthContent"}_##][##_Image|kage@Umoq6/btsJ0DUMS0Y/yY9TAMjheiA5cYQWawn4w1/img.jpg|CDM|1.3|{"originWidth":1280,"originHeight":720,"style":"widthContent"}_##]

반면, 참조 타입은 값을 복제했을 때 힙의 데이터 저장 공간의 주소를 공유하게 되며, 값을 변경하면 스택에 저장된 주소 값은 동일하고 참조된 힙 영역의 데이터만 바뀌게 된다. 그 결과, 복제된 변수의 값을 변경하면 원본 변수의 값도 함께 바뀐 것처럼 돼버린다.

```
let myObj1 = {
    name: 'Sam'
};

let myObj2 = myObj1; // 참조 타입 데이터 복제

myObj2.name = 'Tom';

// myObj2.name만 변경했는데 myObj1.name도 같이 변경됨
console.log(myObj1, myObj2) // {name: 'Tom'} {name: 'Tom'}
```

[##_Image|kage@TzQMW/btsJ0RSSOsj/aIOTPedFhBSmSOVSP6Mvtk/img.jpg|CDM|1.3|{"originWidth":1280,"originHeight":720,"style":"alignCenter"}_##]

참조 타입 데이터는 `myObj2 = myObj1`처럼 변수를 직접 복제하는 참조하는 값이 같아 서로의 상태에 의존되는 상황이 생겨버린다. 이런 상황을 '얕은 복사(Shallow Copy)'라고 한다. 반대로, 참조 타입 데이터를 복제했을 때 힙 영역에 있는 데이터를 복제하고, 스택에 힙 메모리 주소의 참조를 아예 다르게 해서, 복제된 값과 원본 값의 상태 의존을 아예 없어지도록 값을 복제하는 것을 '깊은 복사(Deep Copy)'라고 한다

## **얕은 복사의 문제점**

먼저, 얕은 복사가 문제가 되는 상황을 실제 메모리 영역까지 고려해서 좀 더 상세히 확인해 봤다

```
function Person(name, age) {
  this.name = name,
  this.age = age
}

let obj1 = new Person('Sam', 25);
let obj2 = new Person();
```

`Person`이라는 객체 프로토타입을 정의하고, 생성자 함수로 `Person` 객체를 각각 선언하여 `obj1`과 `obj2`에 각각 할당하였다(`obj2`는 인자를 전달하지 않고 빈 객체로 생성하였다). 크롬 브라우저의 개발자 도구 Memory 탭을 통해 힙 영역에 `obj1`과 `obj2`의 메모리 공간이 서로 다른 `@77411`과 `@77423`로 확보된 것이 확인된다.

[##_Image|kage@o9k9g/btsJ2S32jfZ/sSv72lcrSBKowgeKP2MEP0/img.png|CDM|1.3|{"originWidth":3006,"originHeight":1646,"style":"alignCenter"}_##]

이제 `obj2`에 `obj1` 객체를 재할당 해보면, `obj1`에 있던 프로퍼티와 값이 복제된 것이 확인된다.

```
obj2 = obj1;
console.log(obj1, obj2); // Person {name: 'Sam', age: 25}  Person {name: 'Sam', age: 25}
```

메모리를 다시 확인해 보자. `obj1`과 `obj2`의 값을 저장하는 힙 공간이 하나가 된 것이 확인된다. `obj1`과 `obj2`는 두 개의 참조 타입 데이터를 각각 다르게 저장하고 관리하는 게 아니라, 한 곳의 힙 메모리에 저장된 값을 함께 참조한다(단, 재할당 시 최초의 힙 메모리 주소가 아닌 새로운 곳에 데이터를 다시 저장하고, `obj1`과 `obj2`의 참조가 함께 변경됐다는 점이 재밌다. 왜 그런진 잘 모르겠다).

[##_Image|kage@oYruA/btsJ1DmDEdi/6koziVUi7kjgNctPsqiE0k/img.png|CDM|1.3|{"originWidth":3006,"originHeight":1646,"style":"alignCenter"}_##]

이제 `obj2`의 값에 변경을 해주면 `obj1`의 값도 함께 바뀐다. `obj2`가 변경한 데이터를 `obj1`도 같이 참조하기 때문이다.

```
obj2.name = 'Tom';
console.log(obj1, obj2); // Person {name: 'Tom', age: 25}  Person {name: 'Tom', age: 25}
```

힙 메모리 영역을 확인해 봐도, 한 곳의 메모리 주소에 저장된 값이 바뀐 게 확인된다. 해당 데이터를 `obj1`과 `obj2`가 같이 참조하고 있기 때문에 `obj2`의 값만 변경해도 `obj1`이 같이 변경되는 것이다(참고로, 힙 메모리 주소가 `@110767`로 또 변경됐다. 참조 타입의 '가변성'이 스택 메모리를 기준인거지, 힙에서는 데이터를 변경할 때마다 새로운 메모리 공간을 재확보하면서 확보하는 불변적인 방식인 건 아닐까. 어쩌면, 참조 타입의 특성이 스택 메모리에 저장되는 힙 영역의 메모리 주소가 계속 바뀌기 때문에 '가변적'이라고 하는 게 아닐까 싶기도 하다).

[##_Image|kage@bnQbv7/btsJ0UWN5qn/MsrlHkWpsYnGLwTmAkR8dK/img.png|CDM|1.3|{"originWidth":3006,"originHeight":1646,"style":"alignCenter"}_##]

이렇게, 얕은 복사로 참조 타입인 객체를 복사했을 때 복제된 객체와 기존 객체의 값이 의존되는 상황이 확인됐다. 이런 문제 상황을 회피하기 위해선 깊은 복사로 객체를 복사하는 게 필요하다.

## **깊은 복사의 어려움**

깊은 복사를 구현하는 건 생각보다 복잡하다. 직접 코드로 구현을 해보면서 어려움을 확인해 보자. 일단, 가장 직관적으로 깊은 복사를 하는 방식은 복제할 객체의 속성-값을 하나하나 직접 복제하는 것이다. 코드로 살펴보자.

```
function Person(name, age) {
  this.name = name,
  this.age = age
}

let obj1 = new Person('Sam', 25);
let obj2 = new Person();

for (let key in obj1) {
  obj2[key] = obj1[key];
}

obj2.name = 'Tom';

console.log(obj1, obj2); // Person {name: 'Sam', age: 25}  Person {name: 'Tom', age: 25}
```

반복문으로 `obj1`의 프로퍼티(key)들을 순회하면서 `obj1`\[key\]의 값을 `obj2`\[key\]에 직접 복사해 줬다. `obj1.name`과 `obj2.name`은 원시 타입(각각 문자열과 숫자형 데이터) 데이터이기 때문에, 객체 내부의 프로퍼티와 값에 직접 접근해서 복사를 해주면 `obj1`과 `obj2`는 서로 다른 참조를 유지하면서 값만 가져올 수 있다.

[##_Image|kage@bpu4xv/btsJ2oPTszP/ZhzlnuNgJos6lKki7kqFQ1/img.png|CDM|1.3|{"originWidth":3006,"originHeight":1646,"style":"alignCenter","caption":"힙 메모리 영역이 서로 다른 주소를 가지고 있다. `obj1`과 `obj2` 값의 의존이 사라졌다."}_##]

하지만, 위의 방식은 객체 프로퍼티의 값으로 참조 타입이 올 경우 문제가 발생한다. JavaScript의 객체는 프로퍼티의 값으로 모든 데이터 타입을 가질 수 있다. 해당 상황에서는, 가장 밖에 있는 객체의 속성과 값만 접근해 복제하는 건 아쉬움이 있는 방법이다. 아래 상황을 확인해 보자.

```
function Person(name, age) {
  this.name = name,
  this.age = age
}

let obj1 = new Person('Sam', 25);
let obj2 = new Person();

// obj1의 address 프로퍼티의 값으로 객체를 할당
obj1.address = {
  country: 'Korea',
  city: 'Seoul'
};

for (let key in obj1) {
  obj2[key] = obj1[key];
}

console.log(obj1, obj2);
```

[##_Image|kage@odg6x/btsJ14RT6uN/TomYshZkBxPtIyXMLJMyNk/img.png|CDM|1.3|{"originWidth":3010,"originHeight":1640,"style":"alignCenter","caption":"객체가 잘 복제된 것처럼 보인다."}_##][##_Image|kage@b0jv6i/btsJ2w8k5tC/N63DPlYAZMzKp2Q9xZ2ujk/img.png|CDM|1.3|{"originWidth":3010,"originHeight":1640,"style":"alignCenter","caption":"하지만, `address` 프로퍼티에 할당된 객체의 메모리 주소가 같다. 뭔가 불길하다."}_##]

`obj1.address`와 `obj2.address`의 메모리 주소가 같기 때문에, 한쪽에서 접근해 값을 변경하면 다른 쪽 값도 같이 변경된다. `obj1`과 `obj2`는 제대로 복제가 됐지만, `obj1.address`와 `obj2.address`는 제대로 복제가 안된 거다. 해당 상황에서, `obj2.address.city`로 접근해 값을 바꿔보면 `obj1.address.city`도 같이 값이 바뀐 게 확인된다.

```
obj2.address.city = 'Busan'
console.log(obj1.address.city, obj2.address.city); // Busan Busan
```

모던 자바스크립트 딥다이브 p150을 보면 '얕은 복사는 한 단계까지만 복사하는 것을 말하고 깊은 복사는 객체에 중첩되어 있는 객체까지 모두 복사하는 것을 말한다'라는 설명이 있다. 참조 타입의 속성 값으로 저장된 값이 참조 타입인 상황을 전제해야 한다는 것이다. 만약에 객체의 속성 값이 객체인데, 그 객체의 속성 값이 또 객체이고, 또 객체이고... 이렇게 객체가 속성 값으로 참조되어 있는 경우가 굉장히 많이 반복되는 경우에는 객체를 복사하기 위해 복사하는 코드가 굉장히 깊게 순회를 돌아야 한다(여러 차례 순회를 해야 한다).

그렇기 때문에, 깊은 복사를 제대로 구현한 코드는 객체 내에 있는 모든 프로퍼티의 타입을 확인하고, 타입이 `object`인 경우엔 해당 객체 내부에 있는 프로퍼티와 값을 전부 다 순회하면서 복제해야 한다. 코드로 구현해 보면 아래와 같다.

```
function deepCopy(obj, objNew) {
  // key를 순회하며 값을 하나하나 복제
  for (let key in obj) {
    // ojb[kye]가 객체 타입이면 그 안에 있는 값을 다시 재귀로 돌아서 값을 하나씩 복제
    // null은 원시타입이지만 typeof null은 'object'로 결과가 나오기 때문에, null인 경우 제외
    if ((typeof obj[key] === 'object') && (typeof obj[key] !== null)) {
      objNew[key] = deepCopy(obj[key], objNew[key] = {}); // 재귀로 접근
    } else {
      objNew[key] = obj[key]; // object가 아닌 타입의 데이터는 바로 복사
    }
  }

  return objNew;
}
```

아래와 같이 직접 구현한 코드를 사용하면 프로퍼티의 값이 객체로 들어가 있어도 개별로 하나씩 순회하면서 값을 복사할 수 있다.

```
// 위에서 활용하던 예제 이어서 진행
// 객체로 거주지 정보를 추가
obj1.address = {
  country: 'Korea',
  city: 'Seoul'
}; 

// 취미 객체 추가로 생성
obj1.hobby = {
  // 취미인 스포츠면 true, 아니면 false
  sports: {
    running: true,
    swimming: false,
    soccer: true
  }
};

// 위에서 만든 deepCopy 함수로 깊은 복사 실행
deepCopy(obj1, obj2);

// obj2의 값들 변경
obj2.address.city = 'Busan';
obj2.hobby.sports.swimming = true;

console.log(obj1, obj2);
```

[##_Image|kage@bFNNOR/btsJ3X48OxD/TTw2Gek3BwigNAwTxkEuhk/img.png|CDM|1.3|{"originWidth":3010,"originHeight":1640,"style":"alignCenter"}_##]

`$address.city`의 값과 `$hobby.sports.swimming`의 값의 의존이 사라졌다. 깊은 복사에 성공했다(힙에 데이터 저장을 위해 확보한 메모리 주소도 서로 값이 다른 게 잘 확인된다).

[##_Image|kage@cUmia5/btsJ2IhsCqh/knJSXRQfhVV2IyrDkkREb1/img.png|CDM|1.3|{"originWidth":2706,"originHeight":1238,"style":"alignCenter","caption":"깊이를 순회하며 모든 참조 타입 값이 잘 복제됐다. 깊은 복제가 성공적으로 잘 이뤄졌다."}_##]

사실, 직접 작성한 코드는 직관적으로 필요한 기능을 동작하게만 간단하게 만든 코드고, 여러 예외 처리가 완벽하게 이뤄지지 않았다. 그래서 실제 개발에서 그대로 사용 시 의도치 않은 장애가 발생할 수도 있다(대표적으로, 배열과 객체를 구분해서 처리하지 않았다). 또, 저렇게 수정 가능한 함수에 깊은 복사 기능을 정의해서 사용할 경우 누군가 코드를 수정 시 실수를 하면 문제가 생길 수 있다(Editable 하기 때문에 기능의 안정성이 담보되지 않는다).

그래서, 실제 개발 시 깊은 복사를 해야 할 때에는 JavaScript에서 지원하는 도구를 사용하거나, 잘못된 참조로 인한 값 변경 시 의존을 없애기 위한 스킬을 사용한다.

## **깊은 복사를 처리하는 다양한 방법**

#### **`lodash` 라이브러리의 `cloneDeep()` 메서드**

JavaScript에서 지원되는 `lodash` 라이브러리에는 깊은 복사를 지원하는 `cloneDeep()` 메서드가 있다. `cloneDeep()`에는 깊은 복사에 필요한 필수 로직 처리와, 필요한 예외 처리들이 모두 잘 구현되어 있기 때문에 해당 메서드를 사용하면 간편하게 깊은 복사를 수행할 수 있다. 또한, 깊은 복사를 직접 구현한 코드를 사용하면 누군가의 접근으로 코드가 망가지고, 의도치 않은 동작을 하게 될 수 있지만, `cloneDeep()`은 누군가에 의해 잘 만들어진 메서드에 접근 및 수정이 어렵기 때문에(만에 하나 수정이 됐다 해도 Node.js를 활용해 다시 설치하면 되기 때문에) 보다 안정적인 사용이 가능하다.

`lodash`는 Node.js 환경에서 사용이 가능하다. 하지만, Node.js를 설치한 후 `node_modules`에 있는 `lodash`를 참조해 메서드를 불러와 그냥 사용하려고 하면 브라우저 환경에서 에러가 난다. 브라우저는 Node.js 환경이 아니고(브라우저 엔진 환경에서 동작한다), 개발 환경을 같이 번들링 해서 띄워놓는 게 아니면 브라우저는 `lodash`를 참조할 수 없다.

만약에 번들링 도구를 사용한 환경 설정이 어려운 상황에서, 브라우저 동작으로 `lodash`의 기능을 확인하고 싶다면 HTML 파일 `<head>` 태그에 아래 스크립트를 추가하면 된다. 해당 코드를 추가하면 `src`로 추가한 CDN 서버에서 받아온 `lodash.min.js` 파일에 접근해 필요한 메서드들을 불러올 수 있다.

```
<script src="https://cdn.jsdelivr.net/npm/lodash@4.17.21/lodash.min.js"></script>
```

해당 태그를 HTML 파일에 추가하고, 다음 JavaScript 코드를 아래와 같이 작성하면 깊은 복사가 잘 실행되는 걸 확인할 수 있다.

```
function Person(name, age, address) {
  this.name = name,
  this.age = age,
  this.address = address
}

let obj1 = new Person('Sam', 25, { country : 'Korea', city : 'Seoul'});
let obj2 = new Person();

obj2 = _.cloneDeep(obj1);

obj2.address.city = 'Busan';
console.log(obj1, obj2);
```

[##_Image|kage@kM6Sg/btsJ34i4qA9/VMNmKj2iamFK6qXunVYNvK/img.png|CDM|1.3|{"originWidth":2664,"originHeight":1238,"style":"alignCenter","caption":"복제된 객체에서 `.address.city`의 값이 의존 없이 잘 수정되었다."}_##][##_Image|kage@D1MWZ/btsJ3llDhqh/of79hkPDA3oNSVIFkA7vg1/img.png|CDM|1.3|{"originWidth":2664,"originHeight":1398,"style":"alignCenter","caption":"`.address`가 저장된 힙 메모리 주소가 다른 게 확인된다."}_##]

사용한 라이브러리는 이외에도 다양한 기능들을 제공하기 때문에, 잘 이용하면 쉽게 깊은 복사를 할 수 있을 뿐 아니라 JavaScript의 여러 데이터 구조(배열, 객체, Collection, Date 등)를 쉽게 다룰 수 있다.

#### **`JSON.stringfy()`와 `JSON.parse()`**

굳이 객체 안에 있는 모든 파라미터를 깊게 순회하며 필요한 값을 복제하지 않고, 복제할 객체를 문자열로 만든 뒤 해당 문자열을 통으로 복제하고, 이후에 해당 문자열을 다시 객체 형태로 변환하면 깊은 복사를 처리할 수 있다. 문자열은 원시 타입이고, 원시 타입은 복제를 할 때 스택 메모리에 참조된 메모리 주소가 아닌 실제 값을 복제하는 식으로 처리되기 때문에, 해당 방식으로 처리하면 손쉽게 깊은 복사를 처리할 수 있다. 객체를 문자열로 만드는 데에는 JSON 객체가 사용되며, 해당 객체에 대해 이해하려면 먼저 JSON 데이터 타입에 대해 살펴봐야 한다.

JSON은 JavaScript Object Notation의 줄임말로, 'JavaScript의 객체 문법으로 구조화된 데이터를 표현하기 위한 문자 기반의 표준 포맷'이다([MDN 문서](https://developer.mozilla.org/ko/docs/Learn/JavaScript/Objects/JSON) 참고). JavaScript는 태생이 브라우저에서 동작되는 웹 개발용 언어였고, 웹의 개념 상 서버와의 통신 과정에서 네트워크를 통해 데이터를 주고받는 상황이 많이 발생했다. 그런데, 그 당시만 해도 JavaScript는 서버 개발에 사용될 수 없는 프로그래밍 언어였기 때문에, 모든 서버들은 다른 프로그래밍 언어로 이루어져 있었다.

그랬기 때문에, JavaScript에서만 고유한 방식으로 동작하는 객체를 그대로 서버에 전달하면 문제가 발생했다. 서버를 구성한 프로그래밍 언어는 JavaScript 객체가 어떻게 구성되고, 또 어떻게 동작하는지 알 수 없었기 때문에, 해당 데이터를 처리하지 못했던 것이다. 이러한 문제를 해결하기 위해서, JavaScript의 객체 형태 데이터는 객체 타입이 아니라 문자열로 치환돼서 전달돼야 했고, 그렇게 JSON 타입이 탄생하게 됐다. JSON은 객체를 문자열 형태로 표현한 포맷이다.

크롬 브라우저 개발자 도구 콘솔에서 JSON 객체를 출력해 보면 아래와 같이 값이 확인된다. JSON은 엄밀히 말하면 문자열이지만, JSON만의 고유한 기능이 별도로 정의돼 있다. 아래 캡처 이미지를 보면 JSON 객체에 정의된 메서드들이 확인된다.

[##_Image|kage@c6Ntja/btsJ3XqOhe6/Ens75jpqluiDOnF54AqkN1/img.png|CDM|1.3|{"originWidth":2664,"originHeight":1398,"style":"alignCenter"}_##]

해당 메서드 중 `.stringify()`를 사용하면 JavaScript 객체가 문자열 데이터로 변환된다.

```
let obj = { name: 'Sam' };

console.log(JSON.stringify(obj)); // '{"name":"Ko"}'
console.log(typeof JSON.stringify(obj)); // 'string'
```

프로퍼티의 값으로 객체가 할당된 깊은 구조의 객체도 `JSON.stringify()`를 사용하면 문자열로 바뀐다.

```
function Person(name, age, address) {
  this.name = name,
  this.age = age,
  this.address = address
}

let obj1 = new Person('Sam', 25, { country : 'Korea', city : 'Seoul'});

console.log(JSON.stringify(obj1)); // '{"name":"Sam","age":25,"address":{"country":"Korea","city":"Seoul"}}'
```

문자열은 원시 타입이다. 그렇기 때문에, 객체를 문자열로 변환된 값을 복제하면 객체의 위치를 나타내는 힙 메모리 주소가 참조되는 것이 아니라, 전체가 문자열이 된 문자 형태의 객체값을 스택에 직접 저장하게 된다.

```
function Person(name, age, address) {
  this.name = name,
  this.age = age,
  this.address = address
}

let obj1 = new Person('Sam', 25, { country : 'Korea', city : 'Seoul'});
let obj2 = JSON.stringify(obj1); // 문자열로 값 저장
```

이제 `let obj2`는 문자열이 저장되어 있기 때문에 '문자열 타입을 담은 변수'로 정의된다. 만약 해당 변수에 다른 값을 재할당하게 되면, 스택 메모리는 재할당된 변수를 저장하기 위한 별도의 공간을 다시 확보하고, 재확보한 메모리 공간에 값을 저장한다. 그리고 이때, 문자열로 바꾼 객체를 다시 객체 타입의 데이터로 변환해서 값을 저장하면 객체가 힙의 별도 공간에 메모리를 확보해 저장되고, 재확보한 스택 메모리 공간에 힙의 주소가 참조되면서, 자연스럽게 깊은 복사가 된다(복사된 객체가 서로의 상태 변화에 있어 독립적이게 된다).

`JSON.stringify()`를 이용해 문자열로 바꾼 객체를 다시 객체 타입으로 변환하기 위해선 `JSON.parse()` 메서드를 사용하면 된다.

```
obj2 = JSON.parse(obj2); // {name: 'Sam', age: 25, address: {…}}
```

이렇게 복제된 `obj2`는 `obj1`과 다른 힙 메모리 주소를 참조하며, 상태값 변화에 있어서 상호 독립적이다.

```
obj2.address.city = 'Busan';

console.log(obj1, obj2); //
```

[##_Image|kage@IRlYq/btsJ3SpFcaK/NTaCwqal6eLmUDSXXO7Nuk/img.png|CDM|1.3|{"originWidth":2420,"originHeight":1282,"style":"alignCenter","caption":"`.address.city`의 값이 서로 다르게 저장돼있다"}_##]

프로퍼티 `address`에 할당된 객체의 `city` 프로퍼티 값이 각각 `Seoul`과 `Busan`으로 다르게 저장되었다. 깊은 복사가 잘 처리된 게 확인된다. 참고로, 위에 두 개 줄로 나눠서 실행한 깊은 복사 과정을 아래와 같이 한 줄로도 처리할 수 있다(동작 순서나 방식은 동일하다).

```
obj2 = JSON.parse(JSON.stringify(obj1));
```

`JSON.stringify()`와 `JSON.parse()`를 사용하면 쉽게 깊은 복사를 구현할 수 있다. 하지만, 해당 방식에도 단점은 있다. 위에 결과 출력 이미지를 봐도 알 수 있겠지만, 원래 객체 obj1은 생성자 함수 `Person()`을 통해 생성했으며, `\[\[Prototype\]\]`의 하위에 있는 `constructor`가 `Person`으로 지정돼 있는 게 확인된다. 반면, `obj2`의 생성자는 일반 객체 `Object`다.

[##_Image|kage@IG8a8/btsJ2nxYUos/4X4d7RnKwgz90DkVCk4Aok/img.png|CDM|1.3|{"originWidth":2206,"originHeight":1282,"style":"alignCenter","caption":"JSON을 이용한 깊은 복사는 생성자가 다른 걸로 처리된다."}_##]

만약에 생성자 함수로 객체의 프로토타입을 지정하고, 프로토타입에 상속시켜 줄 값이나 메서드를 추가한 다음, 생성자 함수로 선언된 객체에서 프로토타입 체인을 통해 접근해 사용하려고 했다면 두 객체의 생성자가 달라지는 건 문제가 된다. 객체에서 사용돼야 하는 값과 메소드를 전부 객체 안에 정의를 해뒀다면 문제가 되지 않겠지만, 만약에 생성자를 동일하게 유지하고 싶다면 JSON을 통해 파싱한 값을 생성자 함수 `new Person()` 안에 값으로 넣어주면 된다.

```
let obj2 = new Person(JSON.parse(JSON.stringify(obj1)));
obj2.address.city = 'Busan';
console.log(obj1, obj2);
```

[##_Image|kage@J3CJ7/btsJ3LxkpSZ/XYzExULV7oAAME7UmSVvlK/img.png|CDM|1.3|{"originWidth":2386,"originHeight":1120,"style":"alignCenter","caption":"값에 의존이 없도록 잘 복제됐고, 생성자도 `Person`으로 동일해졌다."}_##]

## **결론**

객체 타입의 데이터를 복제할 때 얕은 복사로 처리되면 의도치 않은 동작이 발생할 수 있다. 주의해야 한다. 깊은 복사는 직접 구현해서 해도 되지만, 복잡하기도 하고, 실수의 여지가 있기 때문에 가능하면 `lodash`의 `cloneDeep()` 메서드를 사용하거나, JSON으로 파싱해서 복제하자. 그리고, JSON으로 파싱해서 복제할 땐 생성자 정보는 복제가 안될 수 있으니, 이 점도 꼭 주의하자.
