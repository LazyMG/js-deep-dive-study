## 제어문

- 제어문은 조건에 따라 코드 블록을 실행하거나 반복 실행할 때 사용한다.

#### 블록문
- 블록문: 0개 이상의 문을 중괄호로 묶은 코드 블록
```
{
    var foo = 10;
}
```

#### 조건문
- 조건문은 주어진 조건식의 평가 결과에 따라 코드 블록의 실행을 결정
- 불리언 값으로 평가될 수 있는 표현식

1. if...else 문
```
if (조건식 1) {
    // 조건식 1이 참일 때 이 코드 블록 실행
} else if (조건식 2) {
    // 조건식 2가 참일 때 이 코드 블록 실행
} else {
    // 조건식 1, 2가 모두 거짓일 때 이 코드 블록 실행
}
```

2. switch 문
- switch 문은 주어진 표현식을 평가하여 그 값과 일치하는 표현식을 갖는 case문으로 실행 흐름을 옮긴다.
- switch문의 표현식은 불리언 값보다는 문자열이나 숫자 값인 경우가 많음

```
switch (표현식) {
    case 표현식1:
    switch 문의 표현식과 표현식1이 일치하면 실행될 문;
    break;
}
```

- break 문을 생략하면 switch문이 끝날 때까지 이후의 모든 case 문과 default문을 실행하는 폴스루 발생. 

#### 반복문

1. for 문
```
for (var i = 0; i < 2; i++) {
    console.log(i);
}
```

2. while 문
```
var count = 0;

while(count < 3) {
    console.log(count);
    count++;
}
```

3. do...while 문
```
var count = 0;

do {
    console.log(count);
    count++;
} while(count < 3);
```

#### break 문
- 레이블문, 반복문 또는 switch 문 탈출

#### continue 문

- 반복문의 코드 블록 실행을 현 지점에서 중단하고 반복문의 증감식으로 실행 흐름 이동