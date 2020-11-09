# 명시적 변환, 암묵적 변환, Nominal, 구조화, 덕 타이핑, == vs === vs typeof





## 타입 변환



암묵적 형변환은 양날의 검이다. 코드 결함의 큰 원인이 될 수 있지만 가독성을 잃지 않고 코드를 적게 작성할 수 있는 유용한 메커니즘 이기도 하다.

1. 자바스크립트에는 **세 가지 유형으로의 변환만 존재**한다. String, Boolean, Number
2. 원시 타입과 객체 타입의 변환 로직은 다르게 작동하지만 원시 타입과 객체 타입 모두 위 세가지 타입으로만 변환 할 수 있다.



### String conversion

값을 String으로 명시적 형변환하려면 String () 함수를 호출하면 된다. 피연산자가 문자열 인 경우 암묵적 형변환은 이진 + 연산자에 의해 가능하다.

```javascript
String(123) // 명시적 형변환
123 + ''    // 암묵적 형변환
```



모든 원시 타입은 String 타입으로 잘 형변환이 된다.

```javascript
String(123)                   // '123'
String(-12.3)                 // '-12.3'
String(null)                  // 'null'
String(undefined)             // 'undefined'
String(true)                  // 'true'
String(false)                 // 'false'
```



하지만, Symbol은 명시적으로만 형변환되고 암묵적으로는 형변환 되지 않는다.

```javascript
String(Symbol('my symbol'))   // 'Symbol(my symbol)'
'' + Symbol('my symbol')      // TypeError is thrown
```



### Boolean conversion

값을 boolean으로 명시적 형변환하려면 Boolean () 메서드를 호출하면 된다. 암묵적 형변환은 논리적 컨텍스트에서 발생하거나 논리 연산자 (|| &&!)에 의해 트리거 된다.



```javascript
Boolean(2)          // 명시적 형변환
if (2) { ... }      // 논리적 컨텍스트에 의한 암묵적 형변환
!!2                 // 논리 연산자에 의한 암묵적 형변환
2 || 'hello'        // 논리 연산자에 의한 암묵적 형변환
```



falsy 값을 기억하고 있는 것이 좋다. falsy 값이 아닌 나머지는 모두 truthy 값이다.

```javascript
Boolean('')           // false
Boolean(0)            // false     
Boolean(-0)           // false
Boolean(NaN)          // false
Boolean(null)         // false
Boolean(undefined)    // false
Boolean(false)        // false
```



### Numeric conversion

값을 숫자 타입으로 명시적 형변환하려면 Number () 메서드를 호출하면 된다. 

숫자 타입으로의 암묵적 형변환은 다양한 경우가 있다.

- 비교 연산자  (`>`, `<`, `<=`,`>=`)
- 비트 연산자 ( `|` `&` `^` `~`)
- 산술 연산자 (`-` `+` `*` `/` `%` ).
  -  참고: 피연산자가 문자열인 경우 `+` 연산자는 숫자 타입으로의 암묵젹 형변환을 트리거 하지 않는다.
- 단항  `+` 연산자
- 동등 연산자  `==` (포함.  `!=`).
  - 두 피연산자가 모두 문자열 인 경우 숫자 타입으로의 암묵젹 형변환을 트리거 하지 않는다.



```javascript
Number('123')   // 명시적 형변환
+'123'          // 암묵적 형변환
123 != '456'    // 암묵적 형변환
4 > '5'         // 암묵적 형변환
5/null          // 암묵적 형변환
true | 0        // 암묵적 형변환
```



Symbol 타입은 숫자 타입으로 명시적, 암묵적 형변환 모두 되지 않고 TypeError를 발생시킨다.

```
Number(Symbol('my symbol'))    // TypeError is thrown
+Symbol('123')                 // TypeError is thrown
```



#### + 연산자의 경우

`+` 연산자는 다른 수학 연산자와 달리 아래 세 가지 기능을 수행한다.

1. 덧셈
2. 문자열 연결
3. 단항 연산자

```javascript
// 덧셈
1 + 2 // 3
1 + 2 + 1 // 4

// 문자열 연결
1 + 2 + "1" // "31"
(1 + 2) + "1" // "31"

// 단항 연산자
+ '1'
```



### Type coercion for objects


자바스크립트 엔진이 [1] + [2,3]과 같은 표현삭을 만나면 먼저 객체를 원시 값으로 변환 한 다음 최종 타입으로 변환해야 한다. 

가장 간단한 경우는 **Boolean으로의 타입 변환**이다. 원시 타입이 아닌 값은 항상
객체 또는 배열이 비어 있는지 여부에 관계없이 **true**로 강제 형변환 된다.

객체는 숫자 및 문자열로의 타입 변환을 담당하는  **[[ToPrimitive]]** 라는 내부 메서드를 통해 원시 타입으로 변환된다.

[[ToPrimitive]]는 입력 값 및 선호하는 변환 유형 (숫자 또는 문자열)과 함께 전달된다. preferredType은 선택 사항이다.

숫자 및 문자열 변환은 모두 입력 개체의 **두 가지 메서드 인 valueOf 및 toString을 사용**한다.



대부분의 Object 타입변환은  [object Object]를 반환한다.

```javascript
"name" + {} // "name[object Object]
```



#### toString

모든 자바스크립트 객체는 객체가 문자열로 변환 될 때마다 호출되는 toString 메소드를 상속한다.

```javascript
const foo = {}
foo.toString() // [object Object]

const baz = {
  toString: () => "I'm object baz"
}

baz + "!" // "I'm object baz!"

const foo = {
  toString: () => 4
}

2 * foo // 8
2 + foo // 6
```



#### valueOf

toString 및 valueOf 메서드가 Object에 함께 정의 된 경우 Javascript는  valueOf 메서드를 사용한다.

```javascript
const bar = {
  toString: () => 2,
  valueOf: () => 5
}

"sa" + bar // "sa5"
3 * bar // 15
2 + bar // 7
```



#### Array Object

배열은 일반 객체와는 달리 Object.prototype.toString ()을 오버라이딩한 Array.prototype.toString () 메서드가 호출되어 Array를 문자열로 변환한다.  Array.prototype.toString ()  메서드는  인수없이 배열의 join 메서드를 호출하는 것과 유사한 방식으로 작동한다.

```javascript
[1,2,3].toString() // "1,2,3"
[1,2,3].join() // "1,2,3"
[].toString() // ""
[].join() // ""

"me" + [1,2,3] // "me1,2,3"
4 + [1,2,3] // "41,2,3"
4 * [1,2,3] // NaN
```

숫자값이 기대되면 반환값을 숫자로 변환한다.

```javascript
4 * [] // 0
4 / [2] // 2

//similar to
4 / Number([2].toString())
4 / Number("2")
```



### NaN

NaN은 자바스크립트 값 중 자기 자신과 같지 않은 유일한 특수 숫자 값이다.

```javascript
NaN === NaN // false

const notANumber = 3 * "a" // NaN

notANumber === notANumber // false
```



ES6에서 NaN을 확인하는 메서드 Number.isNaN()을 도입했다.

```javascript
Number.isNaN(NaN) // true
Number.isNaN("num") // false
```



#### global isNaN

NaN인지 확인하기 전에 인수를 강제 타입 변환하기 때문에 전역 isNaN 메서드는 사용 하지 않는 것이 좋다.

```
isNaN("name") // true
isNaN("1") // false
```



### null vs undefined

```javascript
console.log( typeof undefined)
console.log( typeof null)

undefined == null
undefined === null
```

공통점

- falsy 값이다.

차이점

- null은 Object이고 undefined는 자기 자신만의  타입인 undefined 타입이다.
- 숫자로 타입 변환 시 null은 0, undefined는 NaN이다.



### Examples

```javascript
true + false             // 1
12 / "6"                 // 2
"number" + 15 + 3        // 'number153'
15 + 3 + "number"        // '18number'
[1] > null               // true
"foo" + + "bar"          // 'fooNaN'
'true' == true           // false
false == 'false'         // false
null == ''               // false
!!"false" == !!"true"    // true
['x'] == 'x'             // true 
[] + null + 1            // 'null1'
[1,2,3] == [1,2,3]       // false
{}+[]+{}+[1]             // '0[object Object]1'
!+[]+[]+![]              // 'truefalse'
new Date(0) - 0          // 0
new Date(0) + 0          // 'Thu Jan 01 1970 02:00:00(EET)0'
```



### Nominal Typing

- 특정 키워드를 통해 서로 호환 가능하다고 명시적으로 표현된 타입 간의 할당만을 허용
- 대표언어: c#, Java



### Structural Typing && Duck Typing

- 멤버에 따라 타입을 연관 짓는 방법
- 타입의 구조를 비교하여 호환성을 결정
- 동적 타이핑의 한 종류로, 객체의 변수 및 메서드의 집합이 객체의 타입을 결정하는 것을 의미.
- 만약 어떤 새가 오리처럼 걷고, 헤엄치고, 꽥꽥 소리를 낸다면 그 새를 오리라고 부를 것이라는 말에서 유래
- 형태를 판별하는 대신, 원하는 동작을 수행할 수 있는지 여부를 검사.
- 유연한 코드 작성의 용이하지만, 형 검사를 하지 않기 때문에 오류 발생 가능성이 높은 편이다
- 대표언어: typeScript



### == vs ===

- 동등 연산자 `==` 는 암묵적 타입 변환 후 비교한다.
- 일치 연산자 `===` 는 암묵적 타입 변환을 하지 않고 비교한다.

- `===` 일치 연산자로 타입과 값을 비교함으로써 피연산자들이 진정으로 동등한지를 비교할 수 있다.



```javascript
// 잁치 연산자로 타입 체킹 시 typeof가 사용될까???
undefined === null // false
undefined == null  // true
```





## 참고

https://dev.to/promhize/what-you-need-to-know-about-javascripts-implicit-coercion-e23

https://www.freecodecamp.org/news/js-type-coercion-explained-27ba3d9a2839/

https://hackernoon.com/javascript-coercion-explained-545c895213d3

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/toString

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Object/valueOf

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/toString

https://webbjocke.com/javascript-check-data-types/

https://corock.tistory.com/459?category=727443

https://flow.org/en/docs/lang/nominal-structural/