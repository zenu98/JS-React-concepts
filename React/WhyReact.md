![](https://velog.velcdn.com/images/zenu98/post/a150fa46-d282-4998-97e8-de2e758290f7/image.png)

최근 몇 년간 전 세계 개발자는 자바스크립트에 뜨겁게 열광하고 있습니다. 한때 자바스크립트는 웹 브라우저에서 간단한 연산을 하거나 시각적인 효과를 주는 단순한 스크립트 언어에 불과했지만, 현재는 웹 애플리케이션에서 가장 핵심적인 역할을 합니다. 더 나아가 영역을 확장하여 서버 사이드는 물론 모바일, 데스크톱 애플리케이션에서도 엄청나게 활약합니다.

이제 자바스크립트만으로도 규모가 큰 애플리케이션을 만들 수 있는 시대가 왔습니다. 이러한 대규모 애플리케이션 구조를 관리하기 위해 수많은 프레임워크가 조금씩 다른 관점에서 이를 해결하려고 노력해 왔습니다.

![](https://velog.velcdn.com/images/zenu98/post/7b8b8554-4ba7-4d58-8811-b6648c257bd3/image.png)

<span style="color:#808080">Angular, Backbone.js, Derby.js, Vue.js, Ember.js, Ext.js, Knockback.js, Sammy.js, PureMVC 등등..</span>

이러한 프레임워크들은 주로 MVC(Model-View-Controller) 아키텍처, MVVM(Model-View-View Model) 아키텍처를 사용합니다. AngularJS의 경우는 MVW(Model-View-Whatever) 아키텍처로 애플리케이션을 구조화합니다.

MVC,MVVM,MVW 등과 같은 여러 구조가 지닌 공통점은 모델(Model)과 뷰(View)가 있다는 것인데요, 모델은 애플리케이션에서 사용하는 데이터를 관리하는 영역이고, 뷰는 사용자에게 보이는 부분입니다. 프로그램이 사용자에게서 어떤 작업(예: 버튼 클릭, 텍스트 입력 등)을 받으면 컨트롤러는 모델 데이터를 조회하거나 수정하고, 변경된 사항을 뷰에 반영합니다.

![](https://velog.velcdn.com/images/zenu98/post/81ef1720-2521-4c44-85dd-553a2bf39806/image.png)

<center><span style="color:#808080">MVC 아키텍쳐</span><center/>
  
  
다음과 같은 JSON 객체 값을 사용하는 뷰가 있습니다.

  

```javascript
{
  "title":"Hello",
  "name":"Seo",
  "age":"50"
}
<div id="post-1">
  <div class="title">Hello</div>
  <div class="name">Seo</div>
  <div class="age">50</div>
</div>
```
  
이때 age 값을 변경하여 업데이트한다면 애플리케이션에서 post-1의 likes 요소를 찾아 내부를 수정해야합니다. 업데이트하는 항복에 따라 어떤 부분을 찾아 변경할지 규칙을 정하는 작업은 간단하지만, 애플리케이션 규모가 크면 상당히 복잡해지고 제대로 관리하지 않으면 성능도 하락할 수 있습니다.
  
이러한 문제를 해결하기 위해 개발된 것이 바로 리액트(React)입니다.

![](https://velog.velcdn.com/images/zenu98/post/c9ee396e-d523-4b62-a5de-4aabd1baf687/image.png)
  
리액트는 자바스크립트 라이브러리로 사용자가 인터페이스를 만드는 데 사용합니다. 구조가 MVC,MVW 등인 프레임워크와 달리, 오직 V(View)만 신경 쓰는 라이브러리입니다.

리액트는 어떤 데이터가 변할 때마다 어떤 변화를 줄지 고민하는 것이 아니라 그냥 기존 뷰를 날려 버리고 처음부터 새로 렌더링한다는 방식의 아이디어로부터 생겨났습니다. 이렇게 하면 애플리케이션 구조가 매우 간단하고, 작성해야 할 코드양이 많이 줄어듭니다. 더 이상 어떻게 변화를 줄지 신경 쓸 필요가 없고, 그저 뷰가 어떻게 생길지 선언하며, 데이터에 변화가 있으면 기존에 있던 것은 버리고 새로 렌더링하면 될 뿐입니다.
  
이러한 것이 가능할 수 있는 이유는 리액트의 주요 특징인 Virtual DOM에 대해 알아야 합니다.
  


#### 참고
- 리액트를 다루는 기술(개정판)김민준


