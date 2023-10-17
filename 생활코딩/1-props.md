 

`prop` 은 컴포넌트에  속성값을 더해준다. `html` 의 `img` 태그에 width 와 height 를 설정해주는 것과 비슷하다고 생각하면 된다.

우선 기본적인 `html` 코드를 세팅하고 필요한 부분을 컴포넌트로 만들겠다. 

아래코드는 create-react-app 웹서버를 동작시켰을 때 입구파일인 src폴더의 index.js 에서 찾을 수 있는 태그 <App /> 부분이 포함하고 있는 App.js 안의 `App 컴포넌트` 코드이다. 

```jsx
function Header(){
  return <h1><a href="/">React</a><h1>
}
function Nav(){
  return <nav>
  <ol>
    <li><a href="/read/1">html</a></li>
    <li><a href="/read/2">css</a></li>
    <li><a href="/read/3">javascript</a></li>
  </ol>
</nav>
}
function Article(){
  return <article>
    <h2>Welcome</h2>
      hello Web!
        </article>
}

function App (){
return <div className="App">
  <Header></Header>
  <Nav></Nav>
  <Article></Article>
</div>
}


```

다음은 위 코드를 prop을 이용해 동적으로 생성하는 코드를 만든다.

변수는 {} 중괄호를 이용해 잡아내고, for loop 을 돌때 각각의 고유 key 값을 추가해줘야 한다는 것 주의

```jsx
function Header(props){
  return <h1><a href="/">{props.title}</a><h1>
}
function Nav(props){
  let lis =[];
  for(let i=0; i < props.topics.length; i++){
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
  return <article>
          <h2>{props.title}</h2>
          {props.body}
          </article>
}

function App (){
  const topics =[
  	{id:1, title:"html", body:"html is..."},
  	{id:2, title:"css", body:"css is..."},
  	{id:3, title:"javascript", body:"javascript is..."}
  ]
  return <div className="App">
          <Header title="React"></Header>
          <Nav topics={topics}></Nav>
          <Article title="Welcome" body="hello Web!"></Article>
         </div>
}
```

`Article` 컴포넌트를 보면 몇번씩 반복해서 작성한 후 그 안에 title 과 body 내용을 바꾸었다. 웹페이지에는 바뀐대로 적용이 된다. 

이런 상황에서 태그에 prop 값을 주고 변경하는 방법이 유용하다는 걸 알 것 같다.

```jsx
function App (){
  const topics =[
  	{id:1, title:"html", body:"html is..."},
  	{id:2, title:"css", body:"css is..."},
  	{id:3, title:"javascript", body:"javascript is..."}
  ]
  return <div className="App">
          <Header title="React"></Header>
          <Nav topics={topics}></Nav>
          <Article title="Welcome" body="hello Web!"></Article>
          <Article title="chapter 1" body="html"></Article>
          <Article title="chapter 2" body="css"></Article>
          <Article title="chapter 3" body="javascript"></Article>
         </div>
}
```
