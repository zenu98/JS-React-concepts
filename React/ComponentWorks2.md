이러한 간단한 컴포넌트 로직에서는 성능에서의 문제가 될 것이 없지만,
만약 더 큰 어플리케이션 환경이라면 최적화가 필요하다.

```javascript
//in App.js

(...)
 
  return (
    <div className="app">
      <h1>Hi there!</h1>
    
      <DemoOutput show={false} />   // <--

      <Button onClick={toggle}>Toggle</Button>
    </div>
  );
}

export default App;


```

따라서, 개발자인 우리는 특정한 상황일 경우에만 이런 `DemoOutput`을 재실행하도록 리액트에 지시해야 한다.
이 특정 상황은 컴포넌트가 받은 `props`가 변경되었다던가 하는 경우이다.
`DemoOutput`의 `show={false}` 부분을 다시 불러오면 
리액트는 `show`의 값이 바뀔 때에만 DemoOutput을 재실행하도록 하는 것이 우리가 원하는 방식이다.

이 작동 방식을 구현하기 위해서는 일단, `props`가 바뀌었는지 확인할 컴포넌트를 지정한 뒤에 이를 간단히 감싸주기만 하면 된다.

```javascript
// in DemoOutput.js

import React from "react";
import MyParagraph from "./MyParagraph";

const DemoOutput = (props) => {
  console.log("DemoOutput RUNNING!");
  return <MyParagraph>{props.show ? "This is new!" : ""}</MyParagraph>;
};
export default React.memo(DemoOutput);  // <--
```

이는 함수형 컴포넌트에서만 작동한다.

`React.memo`는 인자로 들어간 이 컴포넌트에
어떤 `props`가 입력되는지 확인하고 입력되는 모든 `props`의 신규 값을 확인한 뒤 이를 기존의 `props`의 값과 비교하도록 리액트에게 전달한다.
그리고 `props`의 값이 바뀐 경우에만 컴포넌트를 재실행 및 재평가하게 된다.

그리고 부모 컴포넌트가 변경되었지만
그 컴포넌트의 `props` 값이 바뀌지 않았다면
컴포넌트 실행은 건너뛰게 된다.

자, 이제 이를 `React.memo`로 감싸준 후 저장하면
이 결과를 다시 볼 수 있다.

![](https://velog.velcdn.com/images/zenu98/post/e122b3f6-549f-40ea-8d3d-b332db1956e7/image.png)


최초에는, `App`이 처음으로 렌더링되기 때문에 `DemoOutput`은 바로 실행된다. 하지만, 콘솔을 초기화한 뒤 버튼을 누르면
APP RUNNING과 Button RUNNING만 확인되고
DemoOutput RUNNING은 출력되지 않는 것을 볼 수 있다.

`MyParagraph`는 `DemoOutput`의 자식 관계이므로
따라서 MyParagraph RUNNING 역시 출력되지 않는다.
그리고 `DemoOutput`이 재실행되지 않았다면
당연히 그 자식 역시 재실행되지 않는다.

이렇게, 불필요한 재렌더링을 피하기 위한 최적화가 이루어지고 있다.


그렇다면 이 간단한 최적화 방법을 모든 컴포넌트에 적용하면 안되는 걸까?
그 이유에는 최적화에는 비용이 따르기 때문이다.
이 `memo` 메소드는 `App`에 변경이 발생할 때마다
이 컴포넌트로 이동하여 기존 `props` 값과 새로운 값을 비교하게 한다.

그러면 리액트가 두 가지 작업을 할 수 있어야 하는데, 먼저 기존의 props 값을 저장할 공간이 필요하고 비교하는 작업도 필요하다.

이 각각의 작업은 개별적인 성능 비용을 필요로 하기 떄문에 이 성능 효율은 어떤 컴포넌트를 최적화하느냐에 따라 달라지게 된다.


컴포넌트 트리가 매우 크다면
이 `React.memo`는 매우 유용하게 쓰일 수 있다.
그리고 컴포넌트 트리의 상위에 위치해있다면
전체 컴포넌트 트리에 대한 쓸데없는 재렌더링을 막을 수 있다.

이제 `Button` 에도 이를 적용해 보자.
```javascript
// in Button.js

import React from "react";
import classes from "./Button.module.css";

const Button = (props) => {
  console.log("Button RUNNING !");
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

export default React.memo(Button);  // <--
```

버튼에 대해 매번 재평가 할 필요는 없다.
버튼은 고정된 디폴트 값으로 같은 텍스트, 같은 함수를 사용하기 때문에 이를 래핑(wrapping)하는 것은 꽤 합리적으로 보인다.

그런데 꽤 이상한 일이 발생한다.

![](https://velog.velcdn.com/images/zenu98/post/a2eff0ed-2bf6-4d38-9f29-ccca5c9878af/image.png)

저장을 하고, `App`을 새로고침하면 Button RUNNING이 표시되고,
여기에서 버튼을 클릭하면 다시 Button RUNNING이 표시된다.

왜 이런 일이 발생하는 걸까?
Button RUNNING이 계속 표시된다는 것은
`props`의 값이 계속 바뀐다는 뜻이다.

버튼을 확인해보면 안에는 `onClick`과 자식까지 총 2개의 `props`밖에 없다. 이 둘 모두 값은 불변으로 텍스트도 같고 실행되는 함수도 항상 같다.

이건 리액트에서 흔하게 발생하는 오류 중 하나이다.

```javascript
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


이 `App` 컴포넌트는 어쨌건 함수이기 때문에
마치 일반적인 자바스크립트 함수처럼 재실행된다.

이 말은 모든 코드가 다시 실행된다는 의미이다.

이 코드들은 `App` 함수의 모든 렌더링, 또는 모든 실행 사이클에서 재사용되지 않는 완전히 새로운 함수이다, 이것은 우리가 매번 다시 만드는
상수이다.

여기의 있는모든 코드가 다시 실행되므로
당연히, 새로운 함수가 만들어지고 이 함수는 이전과 같은 함수가 아니고
같은 기능을 하는 **새로운** 함수이다.

자바스크립트에서는, `App` 함수가 실행될 때마다 만들어지는 함수는
모두 새로운 함수이다.

```javascript
<DemoOutput show={false} />
```

이는 `DemoOutput`에 `false` 정보가 갔을 때도 해당된다.
앞서 이 것들은 불변한다고 말했지만 사실 기술적으로는 틀린 말이다.

이 `App` 함수는 재실행된다고 했으니 매번 이 `false` 값이 새로 만들어지는 것이다.

마지막 렌더링 사이클에서 `false`가 발생했더라도 재실행하면 새로운 `false`가 생성되는 것이다.

그렇다면, 이 경우에서 `React.memo`가 `DemoOutput`에서는 작동하는데
왜 `Button`에서는 작동하지 않는 걸까?

![](https://velog.velcdn.com/images/zenu98/post/bf008ecb-dc3a-4745-88bc-bd12368d2995/image.png)


이 `false`는 `boolean` 값이고 문자열, 또는 숫자와 같은
이런 `boolean` 값은 자바스크립트의 원시값이다.

이 `React.memo`가 최종적으로 하는 일은
`props`의 값을 확인하고 `props.show`를 직전의 값인 `props.previous.show`와 비교한다고 생각하면 된다.

`props.show`의 이전 값을 확인하고 현재의 값과 비교하는데
이 작업은 일반 비교 연산자를 통해 실행된다.
값이 원시값이라면 이게 가능하다.
두 개의 `boolean`을 비교해서 `true`가 나왔다면 이는 같은 값이고,
두 개의 문자열을 비교해서 `true`가 나왔다면 이는 같은 문자열이다.
물론 기술적으로는, 이 두 `boolean`은 서로 다른 `boolean`이고
이 문자열도 마찬가지로 서로 다른 문자열이지만 원시값이라면 이런 비교가 가능하게 된다.

하지만 

배열이나 객체, 함수를 비교한다면 말이 달라진다.
위 두 배열을 비교한다고 하면 사람의 눈에는 같아보이지만
자바스크립트에서는 이 둘은 같지 않다.

이 것은 자바스크립트의 핵심적인 개념으로 자바스크립트에서
함수는 하나의 객체에 불과하다.
이 `App` 함수가 실행될 때마다
새로운 함수 객체가 생성이 되고
이 함수 객체가 `onClick props`에 전달이 된다.
이렇게 되면, 버튼은 `props.onClick`과
`props.previous.onClick`을 비교하는 셈이 되는데, 이 두 함수 객체는
같은 내용을 갖고 있다 해도 자바스크립트에서 이 두가지를 비교하면 결코 동일하지 않다.

따라서, `React.memo`는 자바스크립트의 이러한 작동방식 때문에
값이 변경되었다고 인식하게 되는 것이다.