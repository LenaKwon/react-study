# update

## 목표
이전에 생성한 글의 제목과 본문을 수정하여 update 한다. 
create 과 read 를 합쳐서 만들 수 있음.

## 코딩과정
1. 가장 먼저 update 할 수 있는 링크를 만든다. create 시작링크와 동일 `<a href='/update'>Update</a>`
2. create 링크와 update 링크는 똑같이 변화를 주는 속성을 가졌으니 목록으로 묶어주자. `<ul><li></li><li></li></u/>`
3. update 링크는 기존에 등록된 글을 변경하는 작업이기 때문에 welcome page 에서는 보여지지 않고, 글 목록을 클릭했을때만 create 링크 밑에 나타나게 하자.
4. 따라서 mode가 Read 일때의 조건문에 포함시킨다. 우선 `let contextControl=null;` 지역변수를 만들고, 
조건문에 `contextControl =<li><a href='/update'>Update</a></li>` 작성한 후 create 링크 밑에 `{contextControl}` 쓰면 Read 모드일때만 update가 나타난다.
여기서 update할때 주소값에 id 값을 추가해주면 좋음 `contextControl= <li><a href='/update/'+id >Update</a></li>`
5. `update` 를 클릭했을 때 create와 똑같은 절차이다. `contextControl= <li><a href='/update/'+id onClick={(event)=>{event.preventDefault(); setMode('Update')}} >Update</a></li>`
6. setMode('Update') 했으니 이 조건일 때 코드 작성 `else if(mode==='Update'){content=<Update><Update>}`
7. Update 컴포넌트는 Create와 똑같으니 복사해와서 수정하자.
```jsx
function Update(props){
  return<article>
    <form onSubmit={(event)=>{
      event.preventDefault();
      const title = event.target.title.value;
      const body = event.target.body.value;
      props.onUpdate(title,body)
    }}>
      <p><input type="text" name="title" placeholder="title"/></p>
      <p><textarea name="body" placeholder="body"></textarea></p>
      <p><input type="submit" value="Update"/></p>
    </form>
  </article>
}
```
8. 다른 점은 submit value값과 onCreate() 가 아닌 onUpdate() 함수를 호출해야함. 따라서 Update를 사용하는 조건문안의 <Update>에 onUpdate{(title,body)=>{}} 작성
---
9. Update 는 기존의 내용을 수정하는 것이기 때문에 form 안에 기존의 내용이 담겨있을 필요가 있다. 이는 조건문안의 <Update> 가 title 과 body 의 내용을 가지고 있어야 함.
`else if(mode==='Update'){content=<Update title={} body={} onUpdate{(title,body)=>{}><Update>}`
10. 그럼 이 title과 body 내용은 어떻게 가지고 올까? 이전 mode 가 Read 일 때 title 과 body 값을 알아내는 코드를 재사용하면 됨. 
```jsx
else if(mode==='Update'){
let title, body = null;
    for(let i=0; i < topics.length; i++){
      if(topics[i].id==id){
        title = topics[i].title;
        body = topics[i].body;
      }
    }
content=<Update title={title} body={body} onUpdate{(title,body)=>{}><Update>}
```
이렇게 하면 title과 body 값이 update 로 들어감. 이제 이 값을 컴포넌트에서 props 로 잡아 form에 나타나게 만들자
11. form 안의 input 과 textarea 의 밸류 값으로 각각을 넣는다. 
```jsx
      <p><input type="text" name="title" placeholder="title" value={props.title}/></p>
      <p><textarea name="body" placeholder="body" value={props.body}></textarea></p>
```
이제 제목과 본문이 나타난다. 하지만 추가로 작성이 안됨. 

12. props 란 사용자가 컴퍼넌트로 전달한 일종의 명령이다. 여기서 props를 `상태`로 바꿔야 한다. 
`state`는 내부자가 사용하는 데이터이다. 따라서 저 컴포넌트 안에서 props를 얼마든지 바꿀 수 있게 되는 것이다.
13. Update 에 `const[title,setTitle] = useState(props.title); const[body,setBody]= useState(props.body);` 쓴 후 
일전에 value 부분을 `title` 과 `body` 로 각각 변경해준다.  여전히 변경이 안된다. 
14. 이유는 그 form 과 연결된 state가 아직 바뀌지 않았기 때문, 그래서 여기에 onChange() 라는 이벤트를 사용한다. 이것은 html의 onChange와는 다르게 동작한다.
리액트에서는 값을 입력할때마다 onChange가 호출된다. 콘솔에 띄워보면 마지막 입력값만 더해져서 출력된다. 
15. 이제 방금 바뀐 그 값을 새로운 state로 바꿔야 한다. onChange={(event)=>setTitle(event.target.value)} 바디값도 똑같이 바꿀 수 있음.
16. `<p><textarea name="body" placeholder="body" value={body} onChange={(event)=>{setBody(event.target.value)}}></textarea></p>`
17. 정리하면 update 를 누르면 onSubmit 이 호출되면서 title과 body 값을 onUpdate 로 전달한다.
18. 이제 onUpdate 에 들어온 title 과 body 값으로 topics를 변경하면 된다.
19. onUpdate 함수 안에 수정된 내용을 포함한 객체형식을 만든다. const updatedTopic = {id:id, title:title, body:body} 여기 id는 read 당시 이미 세팅되어있음.
20. 바꿔야 하는 topics 는 배열이니까 복사본 만들어서 변경해야함. const newTopics =[...topics];
21. 이제 기존의 topics에서 id가 일치하는 것을 찾아야 한다
```jsx
const newTopics = [...topics]
const updatedTopic = {id:id, title:title, body:body}
for(let i=0; i < newTopics.length; i++){
  if(newTopics[i].id ===id){
    newTopics[i] = updatedTopic;
    break;
  }
}
setTopics(newTopics);
setMode('Read') 
```
22. 마지막 setMode('Read') 써서 상세보기까지 마무리 됨

## 완성코드
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
function Create(props){
  return<article>
    <form onSubmit={(event)=>{
      event.preventDefault();
      const title = event.target.title.value;
      const body = event.target.body.value;
      props.onCreate(title,body)
    }}>
      <p><input type="text" name="title" placeholder="title"/></p>
      <p><textarea name="body" placeholder="body"></textarea></p>
      <p><input type="submit" value="Create"/></p>
    </form>
  </article>
}
function Update(props){
  const [title,setTitle] = useState(props.title);
  const [body,setBody] = useState(props.body);
  return<article>
    <form onSubmit={(event)=>{
      event.preventDefault();
      const title = event.target.title.value;
      const body = event.target.body.value;
      props.onUpdate(title,body)
    }}>
      <p><input type="text" name="title" placeholder="title" value={title} onChange={(event)=>{setTitle(event.target.value)}}/></p>
      <p><textarea name="body" placeholder="body" value={body} onChange={(event)=>{setBody(event.target.value)}}></textarea></p>
      <p><input type="submit" value="Update"/></p>
    </form>
  </article>
}
function App() {
  const [mode,setMode] =useState('Welcome');
  const [id,setId] = useState(null); 
  const [nextId,setNextId] = useState(4);
  const [topics,setTopics] = useState([
    {id:1, title:"html", body:"html is..."},
    {id:2, title:"css", body:"css is..."},
    {id:3, title:"javascript", body:"javascript is..."}
  ])
  let content = null;
  let contextControl = null;
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
    contextControl =<li><a href='/update/' onClick={(event)=>{event.preventDefault(); setMode('Update'); }}>Update</a></li>
  }else if(mode==='Create'){
    content=<Create onCreate={(_title,_body)=>{
      
      const newTopic = {id:nextId, title:_title, body:_body}
      const newTopics =[...topics];
      newTopics.push(newTopic);
      setTopics(newTopics); 
    }}></Create>;
  }else if(mode==='Update'){
    let title, body = null;
    for(let i=0; i<topics.length; i++){
      if(topics[i].id==id){
        title = topics[i].title;
        body = topics[i].body;
      }
    }
    content = <Update title={title} body={body} onUpdate={(title,body)=>{
      const newTopics = [...topics];
      const updatedTopic = {id:id, title:title, body:body};
      for(let i=0; i < newTopics.length; i++){
        if(newTopics[i].id ===id){    
           newTopics[i] = updatedTopic;
           break;
          }
      }
      setTopics(newTopics);
      setMode('Read') 
    }}></Update> 
  }
  return (
    <div className="App">
      <Header title="React" onChangeMode={function(){
        setMode("Welcome");
      }}></Header>
      <Nav topics ={topics} onChangeMode={(_id)=>{ setMode("Read"); setId(_id);}}></Nav>
      {content}
      <ul>
        <li><a href="/create" onClick={(event)=>{event.preventDefault(); setMode('Create');}}>Create</a></li>
        {contextControl}
      </ul>
      
    </div>
  );
}

export default App;

```
