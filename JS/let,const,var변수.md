# 변수선언
![](https://velog.velcdn.com/images/zenu98/post/f8834fc8-7407-4312-afec-2c868290641b/image.png)

## 1.
var,let,const와 같은 변수이름으로 선언을 하게 되면 변수이름과 확보된 메모리저장 주소와 연결한다.
이 메모리 공간은 해제되기 전까지 보호된다.

## 2.
자바스크립트의 변수는 선언 -> 초기화 단계로 수행된다.
- 선언 단계: 변수 이름을 실행 컨텍스트에 등록해서 자바스크립트 엔진에 변수의 존재를 알린다. 

- 초기화 단계: 값을 저장하기 위한 메모리 공간을 확보하고, 암묵적으로 undefined를 할당해 초기화한다.

    (초기화 단계를 거치지 않으면, 확보된 메모리 공간에는 이전에 사용했던 쓰레기 값이 남아있을 수 있다. 자바스크립트 엔진은 암묵적으로 초기화를 수행한다.)
    
 


var
변수를 선언. 추가로 동시에 값을 초기화.

let
블록 스코프 지역 변수를 선언. 추가로 동시에 값을 초기화.

const
블록 스코프 읽기 전용 상수를 선언.

자바스크립트에서 변수 선언은 선언 → 초기화 단계를 거쳐 수행된다.

선언 단계: 변수명을 등록하여 자바스크립트 엔진에 변수의 존재를 알린다.
초기화 단계: 값을 저장하기 위한 메모리 공간을 확보하고 암묵적으로 undefined를 할당해 초기화한다.

## 3.
변수 선언은 코드가 한 줄씩 순차적으로 실행되기 이전에, 소스코드의 평가 과정에서 완료된다
 - 소스코드의 평가 과정에서 자바스크립트 엔진은 변수 선언을 포함해 모든 선언문을 찾아내 먼저 실행한다. 즉, 변수 선언이 어디에 있든 상관없이 다른 코드보다 먼저 실행되는 특징을 호이스팅(hoisting)이라 한다. 소스코드의 평가 과정 이후, 소스코드를 한 줄씩 순차적으로 실행하게 된다.
 

var 키워드를 이용한 변수 선언은 선언 단계와 초기화 단계가 동시에 진행되어, 변수에 암묵적으로 undefined를 할당해 초기화한다.
이 과정에서 호이스팅(hoisting)으로 인해 console을 먼저 찍어도 반환 값이 undefined로 나온다.
따라서 var은 변수 선언문 이전에 변수를 참조할 수 있다.
```javascript
console.log(one); // undefined. ReferenceError(참조 에러)가 아닌 undefined 출력
var one;
```

let 로 선언된 변수는 선언 단계와 초기화 단계가 분리되어 진행된다.
스코프의 선두에서 선언단계가 실행되지만 초기화는 되지 않았기 때문에
참조 에러가 뜨게된다.
```javascript
console.log(one); // ReferenceError(참조 에러)
let one;
```

변수 선언에서 const와 let의 차이로는 const는 반드시 선언과 초기화를 동시에 진행되어야 한다는 점이다.
```javascript
const one; // Uncaught SyntaxError: Missing initializer in const declaration

const one = 1;
```
## 4.
변수에 값을 할당할 때는 소스코드의 평가 과정 이후, 소스코드가 순차적으로 실행되는 시점인 런타임에 실행된다.

```javascript
var one = 1;
```


- 변수 선언+값의 할당을 하나의 문으로 단축해도, 소스코드 평가 과정에서 변수 선언이, 런타임 시점에 값의 할당이 이루어진다.

- 변수에 값을 할당할 때는, 기존에 undefined가 할당된 공간을 비우고 그 공간에 100을 저장하는 게 아니라, 새로운 메모리 공간을 확보하고 그곳에 할당 값 100을 저장한다.

- 더 이상 메모리를 사용할 필요가 없으면 변수에 null을 할당해주면 된다. 

---
### var:변수 재선언 가능
```javascript
var one=1;
console.log(one); //변수선언

var one=2;
console.log(one); //변수재선언

```


### let: 변수 재선언 불가능, 재할당 가능
```javascript
let one=1;
console.log(one); // 변수선언

one=2;
console.log(one); // 변수 재할당

let one=3;
console.log(one); // Uncaught SyntaxError: Identifier 'variable' has already been declaredㅣ
```
### const: 변수 재선언 불가능, 재할당 불가능
```javascript
const one=1;
console.log(one); // 변수선언

const=2;
console.log(one); // 변수 재할당 error

let one=3;
console.log(one); // 변수 재선언 error

```
const는 let과 다르게 반드시 선언과 초기화가 동시에 진행되어야 한다.
```javascript
let one;
one='1';

const name; // output: Uncaught SyntaxError: Missing initializer in const declaration
const name = 'seo'
```
원시값의 대한 재할당은 불가능하지만 객체는 가능하다.


```javascript
const name = 'seo'
name = 'kim' // output: Uncaught TypeError: Assignment to constant variable.

// 객체의 재할당
const name = {
  eng: 'kim',
}
name.eng = 'seo'

console.log(name) // output: { eng: "seo" }
```

### 블록 레벨 스코프

##### var : 함수 레벨 스코프 (function-level scope)
```javascript
if (true) {
  var x = 5;
}
console.log(x); // 5
```
수 내에서 선언된 변수는 함수 내에서만 유효하며 함수 외부에서는 참조할 수 없음. 
즉, 함수 내부에서 선언한 변수는 지역 변수이고 함수 외부에서 선언한 변수는 모두 전역 변수로 취급됨.


#####  let, const : 블록 레벨 스코프 (block-level scope)
```javascript
if (true) {
  let y = 5;
}
console.log(y); // ReferenceError: y is not defined
```
함수, if문, for문, while문, try/catch문 등의 모든 코드 블록 ({...}) 내부에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조할 수 없음.
즉, 코드 블록 내부에서 선언한 변수는 지역 변수로 취급됨.


### 정리
---
변수 선언에는 기본적으로 재할당이 필요하지 않는 상수와 객체에는 const를 사용하고, 재할당이 필요한 경우에 한정해 let 을 사용하는 것이 좋다.기본적으로 변수의 스코프는 최대한 좁게 만드는 것을 권장한다. 따라서, var 키워드는 지양하도록 하자.


#### 참고
- 자바스크립트 Deep Dive, 이웅모 (2020) 
- https://www.howdy-mj.me/javascript/var-let-const/
