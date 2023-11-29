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
이 코드의 출력순서는 1, 3, hello, 2, async callback 순이다.

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

# 13. async await
clear style of using Promise
```javascript
// 1. async
    function fetchUser(){
    // do network requerst in 10 sec...
        return 'lena';
    }
    const user = fetchUser();
    console.log(user);
    // 이러한 코드는 network request 가 10초 걸리는 동안 그 다음줄의 코드가 실행되지 못하기 때문에 비동기적 처리를 해주어야 한다.
    // 만일 이 다음줄에 ui를 보여주는 코드가 있다면 user는 텅텅비어있는 브라우저를 10초동안 보게 될것이다.
    // 이를 Promise 를 이용해 비동기적으로 처리해주면 아래와 같다.
    function fetchUser(){
        return new Promise((resolve,reject)=>{
        // do network requerst in 10 sec...
        resolve('lena');
        })
    }
    const user = fetchUser();
    user.then(console.log)
    console.log(user);

    // 이를 async 를 이용해 비동기적으로 처리해주면 아래와 같다.
    // function 앞에 async를 붙여주면 코드블럭이 Promise로 바뀌어 비동기적으로 작동됨. => syntatic sugar
    async function fetchUser(){
        // do network requerst in 10 sec...
        return 'lena';
        )
    }
    const user = fetchUser();
    user.then(console.log)
    console.log(user);

// 2. await : async 가 붙은 함수 안에서만 쓰일 수 있음. 이때 await 은 delay()함수 실행이 끝날때까지 기다려줌

    function delay(ms){
        return new Promise(resolve => setTimeout(resolve,ms));
    }
    async function getApple(){
        await delay(2000);
        return '🍎';
    }
    async function getBanana(){
        await delay(1000);
        return '🍌';
    }
    // 위와 같은 역할의 코드이지만 이렇게 chaining을 하는 것보단 위의 코드처럼 `동기적인 코드를 쓰는 것처럼` 쓰게되면 코드가 가벼우면서 더 쉽게 이해가 됨
    // function getBanana(){
    //   return delay(1000)
    //   .then(()=>'🍌');
    // }

    // chaining 한 코드 = 콜백지옥
    function pickFruit (){
        return getApple().then((apple)=>{
            return getBanana().then((banana)=> `${apple} + ${banana}`)
        })
    }
    // async 코드로 바꾸기
    // 위와 같은 역할의 코드이지만 이렇게 chaining을 하는 것보단 위의 코드처럼 `동기적인 코드를 쓰는 것처럼` 쓰게되면 코드가 가벼우면서 더 쉽게 이해가 됨
    async function pickFruit(){
        const apple = await getApple(); //사과 따는데 1초 기다림
        const banana = await getBanana(); // 사과 땄으면 바나나 땀.
        return `${apple} + ${banana}`;
    }
    pickFruit().then(console.log)

    // 병렬처리
    // 위 코드의 문제는 사과와 바나나를 가져오는데 아무런 연관성이 없기 때문에 차례로 기다릴 필요가 없으니 병렬처리를 해주면 좋음.
    async function pickFruit(){
        const applePromise = getApple(); // 바로 호출
        const bananaPromise = getBanana(); // 바로 호출 
        const apple = await applePromise; // 기다렸다 오면 실행 
        const banana = await bananaPromise; // 기다렸다 오면 실행
        return `${apple} + ${banana}`;
    }
    pickFruit().then(console.log)

// 3. useful Promise APIs 
    // 즉 각각의 함수를 불러오는데 서로 연관성이 전혀 없는 경우에 위처럼 코드를 작성하는것보다는
    // promise 에서 제공하는 useful Promise APIs 이용 : Promise.all
    // promise 배열을 전달하게 되면 모든 배열을 받을 때까지 병렬적으로 처리해서 모아주는 API

    function pickallFruits (){
        return Promise.all([getApple(), getBanana()]).then((fruits)=>fruits.join("+"))
    }
    pickallFruits().then(console.log);

    // Promise.race는 전달되는 배열안에서 가장먼저 전달받은 것만 반환해줌.
    function pickonlyOne (){
        return Promise.race([getApple(),getBanana()]);
    }
    pickonlyOne().then(console.log);
```
