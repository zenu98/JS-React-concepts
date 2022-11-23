`React.memo`가 객체 외에 `prop` 값에도
작동하도록 도와주는 것이 `useCallback` 훅이다.

`useCallback` 훅은 기본적으로
컴포넌트 실행 전반에 걸쳐 함수를 저장할 수 있게 하는 훅으로
리액트에, 우리는 이 함수를 저장할 것이고
매번 실행때마다 이 함수를 재생성할 필요가 없다는걸 알려줄 수 있다.

이렇게 하면, 동일한 함수 객체가 메모리의 동일한 위치에 저장되므로
이를 통한 비교 작업을 할 수 있다.

```javascript
let obj1 = {};
let obj2 = {};
obj1 === obj2;   // false
```
이 둘은 비슷해보일지 모르지만 자바스크립트에서 이 둘은 같은 것이 아니다. 하지만, `obj2`와 `obj1`이 같은 메모리 안의 같은 위치를 가리키고 있다면 이 두 객체는 같은 객체로 간주된다.

이것이 `useCallback`이 하는 일로 우리가 선택한 함수를
리액트의 내부 저장 공간에 저장해서 함수 객체가 실행될 때마다 이를 재사용할 수 있게 된다.

사용법은 간단하다.
`useCallback` 훅을 사용하여 함수를 첫 번째 인자로 전달하면
`useCallback`은 이 저장된 함수를 반환해준다.
그리고, 이 `App` 함수가 다시 실행되면 `useCallback`이 리액트가 저장한 함수를 찾은 뒤에, 찾은 함수 객체를 재사용한다.
따라서, 어떤 함수가 절대 변경되어서는 안된다면
이 `useCallback`을 사용해 함수를 저장하면 된다.

```javascript
import React, { useState, useCallback } from "react";
import Button from "./components/UI/Button/Button";
import "./App.css";
import DemoOutput from "./components/Demo/DemoOutput";

function App() {
  const [showParagraph, setShowParagraph] = useState(false);

  console.log("APP RUNNING !");

  //useCallback()
  const toggle = useCallback(() => {
    setShowParagraph((prevShowParagraph) => !prevShowParagraph);
  }, []);

  return (
    <div className="app">
      <h1>Hi there!</h1>
      <DemoOutput show={false} />
      <Button onClick={toggle}>Toggle</Button>
    </div>
  );
}

export default App;

```

`useEffect`와 마찬가지로, `useCallback`은 두 번째 인자를 필요로 하며,두 번째 인자는 배열이어야 한다.
`useCallback` 호출에 대한 의존성(Dependency) 배열이죠
이 의존성은 `useEffect`와 같은 것을 의미한다.
함수를 감싼 컴포넌트로부터 전달받는 모든 것을 사용할 수 있다.
즉, 상태나 `props`, 컨텍스트를 지정할 수 있다.
여기에서는 업데이트 함수의 상태만을 명시하면 된다.

두번재 인자에 빈 의존성 배열이 있는데, 이 배열은 리액트에
`toggle`에 저장하려고 하는 콜백 함수는 절대 변경되지 않을 것이라고
리액트에 알려주는 배열이다, 따라서 `App` 컴포넌트가 다시 렌더링되어도
항상 같은 함수 객체가 사용되게끔 한다.

그러면, 이를 저장하고 새로고침하면 Button RUNNING ! 문구는 처음 한 번만 보이고, 그 뒤로 버튼을 클릭해도 다시 나타나지 않는다.

![](https://velog.velcdn.com/images/zenu98/post/5ce1ff8a-5f87-477c-a44f-39f100a34292/image.png)


`useCallback` 덕분에 이 `toggle` 객체가 메모리 안에서 항상 같은 객체임을 보증하기 때문에 우리가 전달한 모든 `props` 값이 비교가 가능하게 됐고, `React.memo`가 제 역할을 수행할 수 있게 되었다.


자, 다시 `useCallback`으로 돌아가보면 우리는 `useCallback`을 사용하면 함수를 저장하고 이를 재사용할 수 있다는 것을 알았다.
이제 `useCallback`의 두번째 인자로 들어가는 기묘한 의존성 배열을 지정해야 하는데, 처음 이를 보다 보면 이게 왜 필요한지 이해하기 위해서는 다시 자바스크립트로 돌아가 클로저(Closer)에 대한 내용을 가져와야 한다. 

클로저를 쉽게 풀어서 정의한다면 **이미 생명 주기가 끝난 외부 함수의 변수를 참조하는 함수를 클로저라고 한다.**

구체적인 예시를 구현해보자.

```javascript
import React, { useState, useCallback } from "react";
import Button from "./components/UI/Button/Button";
import "./App.css";
import DemoOutput from "./components/Demo/DemoOutput";

function App() {
  const [showParagraph, setShowParagraph] = useState(false);
  const [allowToggle, setAllowToggle] = useState(false);

  console.log("APP RUNNING !");

  const toggle = useCallback(() => {
    if (allowToggle) {
      setShowParagraph((prevShowParagraph) => !prevShowParagraph);
    }
  }, []);

  const allowToggleHandler = () => {
    setAllowToggle(true);
  };

  return (
    <div className="app">
      <h1>Hi there!</h1>
      <DemoOutput show={showParagraph} />
      <Button onClick={toggle}>Toggle</Button>
      <Button onClick={allowToggleHandler}>Allow Toggle</Button>
    </div>
  );
}

export default App;


```
![](https://velog.velcdn.com/images/zenu98/post/3e24f2e1-d4a8-4a37-8332-e867104b1e2c/image.png)

이렇게 하면, `App`이 새로고침이 된 후 Toggle 버튼을 누르게 되면
아무것도 발생하지 않고 Allow Toggle을 누르면 다시 Toggle 버튼을 눌렀을 때 제대로 작동해야 한다.

하지만 실제로 실행해 본다면 예상과는 다르게 Toggle 버튼이 작동하지 않게 된다.

이유는, 자바스크립트에서 함수는 클로저이고 `useCallback`을 제대로 사용하지 않아서이다.

![](https://velog.velcdn.com/images/zenu98/post/3cacb87e-7eef-4291-a792-207ab06a02a7/image.png)
실제 코드를 살펴보면 useCallbakc()의 두번째 인자에 들어가 있는 빈 배열에 경고 밑줄이 그어져 있는 것이 보인다.

자바스크립트의 함수는 클로저입니다 이 말은, 함수가 정의가 되면
`toggle` 함수가 정의될 때 자바스크립트는 함수 외부에서 사용하는 모든 변수를 잠그게 된다. 
여기에서는 `allowToggle`이 이에 해당하는데
이 것은 `App` 함수에 있는 변수나 상수이고, 이를 내부함수 안에서
사용하고 있다. 
따라서, 자바스크립트는 이 상수에 클로저를 만들고
함수를 정의할 때 사용하기 위해 상수를 저장한다.

이렇게 되면, 다음에`toggle`이 실행이 되면 이 저장된 상수를 그대로 사용하게 된다. 따라서, 이 변수의 값은 변수가 저장된 시점의 값을 사용하게 되는 것이다.

일반적으로 이는 완벽한 기능이다. 이 기능을 사용하면 함수 밖의 변수를 함수 안에서 쓸 수 있으며 우리가 원하는 시점에 함수를 호출할 수 있다. 버튼에 바인딩한 함수를 구현했던 것 처럼 말이다.

![](https://velog.velcdn.com/images/zenu98/post/b96818b8-b4c5-4818-a673-5b7659fda570/image.png)


여기서 문제는, 우리는 `useCallback`을 사용하여 리액트에게 해당 함수를 저장하라고 지시를 한 부분이다. 이러면 그 함수는 메모리 어딘가에 저장된다.

즉, `App` 함수가 `Toggle`의 상태가 변경되어 재평가, 재실행되면
리액트는 이 함수를 재생성하지 않는다는 것이다.
왜냐하면 우리가 `useCallback`을 통해 리액트에게 어떤 환경에서든 함수 재생성을 하지 않게 막았기 때문이다.
따라서, 리액트가 이 함수에 사용하기 위해 저장한
`allowToggle`의 값은 최신의 값이 아니고, `App` 컴포넌트가
처음 실행된 시점의 값을 저장하고 있는 것이다. 여기서 문제가 발생한다.

함수 재생성을 필요로 하는 때가 있을 수가 있다.
이 함수에서 사용하는, 함수 외부에서 오는 값이 변경되었을 수 있다.

![](https://velog.velcdn.com/images/zenu98/post/5acfc2c9-3870-4363-a1dc-e5ff5fcff76b/image.png)

그래서 우리는 의존성 배열에 이 `allowToggle`을 종속 형태로 추가해주어야 한다. 이것으로 우리는 리액트에게 이 함수를 저장해달라고 요청하는 것이 가능하다. 만약, `allowToggle`의 값이 바뀌고 새로운 값이 들어오면, 우리는 함수를 재생성하고 이 새로 만든 함수를 저장할 수 있다.
이렇게 하면 우리는 함수에 항상 `allowToggle`의 최신 값만을
사용할 수 있다.

이제 Allow Toggle 버튼을 누르고 Toggle 버튼을 다시 누르면
우리가 원했던 출력이 정상적으로 작동하는 것을 볼 수 있다.
![](https://velog.velcdn.com/images/zenu98/post/502e2c5b-221b-4f68-8603-7420eae9c760/image.png)