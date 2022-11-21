



```javascript
// in App.js

import React, { useState } from "react";
import Button from "./components/UI/Button/Button";

import "./App.css";

function App() {
  const [showParagraph, setShowParagraph] = useState(false);

  console.log("APP RUNNING !");

  const toggle = () => {
    setShowParagraph((prevShowParagraph) => !prevShowParagraph);
  };
  
  return (
    <div className="app">
      <h1>Hi there!</h1>
      {showParagraph && <p>This is new!</p>}
      <Button onClick={toggle}>Toggle</Button>
    </div>
  );
}

export default App;


```

```javascript
// in Button.js

import React from "react";

import classes from "./Button.module.css";

const Button = (props) => {
  return (
    <button
      type={props.type || "button"}
      className={`${classes.button} ${props.className}`}
      onClick={props.onClick}
      disabled={props.disabled}
    >
      {props.children}
    </button>
  );
};

export default Button;

```
![](https://velog.velcdn.com/images/zenu98/post/b0a2014d-185a-4630-b4da-4cc30ef959db/image.png)

Toggle 버튼을 누르면 문장이 나타나고 다시 버튼을 누르면 문장이 사라지는 간단한 로직을 구현했다.

![](https://velog.velcdn.com/images/zenu98/post/64e1d79a-4757-41b3-a328-e8aabd29d9f0/image.png)

이제 작동방식에 대해 이야기해보자.
리액트는 state,props,컨텍스트 변경에 따라 컴포넌트 함수를 다시 실행시킨다. 따라서 APP.js에 추가된 `console.log(APP RUNNING!)` 문을 통해 이를 확인할 수 있다.

![](https://velog.velcdn.com/images/zenu98/post/6e26f9fd-9b4f-450d-b951-1373ca40caeb/image.png)

App 컴포넌트가 최초 실행될 때 리액트에서 첫 렌더링이 시작되고 APP RUNNING 문구가 표시된다. 이후 Toggle 버튼을 누를 때 마다 APP RUNNING 문구가 출력되는데 즉, 리액트는 매 번 상태가 변경이 되면 컴포넌트 전체를 재실행(re-executed), 재평가(re-eveluated) 한다는 사실을 확인 할 수 있다.

여기서 더 나아가 로직을 살짝 변경해 보도록 하겠다.

```javascript
// in DemoOutput.js

import React from "react";

const DemoOutput = (props) => {
  
  return <p>{props.show ? "This is new!" : ""}</p>;
};
export default DemoOutput;


```

```javascript
// in App.js

import React, { useState } from "react";
import Button from "./components/UI/Button/Button";
import "./App.css";
import DemoOutput from "./components/Demo/DemoOutput"; 

function App() {
  
  (...)
   
  return (
    <div className="app">
      <h1>Hi there!</h1>
      <DemoOutput show={showParagraph} />
      <Button onClick={toggle}>Toggle</Button>
    </div>
  );
}

export default App;

```

기존에 This is new! 문장을 출력하는 구문을 `DemoOutPut` 이라는 자식 컴포넌트를 만들어 로직을 변경했다. `DemoOutPut` 안의 `<p>` 단락 요소는 항상 렌더링 되지만 그 안의 텍스트는 `props.show`의 값에 따라 바뀐다.
`show`에 전달하는 값은 false라는 `boolean`값을 기본으로 가지고 있는 `showParagraph`이다.

이 로직은 전과 동일하게 작동하는데 전과 달리 실제 변경은 `App.js`가 아닌 `DemoOutput.js`에서 발생하지만 이에 대한 상태를 관리하는 App 컴포넌트 역시 다시 실행된다는 것을 기억해야 한다. 다시 말하지만 state,props,컨텍스트를 가지고 있고 이러한 것들을 가지고 있는 컴포넌트는 재실행, 재평가 된다.

`DemoOutput`의 `props`는
왜 `DemoOutput`이 다시 렌더링되는지를 결정한다.
하지만, 궁극적으로 이 `props.show`에서 얻을 수 있는 값은
우리가 `ShowParagraph`의 상태에서 관리하는 값이고
이것이 결론적으로 리액트에서의 상태와 상태 변화로 이어진다.

```javascript
// in DemoOutput.js

import React from "react";

const DemoOutput = (props) => {
  console.log("DemoOutput RUNNING!");
  return <p>{props.show ? "This is new!" : ""}</p>;
};
export default DemoOutput;


```

이제 `DemoOutput.js`에서도 DemOutput RUNNING! 콘솔로그를 추가한다면 첫 시작 화면에서 컴포넌트가 첫 렌더링을 시작하기 때문에 두 문장을 볼 수 있고 그리고 버튼을 클릭하면 또 이 콘솔 로그들이 실행된다.
매 클릭때마다 `props`가 변경되기 때문이다


![](https://velog.velcdn.com/images/zenu98/post/33bcee62-4e60-46dd-b0dc-61f60fdaeff3/image.png)

이제 조금 더 복잡한 이야기로 들어가보자
`App.js`를 살짝 수정해보자

```javascript
// in App.js

(...)
 
  return (
    <div className="app">
      <h1>Hi there!</h1>
      <DemoOutput show={false} />  // edited
      <Button onClick={toggle}>Toggle</Button>
    </div>
  );
}

export default App;

```
이제 `DemoOutput`에 전달하는 값인 `show`를 false로 고정하였다.
이 말은, 이것은 `ShowParagraph`로부터 떨어져 나갔으므로
더 이상 결과를 보여주지 않겠다는 것이다.
실제로 `ShowParagraph`를 사용하지 않고 있다.
변경은 하고 있지만, 상태 값은 사용하지 않기 때문이다.

이제 다시 App을 새로고침한 후 콘솔로그창을 확인해보자

![](https://velog.velcdn.com/images/zenu98/post/45c0b91a-9519-4e7b-95a3-948526b19a38/image.png)

App이 준비되었고, 두 컴포넌트 모두 렌더링 되었기에 두 문구가 뜨는 것은 이상하지 않다. 이제 버튼을 클릭해보자

![](https://velog.velcdn.com/images/zenu98/post/9287d674-e216-412a-8fe5-ade56ccc3b61/image.png)

자, 클릭을 하면 APP RUNNING이 보입니다 상태가 바뀌었기 때문이다
하지만 DemoOutput RUNNING 역시 표시된다. 
`props`는 바뀐 게 없는데도 불구하고 말이다
`props.show`를 사용하여 이 값은 여전히 false임에도 말이다
이 값은 변하지 않는데도 왜 DemoOutput이 재실행된 것일까?



```javascript
// in App.js

import React, { useState } from "react";
import Button from "./components/UI/Button/Button";
import "./App.css";
import DemoOutput from "./components/Demo/DemoOutput";

function App() {
  const [showParagraph, setShowParagraph] = useState(false);

  console.log("APP RUNNING !");

  const toggle = () => {
    setShowParagraph((prevShowParagraph) => !prevShowParagraph);
  };
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
App 함수는 상태가 변경되었기 때문에 재실행된다.
그럼, App 함수의 부분에는 뭐가 있을까?
당연히 반환문이 있고
이것은 JSX 코드를 반환하게 된다.
여기에 있는 모든 JSX 요소들은
결국 컴포넌트 함수에 대한
함수 호출과 같다.

그러니까, `DemoOutput` 컴포넌트에 대한 함수를 호출하고
`Button` 컴포넌트에 대한 함수를 출력한다.

이 것이 자식 컴포넌트들 역시 다시 실행되고
재평가되는 이유이다, 부모 컴포넌트들이 변경되었고
자식 컴포넌트는 부모 컴포넌트의 일부분이니까.

부모 컴포넌트 함수가 재실행되면
마찬가지로 자식 컴포넌트 함수도 재실행됩니다

따라서 `props`의 값은 여기서는 상관이 없는 것이다.

그저 부모 컴포넌트가 바뀌었을 뿐이다.

즉, `props`의 변경은
실제 `DOM`의 변경으로 이어질 수는 있지만
함수에서, 재평가를 할 때는
부모 컴포넌트가 재평가되는 것으로 충분하다.
물론 `DemoOutput`이 재실행된다고 해서
이 것이 실제 `DOM`이 변경되는 것은 아니다.

버튼을 아무리 눌러도 바뀌는 것은 없다.

리액트가 컴포넌트들을 재평가했기 때문이다.
그리고 우리가 `Virtual DOM`에서 배운 대로, 컴포넌트 재평가와 컴포넌트 함수의 재실행이 일어나도
실제 `DOM`이 다시 렌더링되거나, 변경되는 것은 아니기 때문이다.

![](https://velog.velcdn.com/images/zenu98/post/a8da0526-704a-4d11-909e-f322ecc0d0e7/image.png)

`Button.js`에 콘솔로그를 추가해 보아도 마찬가지이다.

![](https://velog.velcdn.com/images/zenu98/post/b12abe9a-d2fa-47eb-b8de-787eb0028d7b/image.png)

즉, `Button` 컴포넌트 또한 재평가 된 것이다.





