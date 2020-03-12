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
## 遍历数组，打印数组里的每一项的平方
````
var arr = [3,4,5];
for(let i of arr){
   console.log(i*i)
}
````
# 对象相关
## js有哪些内置对象
数据封装类对象：Object、Array、Boolean、Number、String  
其他对象：Function、Arguments、Math、Date、RegExp、Error
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