# 2. 원시 자료형



## 숫자는 자바스크립트에서 어떻게 인코딩 되는가?

자바스크립트의 모든 숫자는 부동 소수점 입니다. 부동 소수점 숫자가 64비트 바이너리로 내부적으로 어떻게 표현되는지 설명. 

자바스크립트는 binary64 도는 배정밀도를 사용한다.

64비트의 이진 형식으로 저장된다.

분수는 비트 0 ~ 51을 차지하고 지수는 비트 52 ~ 62를 차지하고 부호는 비트 63을 차지합니다.

부호 비트가 0이면 숫자는 양수이고 그렇지 않으면 음수이다. 분수는 숫자의 자릿 수를 포함하고 지수는 점이 있는 위치를 나타낸다.

자바스크립트 숫자는 바이너리로 저장되지만 기본 출력은 10진수이다. 



## 0.1 + 0.2가 왜 0.3 과 같지 않으며 9007199254740992과 9007199254740993가 같은가?



Java 또는 C와 같은 정적타입 언어는 숫자에 대해 서로 다른 데이터 유형으로 나뉜다. 예를 들어

[-127; 128] 범위의 정수를 저장해야 하는 경우 Java에서는 'byte'를 사용하고 C에서는 'char'를 사용할 수 있다.  둘 다 1byte만 차지한다.  더 큰 정수를 저장해야 하는 경우 각각 4바이트와 8바이트를 차지하는 'int'  또는 'long' 데이터 유형을 사용할 수 있다. 

자바스크립트의 숫자 타입은 ECMAScript 표준에 따르면 숫자 타입은 한 가지 뿐이며 '배정 밀도 64 비트 바이너리 형식 IEEE 754 값' 이다.  이 유형은 정수와 분수를 모두 저장하는 데 사용되며 Java 및 C의 'double'데이터 유형과 동일하다.

숫자 1 저장 시

- misunderstanding

  0000000000000000000000000000000000000000000000000000000000000000000001

- in fact stored as

  0011111111110000000000000000000000000000000000000000000000000000000000

```
1 / i의 결과가 정수가 아니라 부동 소수점으로 평가되어 매우 흥미로운 동작으로 이어지기 때문에이 루프는 절대 멈추지 않을 것입니다. ??????????????????????????????
```



0.1 + 0.2가 0.3과 같지 않은 이상한 현상은 부동 소수점 형식을 사용하는 모든 언어와 관련이 있습니다. 즉,  Java 또는 C에서 float 또는 double 데이터 유형을 사용하면 동일한 결과가 표시됩니다. 



scientific notation 숫자표현의 일반적인 형태

![Image for pRepresenting numbers in the scientific notationost](https://miro.medium.com/max/324/1*FoBb3L5tCBu54c1JSo1z5A.png)





 0.1 더하기 0.2는 0.3이 아니다. 자바스크립트에서 숫자는 '64 비트 IEEE 754 형식'으로 다뤄지는데, 그 형식으로 표현된 0.1은 정확히 0.1과 같은 수가 아닌 0.1에 가장 가까운 2진법으로 표현 가능한 수이다.  우리가 콘솔에 0.1을 입력하면, 자바스크립트는 이를 '64 비트 IEEE 754 형식'에 따라 2진법으로 바꾸고 그 결과를 다시 10진법으로 바꿔 화면에 표시한다.



2진법으로는 분모가 2의 거듭제곱인 분수만 유한소수로 표현할 수 있다. 그래서 0.1(=1/10), 0.2(=1/5)처럼 분모가 2의 거듭제곱이 아닌 수는 2진법으로 나타낼 때 **무한소수**가 된다.

 0.1(=1/10), 0.2(=1/5) 같이 2진법으로 나타낼 때 소수점 아래가 무한히 이어지는 숫자들을 '64 비트 IEEE 754 형식'으로 나타내려면, 비트 안에 수가 담길 수 있도록 가수(mantissa)부의 정해진 자리에서 수를 반올림해야 한다.

자바스크립트에서 0.3(=3/10)을 입력한다면 수는 한 번 **반올림**된다. 하지만 0.1 + 0.2를 연산하면 0.1과 0.2에서, 그리고 0.1과 0.2를 더한 값에서 반올림되기 때문에 0.1 + 0.2와 0.3은 다른 값이다.

- 0.1 + 0.2 = 0.30000000000000004
- 0.3 = 0.29999999999999998



'64 비트 IEEE 754 형식'에서는 -(2^53 - 1)부터 2^53 - 1 사이의 수가 안전하다(여기서 안전함이란 정수를 정확하고 올바르게 비교할 수 있음을 의미한다). 즉 안전한 가장 큰 정수는 **9007199254740991**, 가장 작은 정수는 **-9007199254740991**이다.

https://velog.io/@sgyoon/2019-09-15-01

https://imcts.github.io/javascript-exponent-expression/





### Number.EPSILON

Number.EPSILON은 두 개의 표현 가능한 숫자 사이의 가장 작은 간격을 반환한다. 이는 부동소수점 근사치를 활용해 분수가 제대로 표현되지 않는 문제를 해결하는데 유용하다.

```
function numberEquals(x, y) {
    return Math.abs(x - y) < Number.EPSILON;
}

numberEquals(0.1 + 0.2, 0.3); // true
```

0.1+0.2와 0.3의 차이는 Number.EPSILON보다 작을 것이다.



### 자바스크립트 원시 타입

- undefined
- null
- boolean
- string
- number
- symbol



자바스크립트 원시(기본) 타입은 properties가 없다. 그렇다면 'abc'.length는 왜 값을 반환하는 것일까?

- JavaScript는 원시타입과 객체 타입을 쉽게 강제 타입 변환 할 수 있기 때문.
- 위 경우 length property에 접근하기 위해 문자열 타입이 문자열 객체를 강제 타입 변환 된다.

```javascript
const primitive = "september";
primitive.vowels = 3;

primitive.vowels; //undefined;
```

https://javascriptweblog.wordpress.com/2010/09/27/the-secret-life-of-javascript-primitives/



string, number, boolean,과 String, Number, Boolean은 서로 다른 유형이다.

- 전자는 'foo', 42, true, false와 같은 리터럴 값 또는 "" + 42, a===b와 같은 표현식의 결과이다.
- 후자는 전역 new (String, Number, Boolean) 생성자가 만든 래퍼 객체



Number

- Infinity 와 NaN도 숫자타입으로 간주
- window.isNaN vs Number.isNaN

```
// @flow
"foo" + String({});     // Works!
"foo" + [].toString();  // Works!
"" + JSON.stringify({}) // Works!
```





https://developer.mozilla.org/ko/docs/Web/JavaScript/Data_structures





## Object

```javascript
const obj = {
    name: 'lee',
    id: 3
}
```

위 코드에서 name 과 obj를 **데이터 프로퍼티(Data Properties)**이라 한다. 



### 접근자 프로퍼티

- C# 이나 파이썬 같은 다른 언어에서는 getters와 setters로 이해될 수 있다.
- 접근자 프로퍼티는 get, set 두가지 함수의 조합이다.

```javascript
const accessorObj = {
  get name() {
    return 'lee';
  }
};

accessorObj.name;
// => 'lee'

accessorObj.name = 'kim';

accessorObj.name;
// => ???

const dataObj = {
  name: 'lee',
};

dataObj.name;
// => 'lee'
```

- get 함수는 key 값을 가져오므로 getter라고 한다. 
- `accessorObj.name = 'kim';`  과 같이 key 값을 수정하려해도 수정되지 않는다. 이는 name key에 해당하는 setter 기능이 없기 때문이다.

```javascript
const accessorObj = {
    _name: 'lee',
    get name() {
        return this._name;
    },
    set name(value) {
        this._name = value;
    }
}

accessorObj.name = 'kim';

accessorObj.name;
// => ???
```

- setter 함수는 지정된 값을 매개변수로 받는다.

```
const obj = {
  get name() {
    return this._name.toUpperCase();
  },
  set name(value) {
    this._name = value;
  },
  get id() {
    return this._id.toString(2); // Returns binary of a number
  },
  set id(value) {
    this._id = value;
  }
}

obj.name = 'Arfat';
obj.name;
// => 'ARFAT'

obj.id = 5;
obj.id;
// => '101
```

- 위 코드와 같이 getter 함수에서 값을 반환하기 전에 속성을 수정하거나 재정의 할 수 있다.

#### JavaScript는 접근자 프로퍼티와 데이터 프로퍼티를 어떻게 구별할 수 있을까?

##### 객체 속성 설명자(Object Property Descriptors)

- 객체의 키와 값은 일대일 매핑인것 같지만 사실은 그렇지 않다.



##### 프로퍼티 속성(Property Attributes)

- 객체의 모든 키에는 키와 관련된 값의 특정을 정의하는 attributes 집합이 포함되어 있다. 키-값 쌍을 설명하는 메타 데이터로 생각할 수도 있다.

- attributes는 객체의 프로퍼티의 상태를 정의하고 설명한다.



##### 총 6개의 property attributes가 있다.

- **[[Value]]**

  - 속성의 get 액세스로 검색된 값을 저장.
  - 아래 사진 예에서 object.x를 실행하면 [[Value]] 속성을 검색한다. 데이터 프로퍼티의 `.` , `[]`  접근방식 둘다 이러한 방식으로 동작한다.

- **[[Get]]**

  - getter property를 만드는 동안 선언한 함수에 대한 참조를 저장

- **[[Set]]**

  - setter property를 만드는 동안 선언한 함수에 대한 참조를 저장

  ```javascript
  const obj = {
    set x(val) {
      console.log(val) 
      // => 23
    }
  }
  obj.x = 23;
  ```

  - 위 예에서, 오른쪽 할당된 값은 val 인수로 setter 함수에 전달된다.

- **[[Writable]]**

  - boolean 값이다.
  - 값을 덮어 쓸 수 있는지 여부를 알려준다. false 일 경우 값 변경이 불가능하다.

- **[[Enumerable]] 중요!!**

  - boolean 값이다.
  - 프로퍼티가 `for-in` 루프에 나타날지 여부를 알려준다. true이면 `for-in` 루프가 해당 프로퍼티를 순회할 수 있다.

- **[[Configurable]]**

  - boolean 값이다.
  - false 일 경우
    - 프로퍼티를 삭제할 수 없다.
    - 데이터 프로퍼티와 접근자 프로퍼티 간 상호 변환이 불가능하다.
    - 다른 property attributes들을 변경할 수 없다 , 즉 enumerable, configurable, get 또는 set의 현재 값이 고정된다.
    - 프로퍼티가 데이터 프로퍼티인 경우, writable를 true에서 false로만 설정할 수 있다.
    - writable, configurable이 모두 false일 경우, 해당 프로퍼티는 값 변경이 불가능하고 삭제가 불가능 합니다.

- 각 프로퍼티 유형에  위 6개 property attributes가 모두 존재하지는 않는다.

  - 데이터 속성의 경우 value, writable, enumerable and configurable 값만 존재합니다
  - 접근 자 속성의 경우  get 과 set이 있습니다.

[[]] 이중 대괄호는 ECMA 사양에서 사용하는 내부 속성을 표시한다.  JS 프로그래머가 코드 내에서 직접 손댈 수없는 속성이다. 내부 속성을 조작하려면 언어에서 제공하는 메서드를 사용해야 한다.

![Image for post](https://miro.medium.com/1*iKJx57JU9sKdff-Os7upyA.png)

```javascript
const object = {
  x: 5,
  y: 6
};
Object.getOwnPropertyDescriptor(object, 'x');
/* 
{ 
  value: 5, 
  writable: true, 
  enumerable: true, 
  configurable: true 
}
*/
```



##### Object.getOwnPropertyDescriptor



##### Object.defineProperty





### Symbol 타입

- 유니크한 값

### Map

- 객체와 유사

- 객체에 비해 갖는 장점

- 어떤 타입이든 키 값으로 설정할 수 있다.

- 객체 수를 알고 싶을 때

  - 배열로 변환하는 방법 `object.keys(obj).length`
  - `for of 문` 사용
  - Map에서는 size라는 프로퍼티 속성을 제공한다.

  

### Set

- 배열과 유사
- 배열에 비해 갖는 장점
  - 중복 제거 가능
  - update, clear, delete 매서드 제공

- iterable를 반환한다.
- WeakSet - 원소 데이터 타입이 참조형이어야 한다.



#### Nullish coalescing operator, Optional chaining

Falsy 값에는 어떤 것들이 있는가?

- undefined, null, 0, '', NaN, false

```javascript
function changePerson (person) {
	person.age = 50;
  
  person = {
    name: 'kim',
    age: 100
  }
  
  return person;
}


const person1 = {
	name: 'lee',
  age: 27
};

const person2 = changePerson(person1);


console.log('person1', person1) // ?
console.log('person2', person2) // ?
```



```javascript
// key로 접근하는 방식, 시간 복잡도를 줄일 수 있다. 상황에 맞게 잘 사용해보자.
const obj = { person1: { name: 'lee' }, person2: { name: 'kim' }};
```

