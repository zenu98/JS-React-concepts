# Critical Render Path란?
Critical Rendering Path(CRP)는 브라우저가 서버로부터 HTML 응답을 받아 화면을 그리기 위해 실행하는 과정입니다.
각 단계는 다음과 같습니다.
![](https://velog.velcdn.com/images/zenu98/post/8cf9f5ce-f775-4638-8cff-2f4a5b5c0919/image.png)

### 1. DOM Tree 생성
렌더 엔진이 문서를 읽어들여서 파싱하고 어떤 내용을 페이지에 렌더링 할 지 결정합니다.

### 2. Render Tree 생성
브라우저가 DOM과 CSSOM을 결합하는 곳이며, 이 프로세서는 화면에 보이는 모든 콘텐츠와 스타일 정보를 모두 포함하는 최종 렌더링 트리를 출력합니다.
즉 화면에 표시되는 모든 노드의 콘텐츠 및 스타일 정보를 포함합니다.

### 3. Layout
브라우저가 페이지에 표시되는 각 요소의 크기와 위치를 계산하는 단계입니다.

### 4. Painting
실제 화면에 그려지는 단계입니다.

# DOM이란?
DOM은 Document Object Model의 약어로, 객체로 문서 구조를 표현하는 방법으로 XML이나 HTML로 작성합니다.

웹 브라우저는 DOM을 활용하여 객체에 자바스크립트와 CSS를 적용합니다.
DOM은 트리 형태로서 특정 노드를 찾거나 수정하거나 제어하거나 원하는 곳에 삽입할 수 있습니다.

이 DOM에는 한가지 치명적인 문제점이 있는데, 바로 동적 UI에 최적화되어 있지 않다는 것입니다. HTML은 자체적으로는 정적이며 JS를 사용하여 이를 동적으로 만들어 나갑니다.

규모가 큰 웹 어플리케이션에서 어떤 인터렉션에 의해 DOM에 변화가 발생하면 그 때 마다 Render Tree가 재생성됩니다. 즉, 모든 요소들의 CSS를 다시 연산하고, 레이아웃을 구성하고, 페이지를 리페인팅 합니다. 이 과정에서 시간이 허비하게 되고 느려진다는 말이 나오게 됩니다.

이렇게 DOM을 조작할 때마다 엔진이 웹페이지를 새로 그리기 때문에 업데이트가 너무 잦으면 성능이 저하될 수 있습니다. 이런 문제를 해결하기 위하여 DOM을 최소한으로 조작하여 작업을 처리하는 방식으로 개선할 수 있습니다.

따라서 리액트는 Virtual DOM 방식을 사용하여 DOM 업데이트를 추상화함으로써 DOM 처리 횟수를 최소화하고 효율적으로 진행합니다.

# Virtual DOM
![](https://velog.velcdn.com/images/zenu98/post/b5ebcc2a-9962-472c-957e-e3edcddcfffe/image.png)

Virtual DOM을 사용하면 실제 DOM에 접근하여 조작하는 대신, 이를 추상화한 자바스크립트 객체를 구성하여 사용합니다. 마치 실제 DOM을 메모리에 복사해 준 것으로 생각하면 됩니다.

리액트에서 데이터가 변하여 웹 브라우저에 실제 DOM을 업데이할 때는 다음 세 가지 절차를 밟습니다.

1. 데이터를 업데이트하면 전체 UI를 Virtual DOM에 리렌더링합니다.
2. 이전 Virtual DOM에 있던 내용과 현재 내용을 비교합니다.
3. 바뀐 부분만 실제 DOM에 적용합니다.

![](https://velog.velcdn.com/images/zenu98/post/1a0780eb-5449-4e12-8b07-75cf5ea77d25/image.png)
<center><small>출처:https://javascript.plainenglish.io/react-the-virtual-dom-comprehensive-guide-acd19c5e327a</small></center>  





데이터가 바뀌면 Virtual DOM에 렌더링되고, 이전에 생긴 Virtual DOM과 비교하여 바뀐 부분을 실제 DOM에 적용시켜 줍니다. 변화된 부분을 찾는 과정을 Diffing이라고 부르며, 그것을 적용시켜주는 것은 재조정(Reconciliation)이라고 부릅니다.
![](https://velog.velcdn.com/images/zenu98/post/dc0396aa-8c4a-4d2f-b421-c139cca8a7d3/image.png)
<center><small>출처:https://javascript.plainenglish.io/react-the-virtual-dom-comprehensive-guide-acd19c5e327a</small></center>

---

### 오해
Virtual DOM을 사용한다고 해서 사용하지 않을 때와 비교하여 무조건 빠른 것은 아닙니다. 리액트를 사용하지 않아고 최적화된 코드로 DOM 작업이 느려지는 문제를 개선할 수 있고, 또 작업이 매우 간단할 떄는 오히려 리액트를 사용하지 않는 편이 더 나은 성능을 보이기도 합니다.

리액트와 Virtual DOM이 언제나 제공할 수 있는 것은 바로 업데이트 처리 간결성입니다. UI를 업데이트하는 과정에서 생기는 복잡함을 모두 해소하고, 더욱 쉽게 업데이트에 접근할 수 있습니다.


---

#### 참고
- 리액트를 다루는 기술(개정판)김민준
