# 数组相关
## 写一个函数 squireArr，其参数是一个数组，作用是把数组中的每一项变为原值的平方
````
var arr = [3, 4, 6]    
function squireArr(arr) {
 window.arr = arr.map(item =>{
     return item*item;
 }) //新数组得覆盖全局里的旧数组
}
squireArr(arr)
console.log(arr) // [9, 16, 36]
````
**注意：当数组中元素是值类型，map不会改变原数组；当是引用类型，则可以改变原数组**
## 写一个函数 squireArr，其参数是一个数组，返回一个新的数组，新数组中的每一项是原数组对应值的平方，原数组不变
````
var arr = [3, 4, 6]
function squireArr(arr) {
  return arr.map(item=> item * item);    
}
var arr2 = squireArr(arr)
console.log(arr) // [3, 4, 6]
console.log(arr2) // [9, 16, 36]
````

## 遍历数组，打印数组里的每一项的平方
````
var arr = [3,4,5];
for(let i of arr){
   console.log(i*i)
}
````
## 数组的哪些API会改变原数组
splice()、reverse()、fill()、sort()、push()、pop()、unshift()添加元素到开头、shift()删除开头元素
## 写一个函数，操作数组，返回一个新数组，新数组中只包含正数
````
function filterPositive(arr) {
    return arr.filter(function(val){
        return val>0 && typeof(val) == 'number';
    })
}
var arr = [3, -1, 2, true]
filterPositive(arr)
console.log(filterPositive(arr))
````
##  用splice函数分别实现push、pop、shift、unshift方法
````
/**
* @param {number} index 索引
* @param {number} how  1删除 0不删除
* @param {any}    item 可添加项
**/
var arr = ['b','c','d'];
arr.splice(arr.length,0,'e'); //e被添加到数组最后
arr.splice(arr.length-1,1); //数组末尾删除一个元素
arr.splice(0,1) //删除从头起的元素，删除一个
arr.splice(0,0,'a') //'a'添加到数组最前面
````
## 取数组的最大值（ES5、ES6）？
````
//ES5
Math.max.apply(null,[14,3,77,30])

//ES6
Math.max(...[14,3,77,30])

//reduce
[14,3,77,30].reduce((accumulator, currentValue)=>{
   return accumulator = accumulator > currentValue ? accumulator : currentValue
});
````
## 数组扁平化 实现flatten([1, [2], [3, [[4]]]]) => [1, 2, 3, 4];
````
var arr = [1, [2], [3, [[4]]]];
function flatten(arr) {
    return arr.reduce(function (pre, curr) {
        return pre.concat(Array.isArray(curr) ? flatten(curr) : curr)
    }, [])
}
console.log(flatten(arr));
````

# 对象相关
## js有哪些内置对象
数据封装类对象：Object、Array、Boolean、Number、String  
其他对象：Function、Arguments、Math、Date、RegExp、Error
## 遍历 company 对象，输出里面每一项的值
````
var company = {
    name: "qdywxs",
    age: 3,
    sex: "男"
}
for(let key in company){
    console.log(company[key])
}
    
Object.keys(company).forEach((key)=>{
     console.log(company[key]) 
})
````
## 以下代码输出
````
var name = "sex"
var company = {
    name: "qdywxs",
    age: 3,
    sex: "男"
}
console.log(company[name]) //"男"

var name = "sex"
var company = {
    name: "qdywxs",
    age: 3,
    sex: "男"
}
console.log(company.name) //"qdywxs"
````
## 使用递归完成 1 到 100 的累加？
````
var sum = 0;
function add(num){
    if(num<=100){
        sum+=num;
        num++;
        arguments.callee(num);// 与add(num)一样
    }
    return sum;
}
var result = add(1);
alert(result)
````
## 如下代码输出多少？如果想输出 3，那如何改造代码？
````
var fnArr = [];
for(var i=0; i<10; i++) {
    fnArr[i] =  function() {
        return i
    };
}
console.log(fnArr[3]()) //10

改造:var变成let
````
````
for(var i=0; i<5; i++){
  setTimeout(function(){
    console.log('delayer:' + i )
  }, 0)
}

输出结果为：delayer:5（连续输出5个），执行setTimeout时，代码会挂到任务队列中，待i遍历完成之后执行，而此时全局中i = 5
````
````
function makeCounter() {
    var count = 0
    return function() {
       return count++
    };
}
var counter = makeCounter()
var counter2 = makeCounter();
console.log(counter()) // 0
console.log(counter()) // 1
console.log(counter2()) // 0
console.log(counter2()) // 1
````
## 补全代码，实现数组按姓名、年纪、任意字段排序
````
var users = [
    {name: "John", age: 20, company: "Baidu"},
    {name: "Pete", age: 18, company: "Alibaba"},
    {name: "Ann", age: 19, company: "Tecent"}
]
    
users.sort(byField("age"))
users.sort(byField("company"))

function byField(field){
    return function(user1,user2){
        return user1[field] > user2[field]
    }
}
````