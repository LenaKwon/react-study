# 이벤트를 가진 컴포넌트 말들기
자바스크립트의 onclick 기능같은 이벤트 기능 만들기.
```javascirpt
<input type="button" onclick="alert(hi)">
```

```jsx
function Header (props){
  return <h1><a href="/">{props.title}</a></h1>
}
function Nav (props){
  let lis =[];
  for(let i=0; i<props.topics.length; i++){
    const t = props.topics[i];
    lis.push(<li key={t.id}><a href={/read/+t.id}>{t.title}</a></li>)
  }
  return <nav>
           <ol>
            {lis}
          </ol>
        </nav> 
}
function Article(props){
  return <article><h2>{props.title}</h2>
  {props.body}</article>
 
}
function App() {
  const topics = [
    {id:1, title:"html", body:"html is..."},
    {id:2, title:"css", body:"css is..."},
    {id:3, title:"javascript", body:"javascript is..."}
  ]
  return (
    <div className="App">
      <Header title="React"></Header>
      <Nav topics ={topics}></Nav>
      <Article title="welcome" body="hello Web"></Article>
      <Article title="Hi" body="this is Lena"></Article>
      <Article title="the Third" body="let's start"></Article>
    </div>
  );
}

export default App;
```
- 첫번째 이벤트로 Header 클릭시 "Header"란 경고창을 뜨게 할 것임
- Header 컴포넌트에 이벤트를 넣고 싶을 때 `onChangeMode` 라고 하는 `prop`의 값으로 `함수`를 전달한다.
- Header 안에서 `링크`를 클릭하면 `컴포넌트`가 `onChangeMode` 함수를 `호출`해서 헤더를 클릭했을 때 해야되는 작업들을 시행되게 할 것이다.
- 예를 들어 'Header' 라는 경고창이 뜨게 한다던지 하는.
- Header 태그는 어떻게 바꾸어 줘야 할까
- Header `a` 태그 안에 `onClick={}` 이란 이벤트를 걸어준다. 이것은 html 코드가 아닌 유사코드이며 react 안에서 실행시켜준다.(주의! onClick의 C 대문자임)
- onClick 했을때 함수호출함 `onClick ={function(){}}`
- 먼저 click 했을 때 페이지가 reload 되지 않도록 만든다. onClick 콜백함수 첫번째 파라미터로 event 객체를 받는다. event 객체는 event 상황을 제어할 수 있는 여러가지 정보와 기능이 들어가 있는데,
- `event.preventDefault();` 하면 `a` 태그가 동작하는 기본동작을 prevent 해준다. 즉 클릭해도 reload 가 일어나지 않음.
- 그 다음 작업은 Header 의 props로 전달된 `onChangeMode()` 함수를 호출하는 것이다. `props.onChangeMode();`

---------
- 두번째 이벤트는 nav 의 리스트를 클릭할때마다 경고창에 해당 id가 뜨게 할 것이다.
- Nav 도 이전 이벤트와 똑같이 onChangeMode 란 함수를 갖고, 첫번째 파라미터로 id를 받아 alert에 띄운다. onChangeMode={(id)=>{alert(id}}
- 이젠 nav 태그안의 a 태그안에 onClick 이벤트를 넣는다. 첫번째 이벤트와 비슷 `onClick={(event)=>{event.preventDefault; props.onChangeMode(); }}`
- 여기서는 onChangeMode 값으로 id 값이 필요하기 때문에 함수호출시 id값을 주입해야하는데 가장 쉬운 방법은 a 태그 안에 id={t.id} 를 넣어주는 것.
- 이벤트 함수내에서 a 태그의 id를 가져오는 방법은? 그때도 event 객체를 사용한다. `event.target` 에서 타겟은 이 이벤트를 유발시킨 타켓을 말하므로 a 태그이고 그 id 값을 가지고 오고 싶다면 `event.target.id`


```jsx
function Header (props){
  return <h1><a href="/" onClick={(event)=>{
event.preventDefault();
props.onChangeMode();
}}>{props.title}</a></h1>
}
function Nav (props){
  let lis =[];
  for(let i=0; i < props.topics.length; i++){
    const t = props.topics[i];
    lis.push(<li key={t.id}><a id={t.id} href={/read/+t.id} onClick={(event)=>{
event.preventDefault();
props.onChangeMode(event.target.id);
}}>{t.title}</a></li>)
  }
  return <nav>
           <ol>
            {lis}
          </ol>
        </nav> 
}
function Article(props){
  return <article><h2>{props.title}</h2>
  {props.body}</article>
 
}
function App() {
  const topics = [
    {id:1, title:"html", body:"html is..."},
    {id:2, title:"css", body:"css is..."},
    {id:3, title:"javascript", body:"javascript is..."}
  ]
  return (
    <div className="App">
      <Header title="React" onChangeMode={()=>{
 alert("Header")}></Header>
      <Nav topics ={topics} onChangeMode={(id)=>{alert(id)}} ></Nav>
      <Article title="welcome" body="hello Web"></Article>
      <Article title="Hi" body="this is Lena"></Article>
      <Article title="the Third" body="let's start"></Article>
    </div>
  );
}

export default App;
```
