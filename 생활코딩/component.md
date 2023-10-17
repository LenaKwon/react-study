react는 `사용자 정의 태그 (컴포넌트)` 를 만드는 기술이다.

예를 들어 index.html 파일에 아래 코드가 수억개가 반복된다고 하면 코드 파악하기가 쉽지 않을 것.

```html
<header>
	<h1><a href =”/”> WEB<a/><h1>
<header/>
```

따라서 반복되는 것을 사용자정의태그 (컴포넌트)로 만든다. 

컴포넌트는 이름이 꼭 `대문자`로 시작하고 `html 태그`를 `return 값`으로 가진다.

```jsx
function Header () {
	return <header>
                  <h1><a href =”/”> WEB<a/><h1>
                <header/>
}
```

이렇게 컴포넌트 만들면  간편하게 반복해서 쓸 수 있음. 컴포넌트는 컴포넌트 안에도 쓸 수 있음

```html
function App(){
return (
<div>
  <Header><Header/>
  <Header><Header/>
  <Header><Header/>
  <Header><Header/>
<div/>
);
}
```

Header 컴포넌트 안의 WEB 이란 글자를 바꾸면  Header 컴포넌트가 적용된 모든 부분에 동시에 바뀜

이러한 react 의 특징을 통해 우리는 `태그`를 `독립된 부품`으로 만들 수 있게 됨. 

따라서 더 `적은 복잡도`를 갖을 수 있고, `컴포넌트`를 다른사람과 `공유`할 수 있게 함으로써 `생산성`을 획기적으로 끌어올림
