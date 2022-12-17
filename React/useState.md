간단한 리액트 컴포넌트를 통해 리액트 훅에 대한 내용을 총 정리하는 시간을 갖도록 해보겠다.



![](https://velog.velcdn.com/images/zenu98/post/9b55a09c-5335-403d-9d67-70ce7c5ee7eb/image.png)


```jsx
// components/Ingredients/IngredientForm.js

import React from "react";

import Card from "../UI/Card";
import "./IngredientForm.css";

const IngredientForm = React.memo((props) => {
  const submitHandler = (event) => {
    event.preventDefault();
    // ...
  };

  return (
    <section className="ingredient-form">
      <Card>
        <form onSubmit={submitHandler}>
          <div className="form-control">
            <label htmlFor="title">이름</label>
            <input type="text" id="title" />
          </div>
          <div className="form-control">
            <label htmlFor="amount">수량</label>
            <input type="number" id="amount" />
          </div>
          <div className="ingredient-form__actions">
            <button type="submit">추가</button>
          </div>
        </form>
      </Card>
    </section>
  );
});

export default IngredientForm;


```

`IngredientForm.js`에는 몇가지 `Input` 입력창이 존재하고 우리가 이 입력창에서 하는 일은 보통 `state`로 사용할 프로퍼티와 바인딩하고, 키 입력이 들어올 때마다 `state`를 업데이트해 다시 입력창에 반영하는 일이다.
 현재 작동하지 않는 ‘Add ingredient’ 버튼을 활성화시키기 위해, 여기에도 동일한 작업을 해줘야 한다. 이 앱에서는 어떤 동작을 하려면 먼저 사용자로부터 입력을 받아야 한다.
 
 그러기 위해서 우리는 첫번째 알아볼 리액트 훅, `useState`를 사용해보자
 
![](https://velog.velcdn.com/images/zenu98/post/d61f6db7-95e5-4260-983a-1cb881feb23a/image.png)
 
```jsx
(alias) useState<{
    title: string;
    amount: string;
}>(initialState: {
    title: string;
    amount: string;
} | (() => {
    title: string;
    amount: string;
})): [{
    title: string;
    amount: string;
}, React.Dispatch<React.SetStateAction<{
    title: string;
    amount: string;
}>>] (+1 overload)
import useState
```
위에 보이는 것은 `useState()`함수의 정의와 사용법이다. 두개의 요소가 있는 배열을 반환하는 것을 볼 수 있는데, 배열의 첫번째 요소는 현재 `state`의 상태이다. 
그리고 언제든 이 상태가 업데이트 되면 컴포넌트를 재구성하고 컴포넌트 함수는 다시 실행된다. 그러면 `useState()`함수도 전부 다시 호출될 것이다. 하지만 리액트는 우리가 설정한 `state`를 내부에 저장하여 컴포넌트가 다시 실행되어도 `state`가 남아있을 수 있도록 해준다.
따라서 `useState()`가 반환하는 첫번째 값은 현재 `state`에 대한 상태이고 이것으로 컴포넌트가 다시 렌더링 될 때 사용된다.

이제 이 `state`를 업데이트 하게 하는 것이 두번째 요소이다.
두 번째 요소는 현재 상태를 업데이트할 수 있는 함수인데, `Dispatch`가 사용된 함수로, 이 함수를 호출할 때 상태를 업데이트할 새로운 데이터를 넣어 호출할 수 있다.

위의 원리에 따라서 `useState`를 사용해보면 다음과 같다.
 
```jsx
import React, { useState } from "react";

import Card from "../UI/Card";
import "./IngredientForm.css";

const IngredientForm = React.memo((props) => {
  const inputState = useState({ title: "", amount: "" });
  const submitHandler = (event) => {
    event.preventDefault();
    // ...
  };

  return (
    <section className="ingredient-form">
      <Card>
        <form onSubmit={submitHandler}>
          <div className="form-control">
            <label htmlFor="title">이름</label>
            <input
              type="text"
              id="title"
              value={inputState[0].title}
              onChange={(event) => {
                const newTitle = event.target.value;
                inputState[1]((prev) => ({
                  title: newTitle,
                  amount: prev.amount,
                }));
              }}
            />
          </div>
          <div className="form-control">
            <label htmlFor="amount">수량</label>
            <input
              type="number"
              id="amount"
              value={inputState[0].amount}
              onChange={(event) => {
                const newAmount = event.target.value;

                inputState[1]((prev) => ({
                  amount: newAmount,
                  title: prev.title,
                }));
              }}
            />
          </div>
          <div className="ingredient-form__actions">
            <button type="submit">추가</button>
          </div>
        </form>
      </Card>
    </section>
  );
});

export default IngredientForm;

```

이러한 방식은 작동에 문제는 없지만 코드에 어떤 함수가 있는지 한눈에 파악하기 힘들다. 따라서 우리는 모던 자바스크립트 문법인 `배열 구조 분해 할당(Array Destructuring)`을 사용해서 함수를 파악하기 쉽게 만들 수 있다.

![](https://velog.velcdn.com/images/zenu98/post/0641734f-47d1-484f-814a-5e3fca91150c/image.png)

이제 우리에게 익숙한 모습이 되었다.

첫 번째 요소에는 데이터(현재 `state`)를 저장하고
두 번째 요소는 해당 데이터를 업데이트하는 함수이다.
보통 함수 이름은 `setInputState`처럼 set 뒤에 데이터의 이름을 붙여서 만든다.

배열 구조 분해 할당을 통해 `inputState`에는 데이터가 저장될 거고, `setInputState`에는 데이터를 업데이트하는 함수가 저장될 것이다.

```jsx
import React, { useState } from "react";

import Card from "../UI/Card";
import "./IngredientForm.css";

const IngredientForm = React.memo((props) => {
  const [inputState, setInputState] = useState({ title: "", amount: "" });
 // const inputState = useState({ title: "", amount: "" });
  const submitHandler = (event) => {
    event.preventDefault();
    // ...
  };

  return (
    <section className="ingredient-form">
      <Card>
        <form onSubmit={submitHandler}>
          <div className="form-control">
            <label htmlFor="title">이름</label>
            <input
              type="text"
              id="title"
              value={inputState.title}
              onChange={(event) => {
                const newTitle = event.target.value;
                setInputState((prev) => ({
                  title: newTitle,
                  amount: prev.amount,
                }));
              }}
            />
          </div>
          <div className="form-control">
            <label htmlFor="amount">수량</label>
            <input
              type="number"
              id="amount"
              value={inputState.amount}
              onChange={(event) => {
                const newAmount = event.target.value;
                setInputState((prev) => ({
                  amount: newAmount,
                  title: prev.title,
                }));
              }}
            />
          </div>
          <div className="ingredient-form__actions">
            <button type="submit">추가</button>
          </div>
        </form>
      </Card>
    </section>
  );
});

export default IngredientForm;
```
`useState()`훅의 전반적인 동작 원리를 이해했고
이제 코드를 조금 더 알아보기 쉽게 바뀌었지만 아직 거슬리는 부분이 한가지 남아있다. 상태 업데이트 방식이 복잡하다는 것이다.

클래스형 컴포넌트에서 사용하는 `state`와 달리, `useState()`를 사용하는 함수형 컴포넌트의 `state`는 객체일 필요가 없으며 리액트가 합쳐주지도(merge) 않습니다, 대신 `state`를 여러 개 등록할 수 있다.

```jsx
import React, { useState } from "react";

import Card from "../UI/Card";
import "./IngredientForm.css";

const IngredientForm = React.memo((props) => {
  const [enteredTitle, setEnteredTitle] = useState("");
  const [enteredAmount, setEnteredAmount] = useState("");
  const submitHandler = (event) => {
    event.preventDefault();
    // ...
  };

  return (
    <section className="ingredient-form">
      <Card>
        <form onSubmit={submitHandler}>
          <div className="form-control">
            <label htmlFor="title">이름</label>
            <input
              type="text"
              id="title"
              value={enteredTitle}
              onChange={(event) => {
                setEnteredTitle(event.target.value);
              }}
            />
          </div>
          <div className="form-control">
            <label htmlFor="amount">수량</label>
            <input
              type="number"
              id="amount"
              value={enteredAmount}
              onChange={(event) => {
                setEnteredAmount(event.target.value);
              }}
            />
          </div>
          <div className="ingredient-form__actions">
            <button type="submit">추가</button>
          </div>
        </form>
      </Card>
    </section>
  );
});

export default IngredientForm;
```
우리가 항상 쓰던 방식이 이제 완성됐다.

이제 이 컴포넌트에는 리액트에 의해 개별적으로 관리되는 `state`가 두 개 있다, 이 둘은 서로 다른 변수에 저장돼 있고, 서로 다른 함수를 통해 값을 설정한다.

이 둘은 독립적으로 관리되기 때문에 우리가 뭔가를 합쳐줄 필요도 없다, 하나가 업데이트되어도 다른 하나는 그대로 유지되고, 반대도 마찬가지이다.