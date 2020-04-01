# ES7 async/await
js有很多**异步处理**的解决方案，async/await就是其中的一种，相较ES6的Promise要更加的优雅  
顾名思义，async是异步的意思，自然用于声明一个function是异步的，而await是等待的意思，用于等待一个异步方法执行完成  
## async
async关键字修饰的函数其实返回的就是一个Promise对象，如果function中返回的是一个值，async直接会用Promise.resolve()包裹一下返回
````
async function f() {
    return 1;
}
f().then((res) => {
    console.log(res)
}) // 1
````
## await
await等待，MDN定义它是等待一个表达式，既然是表达式，那么就可以是一个常量、变量、函数甚至promise等
````
function getA(){
    return "hello";
}
async function getB(){
    return Promise.resolve("await");
}
aysnc function test(){
    const v1=await getA();
    const v2=await getB();
    console.log(v1,v2);
}
test(); //hello await
````
### 为什么await关键字只能在async函数中用？
await操作符等的是一个返回的结果，那么如果是同步的情况，那就直接返回了  
那如果是异步的情况呢，异步的情况下，**await会阻塞整一个流程**，直到结果返回之后，才会继续下面的代码      
阻塞代码是一个很可怕的事情，而async函数，会被包在一个promise中，异步去执行。所以await只能在async函数中使用，如果在正常程序中使用，会造成整个程序阻塞，得不偿失

### async/await的错误处理
Promise除了resolve,还有reject,而await只会等待一个结果，发生错误了，该如何处理？
* try-catch
````
async function testA() {
  try {
    await Promise.reject('错误');
  } catch (err) {
    console.log(err);
  }
}
testA(); // 错误
````
* 用promise的catch来做错误捕捉
````
async function testB() {
    await Promise.reject('错误').catch((err) => {
        console.log(err);
    });
}
testB(); // 错误
````
### async/await和Promise的区别
promise最大的问题就是在于业务复杂之后，then内部的逻辑也变得复杂，或者循环的异步嵌套场景等，会写出来不那么优美  
有大牛就提出async/await其实就是Promise的语法糖  
举个例子:
````
function takeLongTime(n) {
    return new Promise(resolve => {
        setTimeout(() => resolve(n), n);
    });
}
function step1(n) {
    console.log(`step1 with ${n}`);
    return takeLongTime(n);
}

function step2(m, n) {
    console.log(`step2 with ${m} and ${n}`);
    return takeLongTime(m + n);
}

function step3(k, m, n) {
    console.log(`step3 with ${k}, ${m} and ${n}`);
    return takeLongTime(k + m + n);
}
````
takeLongTime起到的作用就是延时之后给出延时的数据  
step1代表第一步延时了多久  
step2代表第一步和第二部总共延时了多久  
step3代表第一步、第二步和第三步一共延时了多久  
* Promise版本
````
function doIt() {
    console.time("doIt");
    const time1 = 300;
    step1(time1)  //time1:300
        .then(time2 => {  //then捕获step1的resolve,所以time2:300
            return step2(time1, time2) //time1:300,time2:300
                .then(time3 => [time1, time2, time3]); //捕获step2的resolve,time3= time1+time2 = 600 
        })
        .then(times => {  //times:[300,300,600]
            const [time1, time2, time3] = times;
            return step3(time1, time2, time3); 
        })
        .then(result => {
            console.log(`result is ${result}`); //result:300+300+600=1200
            console.timeEnd("doIt");
        });
}

doIt();
````
* async/await版本
````
async function doIt() {
    console.time("doIt");
    const time1 = 300;
    const time2 = await step1(time1);
    const time3 = await step2(time1, time2);
    const result = await step3(time1, time2, time3);
    console.log(`result is ${result}`);
    console.timeEnd("doIt");
}

doIt();
````
## 一个async函数，然后利用generators和promises去实现相同功能
````
async function init(){
    const res1 = await doTask1();

    const res2 = await doTask2();

    const res3 = await doTask3();
}
init();
````
````
function runner(genFn){
    const iter = genFn();

    return function run(arg){
     //首先生成器函数返回的是一个遍历器，可以往下next()执行不同阶段，每个阶段有value,done
       let result = iter.next(arg); 
       if(result.done){
           return result.value; //生成器迭代完了
       }else{
           //没有迭代到最后一个阶段，比如第一个阶段，异步返回值后，才能执行then方法捕获，第二个阶段再next()
           return Promise.resolve(result.value).then(run);
       }
    }
}  

runner(function* (){
    const res1 = yield doTask1();
    const res2 = yield doTask2(res1);
    const res3 = yield doTask3(res2);
    return res3;
})
````