# Create
## 목표
작성글 생성해서 목록에 업데이트하기. </br>
글생성 버튼을 누르면 글을 작성할 수 있는 form이 뜨고 작성 후 sumit 버튼을 누르면 목록에 제목과 함께 업데이트 된다.</br>
동시에 업데이트한 글의 제목과 본문내용의 상세보기 페이지가 뜬다.

## 코딩 과정
1. 제일 먼저 return 값 맨 밑에 링크를 만들어줌 `<a href="/create">Create</a>` 원래 글쓰기, 작성, 등을 누르면 글쓰기 페이지로 넘어가듯이 create 페이지로 넘어가면 되는데,
2. 우리는 지난 강의까지 mode 를 이용하여 페이지를 바꾸고 있다. Nav 목록을 이용하여 업데이트 하는 것을 해보겠다.
3. 따라서 mode 값이 create 로 바뀌면 create 에 해당하는 ui 가 나타나도록 해볼 것이다.
4. 우선 create 링크를 클릭하면 기본적으로 url이 바뀌는 것을 방지하는 코드도 쓰고, mode를 Create 으로 세팅한다. 
`<a href="/create" onClick={(event)=>{event.preventDefault(); setMode("Create")}}>Create</a>`
---
5. mode 가 Create 으로 바뀌면 App() 다시 실행되고, if 문에 하나도 걸리지 않으니 Create 에 맞는 조건문을 추가한다.
6. `else if(mode==="Create"){content=<Create></Create>}` 이 경우 create ui가 많으니 컴포넌트로 따로 빼서 만들 것이다.
7. ```jsx
   function Create(){
   return <article>
           <form>
             <p><input type="text", name="title" placeholder="title"/></p>
             <p><textarea><input="text", name="body" placeholder="body"></textarea></p>
             <p><input type="submit" value="Create"></p>
           </form> 
         </article>
   }
---
8. 그렇다면 create 버튼을 눌렀을때 어떤 작업이 이루어져야 할까? create 버튼을 눌렀을때 후속작업을 할 수 있는 페이지로 갈 수 있도록 함수를 전달하기.
9. `else if(mode==="Create"){content=<Create onCreate={()=>{}}></Create>}` 
근데 여기서 `onCreate`은 react 기본함수이름일까 아님 이 케이스에 맞게 생성된 이름일까? 궁금..
10. 이 콜백함수는 title과 body값을 받을 것이다. `else if(mode==="Create"){content=<Create onCreate={(title,body)=>{}}></Create>}`
11. 그렇다면 이제 `onCreate` 함수를 어떻게 불러낼것인가
12. 다시 Create 컴포넌트로 돌아가서 submit 버튼을 눌렀을때 자바스크립트가 실행되게 하는 좋은 타이밍(?)은 form 태그에 `onSubmit` 이라는 prop을 제공하는 것이다.
13. onSubmit 은 submit 버튼을 눌렀을 때 form 태그에서 발생하는 event 임. 따라서 submit 을 했을때 페이지가 reload 된다. 이를 방지하는 코드는 이전에 배운 event.preventDefault();
`<form onSubmit={(event)=>{event.preventDefault();}}></form>`
14. 이제는 이 event 함수 안에서 form의 name이 title, body 인 값을 받아오겠다.
15. 이는 event.target 으로 받아올 수 있는데 target은 이 event 를 발생시킨 태그를 말한다. 즉 submit 버튼을 누르는 이벤트를 발생시킨 태그는 form 태그임. 
form 태그안에 이름이 title과 body 인 값을 받아오는 방법은 아래와 같다.
16. `<form onSubmit={(event)=>{event.preventDefault(); const title = event.target.title.value; const body = evnet.target.body.value;}}></form>`
---
17. 이제 가져온 이 title 과 body의 value 를 Create 사용자에게 공급하면 된다. 그렇다면 사용자는 어떻게 Create 로부터 submit 된 정보를 제공받을까? 
=> 바로 onCreate() => 이것은 prop이다.여기 설명 100퍼 이해 안됨..
18. 그래서 Create() 함수에 인자로 props를 받고 이 props를 통해 onCreate(); 함수를 호출할 것임. 이때 인자값은 title 과 body이다. props.onCreate(title,body);
19. 이것이 실행되면 onCreate()가 가리키는 함수가 실행될 것이고 그 함수의 title과 body 값을 통해서 사용자가 입력한 title과 body 값을 Create 컴포넌트 사용자에게 공급할 수 있다.???
뭔 소리인가...
---
20. 이젠 topics 배열에 새로운 원소를 추가하여 submit 된 title 이 목록에 추가되도록 해보겠다.
21. 이를 위해 topics 의 상태변환이 필요하기 때문에 useState를 `상태`로 승격시키겠다. topics는 `읽을때` `바꿀때는` setTopics를 쓰도록 `읽기`와 `쓰기`의 인터페이스를 추가함.
22. ```jsx
    const topics = [
    {id:1, title:"html", body:"html is..."},
    {id:2, title:"css", body:"css is..."},
    {id:3, title:"javascript", body:"javascript is..."}]
23. ```jsx
    const [topics,setTopics] = useState([
    {id:1, title:"html", body:"html is..."},
    {id:2, title:"css", body:"css is..."},
    {id:3, title:"javascript", body:"javascript is..."}])
24. 이제 topics 에 들어갈 새로운 객체를 만들어 보자.
25. onCreate(title,body) 함수 안에 title 과 body를 받아 새 객체 생성 => `const newTopic = {title:title, body:body};`
26. 그 다음 topics 안의 id 값을 어떻게 처리할 것인가? 여러가지 방법이 있지만 여기서는 임시적으로 새로운 객체의 id를 따로 관리해보도록 하자.
27. const [nextId,setNextId] = useState(4); 이미 3까지 id 가 있으니 4를 기본값으로 세팅 (계속 업데이트 될 상태이니 useState 를 써서 세팅하나봄.)
28. 이 nextId 값을 새로운 객체의 id 로 사용할 수 있게 됐다.  `const newTopic = {id:nextId, title:title, body:body};`
29. 이제 topics 에 push 만 해주면 되는데 `topics.push(newTopics);` 라고 하면 동작하지 않는 이유는 topics는 읽을 때 쓰는 것으로 바꿔두었기때문에 setTopics를 쓰면된다.
30. 그런데 setTopics(newTopic)을 넣어줘도 동작하지 않는 이유는? 변경할 value의 자료형에 따라 처리방법이 달라져야 하기 때문이다. 
이 경우에 업데이트하고자하는 topics는 여러객체가 들어있는 배열이기 때문에 primitive 자료형(string,number,bigint,boolean,undefined,symbol,null) 등과 처리방법이 다르다.
31. 밸류가 객체나 배열이라면 value를 복제하여 복제본을 setValue 할때에 그제서야 컴포넌트가 다시 실행된다.
32. `const newTopics = [...topics]; newTopics.push(newTopic); setTopics(newTopic); 
까지 해서 전달하면 오리지날 topics 와 새로들어온 복제본 newtopic이 같은지 다른지 비교해서, 다르다면 그때서야 비로서 컴포넌트를 다시 렌더링 해주게 된다.
33. 리액트는 오리지널 밸류와 새로들어온 데이터가 같다면 구지 다시 렌더링 하지 않는다. 
const[value,setValue]=useState(1); setValue(2); 는 렌더링 되지만,
const[value,setValue]=useState([1]); value.push(2); setValue(value); 는 렌더링 안된다. 오리지널 value를 넣어준것이기 때문에..(그럼 push된 2는???)
따라서 const [value.setValue]=useState([1]); newValue = [...value]; newValue.push(2); setValue(newValue); 해야 렌더링 됨. 
쉽게 말해서 value가 배열이나 객체처럼 복합적인 값일 경우에는 오리지널 데이터의 복사본을 만들어 사용해야 동작한다.
---
34. 마지막으로 글이 잘 추가됐는지 확인할 수 있게 상세페이지로 이동시키는 작업을 한다.
35. `setMode("Read");` =>읽기로 모드변환 `setId(nextId);` =>지금 우리가 추가한 글 id 지정 `setNextId(nextId+1);` =>다음에 글을 추가할때를 대비해서 id 지정
