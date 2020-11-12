[Promise底层实现](https://www.jianshu.com/p/4f3bef72758c)
````
let executeAsync
// node环境
if(typeof process == 'object' && process.nextTick){
    executeAsync = process.nextTick
}else if(typeof setImmediate == 'function'){
    executeAsync = setImmediate
}else{
    executeAsync = function (fn) {setTimeout(fn, 0)}
}

function callAsync(fn,arg,callback,onError){
    executeAsync(function(){
        try{
           callback ? callback(fn(arg)):fn(arg)
        }catch(e){
           onError(e)
        }
    })
}

//判断是否为function
const isFunction = variable => typeof variable === 'function'

//定义Promise的三种状态常量
const PENDING   = 'PENDING'
const FULFILLED = 'FULFILLED'
const REJECTED  = 'REJECTED'

class MyPromise{
    constructor(handle){
        if(!isFunction(handle)){
            throw new Error('myPromise must accept a function as a paramepter')
        }
        // 添加状态
        this._status = PENDING
        this._value  = undefined

        this._fulfilledQueues = [] //成功回调函数队列
        this._rejectedQueues  = [] //失败回调函数队列
        
        try{
            handle(this._resolve.bind(this),this._reject.bind(this))
        }catch(err){
            this._reject(err)
        }
    }
    
    _resolve(val){
       
       const run = () =>{
           if(this._status !== PENDING) return
           this._status = FULFILLED

           //依次执行成功队列中的函数，并清空队列
           const runFulfilled = (value)=>{
               let cb;
               while(cb = this_fulfilledQueues.shift()){
                   cb(value)
               }
           }

           //依次执行失败队列中的函数，并清空队列
           const runRejected = (error)=>{
               let cb;
               while(cb = this._rejectedQueues.shift()){
                   cb(error)
               }
           }

           if(val instanceof MyPromise){
               val.then(value=>{
                   this._value = value
                   runFulfilled(value)
               },err => {
                   this._value = err
                   runRejected(err)
               })
           }else{
               this_value = val
               runFulfilled(val)
           }
       }
       setTimeout(run,0)
    }

    _reject(err){
        if(this._status !== PENDING) return
        const run = ()=>{
          this._status = REJECTED
          this._value  = err
          let cb
          while(cb = this._rejectedQueues.shift()){
              cb(err)
          }
        }
        setTimeout(run,0)
    }

    then(onFulfilled,onRejected){
        const {_value,_status} = this

        // 返回一个新的Promise对象
        return new MyPromise((onFulfilledNext, onRejectedNext) => {
            // 封装一个成功时执行的函数
            let fulfilled = value =>{
               
            }
        })
    }
}
````