# 11. 비동기의 시작-콜백지옥
자바스크립트는 동기적 언어이다. 순차적 순서대로 코드가 실행되는데, 외부 네트워크에서 불러오는 코드가 시간이 걸리면 그 다음줄의 코드들은 실행되지 못한채 기다리게 된다. 이는 사용자경험을 떨어뜨린다. 이때 필요한 것이 비동기적 실행방법이다. 시간이 걸리는 코드는 진행이 되면서 동시에 다른 코드들도 실행이 되도록 한다.
    
```javascript
console.log('1'); // 동기
setTimeout(()=>console.log('2'),1000) //비동기
console.log('3'); //동기

//synchronous callback
function printImmediately(print){
    print();
}
printImmdeately(()=>console.log('hello')); //동기

//asynchronous callback
 function printwithDelay(print,timeout){
    setTimeout(print,timeout)
}
printwithDelay(()=>console.log('async callback'),2000); //비동기
 ```
    
    이 코드의 출력순서는 1,3,hello,2,async callback 순이다.
  # 12. Promise
  promise 는 자바스크립트 오브젝트로 상태 state 가 완료되기전에는 pending 완료후에는 fullfilled 로 표시된다. 만일 파일을 찾지 못했을 경우엔 rejected로 표시된다.
  원하는 데이터를 만들어내는 producer와 이 데이터를 소비하는 consumer로 나누어진다.
  예제코드에서는 서버통신할 때를 가정하여 setTimeout() 함수를 써줌.
```javascript
// 1. Producer
const promise = new Promise((resolve,reject)=>{
    // doing some heavy work (network, read files)
    console.log("doing something...")
    setTimeout(()=>{
    //resolve("lena")
    reject(new Error('no network'))
    },2000);
    })

// 2. Consumer : then, catch, finally
    promise
        .then((value)=>{
           console.log(value);
        })
        .catch((error)=>{
           console.log(error)
        })
        .finally(()=>{
           console.log('finally')
        })

// 3. promise chaining
const fetchNumber = new Promise((resolve,reject)=>{
        setTimeout(()=>resolve(1),1000);
    })
fetchNumber
.then(num=>num*2)
.then(num=>num*3)
.then(()=>{
    return new Promise((resolve,reject)=>{
        setTimeout(()=>resolve(num-1),1000)
    })
})
.then(num=>console.log(num));

// 4. Error handling
const getHen =()=>
    new Promise((resolve,reject)=>{
        setTimeout(()=>resolve('🐔'),1000)
    })
const getEgg = hen =>
    new Promise((resolve,reject)=>{
        setTimeout(()=>reject(new Error(`${hen}=>🥚`)),1000)
    })
const cook = egg =>
    new Promise((resolve,reject)=>{
        setTimeout(()=>resolve(`error ${egg}=>🍳`),1000)
    })

//getHen()
//.then(hen => getEgg(hen))
//.then(egg => cook(egg))
//.then(friedEgg => console.log(friedEgg));

// 위처럼 인자를 받아와서 바로 전달만 할경우 아래처럼 생략가능함.
 getHen() //
    .then(getEgg)
    .catch(error => {
        return '🥖';
    })
    .then(cook
    .then(console.log)




// 마크다운에서 이모지 넣는 방법은 : 한 뒤 이름검색하면 select list가 뜸 
```
    
시간이 걸리는 작업(network, read files)는 Promise로 비동기적으로 처리하는 것이 좋다.<br/>
Promise를 만드는 순간 그 안에 전달한 executor 함수는 자동적으로 바로 실행된다.<br/>
Promise에는 resolve와 reject라는 콜백함수를 받는 executor라는 콜백함수를 전달헤주어야 한다. <br/>

Consumers: then, catch, finally <br/>
.then() - 성공했을 때 <br/>
.catch() - 실패했을 때 <br/>
.finally() - 성공하든 실패하든 상관없이 호출된다. <br/>

.then()은 값을 바로 전달할 수도 있고, Promise룰 전달해도 된다. <br/>
콜백함수를 전달할 때 받아오는 값으로 바로 함수를 호출하는 경우에는 생략해서 적을 수 있다 <br/>
ex)
.then(meal => console.log(meal)) 를 <br/>
.then(console.log) 와 같이 적을 수 있다. <br/>
