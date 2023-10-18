# state
prop과 함께 component 함수를 다시 실행시켜 새로운 return 값을 만들어주는 역할. 
prop 과 component 모두 값이 변경되면 return 값을 변경시켜서 ui를 변경시키지만 
여기서 다른점은 prop은 컴포넌트를 사용하는 외부자를 위한 데이터이고 state 는 컴포넌트를 만드는 내부자를 위한 데이터이다.

## 목표
각각의 링크를 클릭했을 때 상세보기가 나오도록 하기. 
1. 로고 Welcome 은 인삿말
2. 각각의 목차는 해당 제목과 내용

## 코딩과정
1. 먼저 App() 안에 상황에 따른 mode를 설정한다. 로고 web 을 클릭했을 때는 Welcome 목차리스트를 클릭했을 때는 Read. `const mode ='Welcome';`
2. ```jsx
   let content = null;
   if(mode==='Welcome'){ content=<Article title="Welcome" body="hello Web"></Article> }
   else if (mode==='Read'){content=<Article title="Read" body="Read this article..."></Article>}
   ```   
3. return 값의 `<Article title="Read" body="Read this article..."></Article>` 부분을 `{content}` 로 대체함
---
4. 이제는 설정한 mode를 이벤트가 발생할 때마다 변경되도록 바꿔야 한다.
5. 리턴값 안에 이전 강의에서 링크 클릭시 alert 메세지가 뜨게 했던 Header와 Nav 부분을 이제 `mode = "Welcome";`  `mode = "Read";` 로 각각 바꿔줌
6. 현재 mode 값은 'Welcome' 이고, html,css,js 링크를 누르면 'Read'로 바뀌는데 웹앱은 변경이 되지 않는다.
7. mode 값이 'Read' 로 바뀌어도 App() 함수는 return 값을 반환한 뒤 다시 실행되지 않기 때문에 return 값에는 변화가 없다.
8. 우리가 하고 싶은건 App() 함수가 다시 실행되어 새로운 return 값을 만든 뒤 ui 를 변경시키는 것이다 => 이때 state 가 필요하다.
---
9. state 를 사용하기 위해서는 react 에서 제공하는 `useState` 라는 `훅`을 import 해야한다. `import {useState} from 'react';`
10. 이제 일반적인 지역변수인 mode 의 상태를 useState를 이용해 업그레이드 시킬 것이다. `const mode = usestate('Welcome');`
11. useState의 인자인 'Welcome' 은 초기값이고, useState 를 사용하면 배열을 반환하는데, 0 번째 원소는 상태의 값을 읽을 때 쓰는 데이터, 1번째 원소는 상태를 변경할 때 쓰는 함수이다.
12. 우린 구조화분해를 통해 이 배열의 원소들을 저장하여 사용할 것이다. `const [mode,setMode] = useState('Welcome');`
13. 아직도 클릭했을 때 아무런 변화가 없다. 값을 변경하기 위해 useState의 두번째 인자로 받았던 함수 setMode() 를 쓰면 된다. 
`mode = "Welcome";`  `mode = "Read";`  부분을 `setMode("Welcome");` `setMode("Read");` 로 각각 변경한다.
14. 이제 클릭하면 setMode 로 인해 App() 컴포넌트가 다시 실행되고 useState 가 mode 값을 'Read' 로 세팅해준다.
15. 따라서 mode === 'Read' 인 조건인 content 가 실행되고 이것이 렌더링 되면 ui 가 변경되는 것이다.
---
16. 자 이제 좀 더 구체적으로 mode 가 Read 일 때 각각의 html,css,js 선택했을 때 그에 맞는 title 과 body 값을 갖도록 해보자. 
    현재는 그냥 셋다 동일한 값이 뜸.`(mode==='Read'){content=<Article title="Read" body="Read this article..."></Article>}`
17. 그러기 위해서는 셋 중에 어떤 글을 선택했는지 그것을 또 state 로 만들 필요가 있다.
18. 우선 useState를 이용한 기본세팅 해준다. `const [id,setId] = useState(null);` 아직 선택전이니 초기값은 null 로 설정함.
19. 이 state 는 Nav 를 클릭했을 때 바뀌기 때문에 Nav 안에서 setId 값을 세팅한다.  `setId(id);`의 id 값은 Nav 안의 `onChangeMode` prop이 받는 id 값과 동일.
20. 이제 id 값이 변경되면 App() 컴포넌트가 다시 실행되고, useState 의 id 값이 새로 지정된다.
21. 그럼 이 id 값으로 topics 안의 id 값이 같은 원소를 찾아서 해당하는 title과 body 값으로 세팅해주면 된다.
22. 여기서 반복문을 이용하여 세팅해 주겠다.
```jsx
if(Mode ==='Read'){
  let title, body =null; // 기본 세팅
  for(let i=0; i < topics.length; i++){
    if(topics.id == id){
      title = topics[i].title;
      body = topics[i].body;
    }
  }
  content=<Article title={title} body={body}></Article>
}
```
23. 완성했는데 변경이 되지 않는다. 여기서 한가지 문제점이 있다.
24. 디버깅을 해보면 id 값이 String 이라는 것. for문에 사용된 id 값은 setId 로 부터 오고 setId 는 return 값 <Nav> 안에 있고, 이 안의 id 값은 Nav 컴포넌트 안에 있다.
    `event.target.id` 를 통해 id 값을 알아내는데 그 id 값은 <a> 태그안에 id값에서 가져온다. 처음 입력시엔 숫자였지만, 그것을 태그의 속성으로 넘겨서 문자가 된 것이다.
    따라서 `event.target.id`를 숫자로 컨버팅 해주는 작업이 필요하다. `Number(event.target.id)`

## 완성 코드
```jsx
// import logo from './logo.svg';
import './App.css';
import {useState} from 'react';

function Header (props){

  return <h1><a href="/" onClick={function(event){
    event.preventDefault();
    props.onChangeMode();
  }}>{props.title}</a></h1>
}
function Nav (props){
  let lis =[];
  for(let i=0; i<props.topics.length; i++){
    const t = props.topics[i];
    lis.push(<li key={t.id}><a id={t.id} href={/read/+t.id} onClick={(event)=>{
      event.preventDefault();
      props.onChangeMode(Number(event.target.id));
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
  const [mode,setMode] =useState('Welcome');
  const [id,setId] = useState(null); 
  const topics = [
    {id:1, title:"html", body:"html is..."},
    {id:2, title:"css", body:"css is..."},
    {id:3, title:"javascript", body:"javascript is..."}
  ]
  let content = null;
  if(mode==='Welcome'){
    content = <Article title="welcome" body="hello Web"></Article>;
  }else if(mode==='Read'){
    let title, body = null;
    for(let i=0; i<topics.length; i++){
      if(topics[i].id==id){
        title = topics[i].title;
        body = topics[i].body;
      }
    }
    content = <Article title={title} body={body}></Article>;
  }
  return (
    <div className="App">
      <Header title="React" onChangeMode={function(){
        setMode("Welcome");
      }}></Header>
      <Nav topics ={topics} onChangeMode={(_id)=>{ setMode("Read"); setId(_id);}}></Nav>
      {content}
    
    </div>
  );
}

export default App;

```
