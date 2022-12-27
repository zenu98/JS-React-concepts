이번에는 `useContext()` 작업을 하기 위해 로그인 컴포넌트를 사용해 보도록 하겠다. 실제 로그인하는 로직이 아니라 버튼을 통해 간단한 확인 절차를 구현한 컴포넌트이며 만약 로그인이 되지 않았다면 다음과 같은 화면이 뜨게 된다.

![](https://velog.velcdn.com/images/zenu98/post/67f3a973-c12a-4866-a7e2-46bf799b3603/image.png)
```jsx
// components/UI/Auth.js

import React from "react";

import Card from "./UI/Card";
import "./Auth.css";

const Auth = (props) => {
  const loginHandler = () => {};

  return (
    <div className="auth">
      <Card>
        <h2>로그인 해주세요!</h2>
        <p>로그인을 하면 내용이 보입니다.</p>
        <button onClick={loginHandler}>로그인</button>
      </Card>
    </div>
  );
};

export default Auth;

```

`useContext()` 를 위한 js파일을 다음과 같은 경로에 작성했다.

```jsx
// components/context/auth-context.js

import React, {useState} from "react";

export const AuthContext = React.createContext({
    isAuth:false,
    login:()=>{}
});

const AuthContextProvier = props => {
    const [isLogin, setIsLogin] = useState(false);

    const loginHandler = () => {
        setIsLogin(true);
    }

    return (
        <AuthContext.Provider value={{login: loginHandler, isAuth:isLogin}}>
            {props.children}
        </AuthContext.Provider>
    )
}
export default AuthContextProvier;

```
새 Context를 만들 때는 `createContext` 함수를 사용하며 파라미터에는 해당 Context의 기본 상태를 지정한다. 그리고 새로운 AuthContextProvider라는 새로운 컴포넌트를 만들어 주었다. 
반환값의 <AuthContext.Provider>는 value로 값을 받는데, 이 값이 변경되면 Provder을 받는 모든 컴포넌트들에게 변경 사항을 알리게 된다.
나는 App.js에서 컨텍스트를 제어하고 싶기 때문에 루트 index.js 파일을 다음과 같이 설정해 주었다.

```jsx
// src/index.js

import React from "react";
import ReactDOM from "react-dom";

import "./index.css";
import App from "./App";
import AuthContextProvier from "./components/context/auth-context";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(
  <AuthContextProvier>
    <App />
  </AuthContextProvier>
);

```

```jsx
// src/App.js

import React, {useContext} from "react";
import Auth from "./components/Auth";
import Ingredients from "./components/Ingredients/Ingredients";
import { AuthContext } from "./components/context/auth-context";
const App = (props) => {
  const authContext = useContext(AuthContext);
  let content = <Auth/>
  if(authContext.isAuth){
    content = <Ingredients/>
  }
  return content;
};

export default App;


```
여기서 `useContext()`훅을 통해 auth-context에 있는 AuthContext 컴포넌트와 연결할 수 있다. 이제 `<Auth>` 컴포넌트도 똑같이 연결해주면 된다.

```jsx
// components/UI/Auth.js

import React, {useContext} from "react";
import { AuthContext } from "./context/auth-context";
import Card from "./UI/Card";
import "./Auth.css";

const Auth = (props) => {
  const authContext = useContext(AuthContext);
  const loginHandler = () => {
    authContext.login();
  };

  return (
    <div className="auth">
      <Card>
        <h2>로그인 해주세요!</h2>
        <p>로그인을 하면 내용이 보입니다.</p>
        <button onClick={loginHandler}>로그인</button>
      </Card>
    </div>
  );
};

export default Auth;

```

이제 우리는 기다란 props 체인을 생성하지 않고도 데이터를 전달할 수 있게 되었다.

![](https://velog.velcdn.com/images/zenu98/post/5c2d6348-bb15-4a4e-8eee-b4f23ac5544b/image.gif)
