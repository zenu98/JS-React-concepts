## 콜백 함수
---
파라미터 값이 주어질 때 2초 뒤에 10을 더해서 반환하는 함수를 구현하고 순차적으로 2초에 걸쳐서 10, 20, 30, 40과 같은 형태로 출력하는 콜백함수이다.

```javascript
 function increase(number, callback){

           setTimeout(() => {
               const result = number+10;
               callback(result)
           }, 2000)
       }
       console.log(`시작`);
       increase(0, c => {
           console.log(c);
           increase(c,c => {
               console.log(c);
               increase(c,c => {
                   console.log(c);
                   increase(c,c => {
                     console.log(c);
                     console.log('Done');
               })
           })
       })
         
 /*
 시작
 10
 20
 30
 40
 Done
 */
         
```
이렇게 콜백 안에 콜백을 넣어 구현하면 코드의 가독성이 나빠지는 '콜백 지옥'이 형성된다.

## Promise
---
Promise는 콜백 지옥 같은 코드가 형성되지 않게 하는 방안으로 ES6에 도입된 기능이다.

```javascript
function increase(number){
        const promise = new Promise((success, fail) =>{
            setTimeout(()=>{
                const result = number + 10;
                if(result>50){
                    const e = new Error('NumberTooBig');
                    return fail(e);
                }
                success(result);
            },2000);
        });
        return promise;
    }

 increase(0)
        .then(a => {
            console.log(a);
            return increase(a);
        })
        .then(a => {
            console.log(a);
            return increase(a);
        })
        .then(a => {
            console.log(a);
            return increase(a);
        })
        .then(a => {
            console.log(a);
            return increase(a);
        })
        .then(a => {
            console.log(a);
            return increase(a);
        })
        .catch(a=> {
            console.log(a);
        })
```
함수 안에 여러번 중첩시켜 감싸는 것이 아니라 .then을 사용하여 그 다음 작업을 설정하기 때문에 콜백 지옥이 형성되지 않는다.

## async/await
---
async/await은 Promise를 더욱 쉽게 사용할 수 있도록 해 주는 ES8 문법이다. 이 문법을 사용할 때에는 함수의 앞부분에 async 키워드를 추가하고, 해당 함수 내부에서 Promise의 앞부분에 await 키워드를 사용한다. 이렇게 하면 Promise가 끝날 때까지 기다리고, 결과 값을 특정 변수에 담을 수 있다.

```javascript
function increase(number){
        const promise = new Promise((success, fail) =>{
            setTimeout(()=>{
                const result = number + 10;
                if(result>50){
                    const e = new Error('NumberTooBig');
                    return fail(e);
                }
                success(result);
            },2000);
        });
        return promise;
    }

    async function runTasks(){
        try {   // try/catch 구문을 사용하여 에러를 처리한다.
            let result = await increase(0);
            console.log(result);
            result = await increase(result);
            console.log(result);
            result = await increase(result);
            console.log(result);
            result = await increase(result);
            console.log(result);
            result = await increase(result);
            console.log(result);
            result = await increase(result);
            console.log(result);
        } catch(a){console.log(a);}
    }
    runTasks();
```
