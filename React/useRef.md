리스트에 저장된 값을 검색 창에 입력된 값에 따라 찾는 컴포넌트를 구현하였다. 
이 과정에서 `useCallback()`과 `useEffect()`를 사용하여 구현하였는데 이에 대해서 따로 다루지 않는 이유는 전에 게시한 포스트에서 이미 자세히 다뤄보았기 때문이다.
```jsx
// components/Ingredients/Ingredients.js

import React, { useState, useCallback } from "react";
import IngredientForm from "./IngredientForm";
import IngredientList from "./IngredientList";
import Search from "./Search";

const Ingredients = () => {
  const [userIngredient, setUserIngredient] = useState([]);

  const addIngredient = async (ingredient) => {
    const response = await fetch(
      "https://react-hook-3f4c6-default-rtdb.firebaseio.com/ingredients.json",
      {
        method: "POST",
        body: JSON.stringify(ingredient),
        headers: { "Content-Type": "application/json" },
      }
    );
    const responseData = await response.json();
    setUserIngredient((prev) => [
      ...prev,
      { id: responseData.name, ...ingredient },
    ]);
  };
  const removeIngredient = (id) => {
    setUserIngredient((prev) => prev.filter((a) => a.id !== id));
  };

  const filterHandler = useCallback((filter) => {
    setUserIngredient(filter);
  }, []);

  return (
    <div className="App">
      <IngredientForm onAdd={addIngredient} />

      <section>
        <Search onLoad={filterHandler} />
        <IngredientList
          onRemoveItem={removeIngredient}
          ingredients={userIngredient}
        />
      </section>
    </div>
  );
};

export default Ingredients;

```

```jsx
// components/Ingredients/Search.js

import React, { useState, useEffect } from "react";
import Card from "../UI/Card";
import "./Search.css";

const Search = React.memo((props) => {
  const { onLoad } = props;
  const [enteredFilter, setEnteredFilter] = useState("");

  useEffect(() => {
    console.log("rendering");
    const query =
      enteredFilter.length === 0
        ? ""
        : `?orderBy="title"&equalTo="${enteredFilter}"`;
    fetch(
      "https://react-hook-3f4c6-default-rtdb.firebaseio.com/ingredients.json" +
        query
    )
      .then((response) => response.json())
      .then((responseData) => {
        const loadedIngredients = [];
        for (const key in responseData) {
          loadedIngredients.push({
            id: key,
            title: responseData[key].title,
            amount: responseData[key].amount,
          });
        }
        onLoad(loadedIngredients);
      });
  }, [enteredFilter, onLoad]);

  return (
    <section className="search">
      <Card>
        <div className="search-input">
          <label>Filter by Title</label>
          <input
            type="text"
            value={enteredFilter}
            onChange={(e) => setEnteredFilter(e.target.value)}
          />
        </div>
      </Card>
    </section>
  );
});

export default Search;

```

![](https://velog.velcdn.com/images/zenu98/post/76bf64de-9562-4fc1-b0c7-e1f8d513684d/image.png)

검색은 잘 되지만 이 과정에서 한가지 문제가 있는데 키가 입력될 때마다 요청을 보내는 것을 콘솔창을 확인하면 알 수 있다.
![](https://velog.velcdn.com/images/zenu98/post/e132a46a-fd68-42b8-9f4d-2aee57d88a3d/image.png)

이를 해결하기 위해 `setTimeout` 타이머를 통해 해결하면 된다.

```jsx
// components/Ingredients/Search.js

//  (...)

 useEffect(() => {
    setTimeout(() => {
      const query =
        enteredFilter.length === 0
          ? ""
          : `?orderBy="title"&equalTo="${enteredFilter}"`;
      fetch(
        "https://react-hook-3f4c6-default-rtdb.firebaseio.com/ingredients.json" +
          query
      )
        .then((response) => response.json())
        .then((responseData) => {
          const loadedIngredients = [];
          for (const key in responseData) {
            loadedIngredients.push({
              id: key,
              title: responseData[key].title,
              amount: responseData[key].amount,
            });
          }
          onLoad(loadedIngredients);
        });
    }, 500);
  }, [enteredFilter, onLoad]);

// (...)
```
해당 동작은 500ms 뒤에 실행된다. 추가로 500ms가 지난 뒤에 확인하는 코드는 추가해야 하는데, `setTimeout()`에 넘겨준 함수 밖에서 `enteredFilter`의 값이 500ms 전에 입력된 값과 동일한지 확인을 해야 한다.

먼저, 자바스크립트에서 클로저(Closures)의 동작 방식에 따라 `enteredFilter`는 타이머가 설정된 시점에 값이 고정된다. 그래서 타이머가 만료된 뒤에는 사용자가 입력한 값과 달라질 것이다.

목적은, 현재 입력된 값과 500ms 전에 입력된 값, 즉 `useEffect()`가 실행된 시전에서의 `enteredFilter`값과 현재 입력값을 비교하여 두 값이 같다면 그 값을 사용자가 검색하고자 하는 값으로 보고 이때 HTTP 요청을 보내는 것이다.

현재 입력값을 갖기 위해 필요한 것이 바로 `useRef()`훅이다.

```jsx
// components/Ingredients/Search.js

import React, { useState, useEffect, useRef } from "react";
import Card from "../UI/Card";
import "./Search.css";

const Search = React.memo((props) => {
  const { onLoad } = props;
  const [enteredFilter, setEnteredFilter] = useState("");
  const inputRef = useRef();  // useRef() 레퍼런스 생성
  useEffect(() => {
    setTimeout(() => {
      if (enteredFilter === inputRef.current.value){ 
        const query =
          enteredFilter.length === 0
            ? ""
            : `?orderBy="title"&equalTo="${enteredFilter}"`;
        fetch(
          "https://react-hook-3f4c6-default-rtdb.firebaseio.com/ingredients.json" +
            query
        )
          .then((response) => response.json())
          .then((responseData) => {
            const loadedIngredients = [];
            for (const key in responseData) {
              loadedIngredients.push({
                id: key,
                title: responseData[key].title,
                amount: responseData[key].amount,
              });
            }
            onLoad(loadedIngredients);
          });
      }
    }, 500);
  }, [enteredFilter, onLoad, inputRef]); // 의존성 추가

  return (
    <section className="search">
      <Card>
        <div className="search-input">
          <label>Filter by Title</label>
          <input
            ref={inputRef}  // 레퍼런스 상수 할당
            type="text"
            value={enteredFilter}
            onChange={(e) => setEnteredFilter(e.target.value)}
          />
        </div>
      </Card>
    </section>
  );
});

export default Search;

```

`inputRef.current` 요소에서 `current`는 `ref` 객체가 가진 프로퍼티로, 지금은 `input` 요소를 가리킨다, `inputRef`가 클로저 바깥에 정의되었기 때문에 어떤 값에 고정되거나 하지 않고, 현재 값을 나타낸다.
그리고 `inputRef`도 이펙트 안에서 사용되고, `inputRef`에 저장된 값이 변경되면 이펙트를 다시 실행해야 하니까 마찬가지로 의존성 값이다.

아직 문제점이 남아있는데 현재 `useEffect()`는 입력값이 변경될 때 마다 계속 실행되므로 수많은 `settimeout` 타이머를 설정하고 있다.

이를 해결하기 위해 `useEffect()`에 return 값을 설정해 줄 수 있다. 이 반환값은 항상 함수여야 하고 여기에서는 cleanup 함수를 넣어주면 된다.

```jsx
// components/Ingredients/Search.js

// (...)

useEffect(() => {
    const timer = setTimeout(() => {
      if (enteredFilter === inputRef.current.value) {
        // (...)
      }
    }, 500);
    return () => {
      clearTimeout(timer);   
    };
  }, [enteredFilter, onLoad, inputRef]);

// (...)

```

이 반환된 함수는 동일한 `useEffect()`함수가 다시 실행되기 직전에 실행된다. 즉, 이펙트가 실행된 다음이 아니라, 이펙드가 다시 실행되기 전이다. 따라서 첫 렌더링 때 이펙트는 실행되지만 cleanup 함수는 실행하지 않고 첫 키 입력이 시작될 때 이전 이펙트를 지우고 새로 시작하게 된다.

이렇게 함으로써 불필요한 타이머를 생성해서 메모리를 낭비하는 일을 줄일 수 있게 된다.


