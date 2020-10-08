# 3. 값 타입과 참조 타입



값 타입 ===  passed by value === primitive types

참조 타입 === passed by reference === reference types



## primitives

원시 값이 할당된 변수를 다른 변수에 할당할 때 값이 복사되어 전달된다.

```javascript
const x = 10;
const y = 'abc';

const a = x;
const b = y;

console.log(x,y,a,b); // -> 10, 'abc', 10, 'abc'
```

서로 아무런 관계가 없기 때문에 한 변수에 다른 값이 할당되어도 다른 변수에는 아무런 변화가 없다.

```javascript
const x = 10;
const y = 'abc';

let a = x;
let b = y;

a = 5;
b = 'def';

console.log(x,y,a,b); // -> 10, 'abc', 5, 'def'
```



## Objects

원시 값이 아닌 값을 변수에 할당하면 **해당 값의 참조를 전달**한다. 해당 참조는 메모리의 저장된 객체의 주소 값을 가리킨다. 해당 변수는 실제로 그 값을 포함하고 있는 것이 아니다.

```javascript
const arr = [];
arr.push(1);
```

`arr = []`와 같은 코드를 작성하면 arr에는 메모리 주소가 저장된다. arr에 저장된 메모리 주소는 정적이다. 값을 push 하는 것과 같은 어떤 동작을 arr 변수에 행하면 자바스크립트 엔진은 arr이 가리키는 주소로 이동하고, 해당 주소에 저장된 값을 이용하여 작업합니다.



### Assigning by Reference

객체 타입은 값이 아닌 참조를 복사하여 전달한다.

```javascript
const reference = [1];
const refCopy = reference;
```



![Image for post](https://miro.medium.com/max/2919/1*d2W3ulHbHRGrFQ-c1SG5gA.png)



위 두 변수는 **모두 같은 참조를 가리키고 있다.**  변수가 가리키는 참조(주소)에 할당된 값을 변경하는 것이다.

```javascript
reference.push(2);
console.log(reference, refCopy) // -> [1,2], [1, 2]
```





### Reassigning a Reference

새로운 객체 할당 시 기존 참조 값이 새로운 참조 값으로 대체 된다.



### == and ===

동등연산자는 참조를 체킹하여 boolean 값을 반환한다. 

```javascript
var arrRef = [’Hi!’];
var arrRef2 = arrRef;
console.log(arrRef === arrRef2); // -> true
```



참조 값이 다른 경우 동일한 프로퍼티를 포함하여도 동등연산자는 false를 반환한다.

```javascript
var arr1 = ['Hi!'];
var arr2 = ['Hi!'];
console.log(arr1 === arr2); // -> false
```



### Passing Parameters through Functions

원시 값을 함수에 파라미터로 전달 시 함수의 매개변수는 전달된 파라미터 값을 복사한다. `=` 할당연산자를 통해 원시 값을 할당하는 것과 같은 효과이다.



### Pure Functions

함수 외부에 영향을 주지 않는 함수를 순수함수라고 한다. 함수가 매개변수로 원시 값만 취하고 함수 스코프 외부에 다른 변수 값을 조작하지 않는 한 순수함수이다. 

하지만, Object를 매개변수로 취하는 함수는 스코프 외부에 상태를 변경할 수 있다. 함수 내부에서 해당 객체의 값을 변경하면 해당 객체를 참조하는 스코프 외부에 변수도 변경되는 부작용이 발생할  수 있다.

이런, 부작용을 방지하고 함수를 순수함수로 작성하기위해 Array.map과 같은 배열 함수는 배열 참조를 받아 내부적으로 배열을 복사하고 원본 대신 복사본으로 작업 한다. 이렇게하면 원본이 변경되지 않고 함수 스코프 외부 범위가 영향 받지 않으며, 새로운 배열에 대한 참조가 반환된다.

```javascript
// 비순수함수
function changeAgeImpure(person) {
    person.age = 25;
    return person;
}
const alex = {
    name: 'Alex',
    age: 30
};
const changedAlex = changeAgeImpure(alex);
console.log(alex); // -> { name: 'Alex', age: 25 }
console.log(changedAlex); // -> { name: 'Alex', age: 25 }
```



```javascript
// 순수함수
function changeAgePure(person) {
    var newPersonObj = JSON.parse(JSON.stringify(person));
    newPersonObj.age = 25;
    return newPersonObj;
}
const alex = {
    name: 'Alex',
    age: 30
};
const alexChanged = changeAgePure(alex);
console.log(alex); // -> { name: 'Alex', age: 30 }
console.log(alexChanged); // -> { name: 'Alex', age: 25 }
```

위 코드에서 `JSON.parse(JSON.stringify(person))` 로 원본 객체를 복사한 새로운 객체를 생성. 새로운 객체는 원본과 동일한 속성을 갖고 있지만 메모리 상에서는 분리된 객체이다.



 [Explaining Value vs. Reference in Javascript](https://codeburst.io/explaining-value-vs-reference-in-javascript-647a975e12a0)