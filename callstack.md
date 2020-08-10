# 1. 콜스택 (33가지 개념)

[https://github.com/yjs03057/33-js-concepts#1-%ED%98%B8%EC%B6%9C-%EC%8A%A4%ED%83%9D](https://github.com/yjs03057/33-js-concepts#1-호출-스택)



자바스크립트는 한 번에 하나의 작업을 처리하거나 하나의 코드 조각을 처리할 수 있는 **단일 스레드 언어**이다.

힙, 큐와 같은 부분이 자바스크립트 동시성 모델을 구현하는 **단일 호출 스택**이 있다.

one thread === one call stack === one thing at a time



### 1. 콜스택(Call Stack)

- 싱글 스레드, 동기적 코드 실행

- **LIFO 방식으로 함수 호출을 기록하는 데이터 구조**이다. 실행할 함수를 호출하면 스택에 push하고 함수 실행이 종료되면 스택의 맨 위를 pop한다.

- **임시저장 역할**: 함수가 호출되면 함수, 매개 변수 및 변수가 **스택 프레임(콜스택의 각 항목)**을 형성하기 위해 호출 스택으로 푸시된다. 이 **스택 프레임**은 스택의 메모리 위치이다. 스택에서 팝되어 함수가 반환되면 메모리에서 지워진다. 

  (코드 실행 중에 생성된 모든 실행 컨텍스트를 임시 저장)

- **함수 호출 관리 역할**:  콜스택은 각 스택 프레임의 위치에 대한 기록을 유지한다. 콜스택은 다음 실행해야 할 함수를 알고 있다. 콜스택의 이 역할이 자바스크립트 코드 실행을 **동기적**으로 만드는 것이다.

### 2. 힙 (Heap)

- 구조화 되지 않은 메모리 영역, **변수와 객체에 대한 모든 메모리 할당이 발생하는 곳.**

### 3. 큐 (Queue)

- 자바스크립트 런타임에서 처리할 목록의 대기열. 스택이 비어있으면 대기하고 있는 다음 함수를 대기열에서 꺼내 처리한다. 대부분 외부 비동기 이벤트에 대한 응답들이 큐에서 대기한다.  (FIFO)

- 큐의 종류는 크게 마이크로태스크큐(이벤트 큐, 콜백 큐), 애니메이션 큐, 매크로태스트큐(잡 큐)가 있다.

  - 마이크로태스크큐

    - 대표적으로 promise가 마이크로태스크큐로 동작한다.

  - 애니메이션 큐

    - requestAnimationFrame, requestidlecallback이 있다.

  - 매크로태스크큐

    - 대표적으로 setTimeout이 있다.

  - 실행순서 예제

    - ```javascript
      // 아래 코드의 실행결과는??
      
      console.log('script start');
      
      Promise.resolve()
      	.then(() => console.log('promise1'))
      	.then(() => console.log('promise2'))
      
      setTimeout(() => { console.log('timeOut')}, 1000);
      
      console.log('script end');
      ```

    - 실행순서: 마이크로태스크큐 => 애니메이션 큐 => 매크로태스크 큐

### 4. 이벤트 루프 (Event Loop)

- Javascript에서는 네트워크 요청을 AJAX를 통해 비동기적으로 서버 요청을 수행할 수 있다. 그런데 네트워크 요청이 **동기적으로 요청이 되었다고 가정**하면 자바스크립트 와 같은 단일 스레드 언어에서는 한번에 하나의 요청만 처리할 수 있기 때문에 **다른 요청이 처리될 때까지 사용자는 아무것도 할 수 없을 것**이다. 가장 쉬운 해결책은 비동기 콜백을 사용하는 것이다.  비동기 콜백은 즉시 실행되지 않고 나중에 실행될 예정이므로 동기 함수와 달리 스택 내에서 즉시 푸시 할 수 없다. 그렇다면 비동기 콜백은 대체 어디로 가고 어떻게 처리되는 것일까?

- caller와 response의 느슨한 결합도는 자바스크립트 런타임이 비동기 작업이 완료되고 콜백이 실행되기를 기다리는 동안 다른 작업을 할 수 있게 해준다.
- 브라우저 api가 실행되면 기본적으로 DOM 이벤트, http 요청, setTimeout 등과 같은 비동기 이벤트를 처리하기 위해 C++에서 구현된 **브라우저에 의해 생성된 스레드인 WebAPI를 호출한다.**
- WebAPI는 실행이 완료되면 큐대기열에 콜백을 푸시한다. -> 이벤트 루프는 큐대기열에서 콜백을 실행하고 스택이 비어있을 때 스택에 푸시한다.
- **이벤트 루프에 핵심 역할은 스택과 태스크 큐를 보고 있다가 스택이 비어있을 때 큐대기열의 첫번째 항목을 스택으로 푸시하는 것이다.**



### 5. 실행 컨텍스트 (execution context)

- Javascript 코드가 **평가되고 실행되는 환경**의 추상 개념입니다. 코드가 자바 스크립트에서 실행될 때마다 실행 컨텍스트 내에서 실행됩니다.

- #### 실행 컨텍스트 3가지 종류

  - 전역 실행 컨텍스트
    - window 객체를 생성하고 이 값을 전역 객체와 동일하게 설정한다. 프로그램에는 전역 실행 컨텍스트가 하나만 존재할 수 있다.
  - 함수 실행 컨텍스트
    - 함수가 호출 될 때마가 함수에 대한 새로운 실행 컨텍스트가 생성된다.

- JavaScript 엔진이 처음 스크립트를 발견하면 **전역 실행 컨텍스트를 생성하고이를 현재 실행 스택으로 푸시**합니다. **JavaScript  엔진이 함수를 호출할 때마다 해당 함수에 대한 새 실행 컨텍스트를 만들고 스택의 맨 위로 푸시**합니다.

- #### 실행 컨텍스트는 어떻게 만들어지는가?

  - 실행 컨텍스트는 1) 생성 단계,  2) 실행 단계의 두 단계로 생성됩니다.

  - 생성단계

    - 생성단계에서는 아래 두가지 작업이 발생합니다

      - LexicalEnvironment 구성 요소가 생성됩니다.

        - 식별자 - 변수 매핑을 보유하는 구조 . 식별자(변수명 / 함수명), 변수(객체 또는 원시값에 대한 참조)

        - LexicalEnvironment는 3개의 컴포넌트로 구성된다.

          - Environment Record

            - 변수 및 함수 선언이 LexicalEnvironment 내부에 저장되는 장소.

          - Reference to the outer environment

            - outer lexical environment에 접근 할 수 있음을 의미합니다. 즉, JavaScript 엔진은 현재 LexicalEnvironment에서 찾을 수없는 경우 외부 환경 내에서 변수를 찾을 수 있습니다.

          - This binding

            - 함수가 호출되는 방식에 의해 결정된다.

            - 객체 참조에 의해 호출되면 this의 값이 해당 객체로 설정된다.

            -  lexing 단계에서 this가 바인딩 된다.
      
            - ```
              const person = {
                name: 'peter',
                birthYear: 1994,
                calcAge: function() {
                  console.log(2018 - this.birthYear);
                }
              }
              person.calcAge(); 
              // ?
              
          const calculateAge = person.calcAge;
              calculateAge();
          // ?
              ```
      
          - LexicalEnvironment를 의사 코드로 표현하면 아래와 같다.
      
            - ```
              GlobalExectionContext = {
                LexicalEnvironment: {
                  EnvironmentRecord: {
                    Type: "Object",
                    // Identifier bindings go here
                  }
                  outer: <null>,
                  this: <global object>
                }
              }
              FunctionExectionContext = {
                LexicalEnvironment: {
                  EnvironmentRecord: {
                    Type: "Declarative",
                    // Identifier bindings go here
                  }
                  outer: <Global or outer function environment reference>,
              this: <depends on how function is called>
                }
              }
              ```
      
        - ```
          var a = 20;
          var b = 40;
          function foo() {
            console.log('bar');
          }
          
          // 위 코드에 lexicalEnvironment
          lexicalEnvironment = {
            a: 20,
        b: 40,
            foo: <ref. to foo function>
      }
          ```
    
      - VariableEnvironment 구성 요소가 생성됩니다.
    
        - var 변수 바인딩을 저장하는데 사용
  
  - 실행단계
  
    - **모든 변수에 대한 할당이 완료되고 코드가 최종적으로 실행**됩니다.

### 참고 : 스택오버플로우(stack overflow)

- 스택 오버플로는 종료 지점이없는 재귀 함수 (자신을 호출하는 함수)가있을 때 발생한다. 브라우저 (호스팅 환경)에는 스택 오류가 발생하기 전에 수용 할 수있는 최대 스택 호출이 있다.

![Image for post](https://miro.medium.com/max/748/1*-MMBHKy_ZxCrouecRqvsBg.png)

#### 참고: 클로저의 단점

- 앱 종료 시까지 메모리에 남아있기 때문에 성능에 악영향을 준다.



### 참고: TDZ (Temporal Dead Zone) - 일시적 사각지대

- `const`, `let`, `class` 구문의 유효성에 영향을 미치는 중요한 개념

- `var`, `function`, `import` 는 TDZ에 영향을 받지 않고 현재 스코프 내에서 호이스팅 된다.

  - 선언 전에 접근하여도 error를 발생시키지 않는다.

  - ```javascript
    // Works!
    greet('World'); // => 'Hello, World!'
    function greet(who) {
      return `Hello, ${who}!`;
    }
    
    // Works!
    greet('Earth'); // => 'Hello, Earth!'
    ```

- TDZ는 선언 전에 변수를 사용하는 것을 허용하지 않는다.

```javascript
const name = 'LEE';

name; // => 'LEE'
```

```javascript
name; // throws `ReferenceError`

const name = 'LEE';
```

const name = 'LEE' 구문 전까지, name 변수는 TDZ에 있다.

TDZ에 있는 `name` 변수에 접근하게 되면 , `ReferenceError: Cannot access 'name' before initialization` 자바스크립트 에러가 발생한다.



### 콜스택 예제 코드

```
function firstFunction(){
  console.log("Hello from firstFunction");
}

function secondFunction(){
  firstFunction();
  console.log("The end from secondFunction");
}

secondFunction();

// 결과는?

...

// 답
1. secondFunction이 실행되고 비어있는 스택프레임이 생성된다. 이 스택프레임은 프로그램의 주요 시작점이 된다.
2. secondFunction이 firstFuction을 호출하고 스택으로 push한다.
3. firstFuction이 반환되면서 Hello from firstFunction을 콘솔에 찍는다.
4. firstFuction이 스택에서 pop된다.
5. 실행순서는 secondFunction으로 이동한다.
6. secondFunction이 반환되면서 The end from secondFunction을 콘솔에 찍는다.
7.secondFunction이 스택에서 pop되고 메모리를 비운다.
```



참고

[실행컨텍스트](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)

[콜스택, 이벤트 루프](https://medium.com/@gaurav.pandvia/understanding-javascript-function-executions-tasks-event-loop-call-stack-more-part-1-5683dea1f5ec)

[콜스택](https://www.freecodecamp.org/news/understanding-the-javascript-call-stack-861e41ae61d4/)

[이벤트 루프란 무엇인가? - 영상](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

[매크로태스크큐, 마이크로태스크 큐](https://ko.javascript.info/event-loop)

[TDZ](https://ui.toast.com/weekly-pick/ko_20191014/)

[requestAnimationFrame](https://developer.mozilla.org/ko/docs/Web/API/Window/requestAnimationFrame)