# js闭包
js的闭包(Closure)跟执行上下文中的变量对象和作用域链有着千丝万缕的关系。**闭包是指其所在执行上下文已经出栈，但仍访问了其所在执行上下文变量对象的函数**  
也就是说，**闭包是一个特殊的函数**，【其所在执行上下文】指的是闭包所在函数对应的上下文，而不是闭包本身所对应的执行上下文
````
//一个简单的产生闭包的例子:函数A中创建了函数B并且返回函数B,当函数B执行时，如果访问了A中的变量，就产生了闭包
function A(){
    var a = 2;
    function B(){
        console.log(a)
    }
    return B;
}
A()();  // 2，注：A()返回的是B函数本身，并未打印，A()()才会执行B函数里的打印
````
大多数书，对闭包的解释为:可以读取其它函数内部变量的函数。上述代码，就以函数B指代闭包，而在 Chrome 的开发者工具中，则会以函数 A 指代闭包。  
那么闭包的本质到底是什么？  
首先，js拥有自动的垃圾回收机制，当一个值失去引用的时候，垃圾回收机制会根据特殊的算法找到它并将其回收  
**函数的执行上下文在出栈后，其变量对象会失去引用等待被回收，而闭包的存在会阻止这一过程，因为闭包的作用域链包含了其所在执行上下文的变量对象**  
````
var a = 1;
function fn1(){
    var b = 2;
    function fn2(){
        return b;
    }
    return fn2;
}

var fn2=fn1();
fn2();
````
上述代码，执行上下文栈的行为如下:
````
//代码执行，首先进入的是全局环境，全局上下文被创建并入栈
ECStack.push(globalContext);

//fn1被调用，fn1函数上下文被创建并入栈
ECStack.push(<fn1> functionContext);

/fn1 执行完毕，fn1 函数上下文出栈
ECStack.pop();

// fn2 被调用，fn2 函数上下文被创建并入栈
ECStack.push(<fn2> functionContext);

// fn2 执行完毕，fn2 函数上下文出栈
ECStack.pop();

// 代码执行完毕，全局上下文出栈
ECStack.pop();
````
所以，其实**在fn2函数执行的时候，fn1 上下文已经出栈了**，按照 javascript 的垃圾回收机制，fn1 上下文的变量对象失去引用后会被垃圾回收机制回收，但由于在 fn2 上下文的作用域链包含了 fn1 上下文的变量对象，所以 fn1 上下文的变量对象不会被垃圾回收机制回收  
函数作用域在函数被定义(声明)的时候确定的。每个函数都包含一个[[scope]]内部属性，在函数被定义的时候，该函数的 [[scope]] 属性会保存其上层上下文的变量对象，形成包含上层上下文变量对象的层级链  
fn2函数被定义时，其上层上下文就包括fn1上下文和全局上下文
````
fn2.[[scope]]=[ fn1Context.VO , globalContext.VO ]
````
当 fn2 被调用的时候，其执行上下文被创建并入栈，此时会生成变量对象并将该变量对象添加进作用域链的顶端，并且将 [[scope]] 添加进作用域链
````
fn2Context.Scope=[fn2Context.VO].concat([[scope]])
=>
fn2Context.Scope=[fn2Context.VO , fn1Context.VO, globalContext.VO]
即
fn2Context={
    scope:[fn2Context.VO , fn1Context.VO , globalContext.VO]
}
````
可见，**fn2 上下文的作用域链中包含了 fn1 上下文的变量对象，并且由于 fn2 访问了 fn1 中的变量，所以阻止了 fn1 上下文的变量对象被垃圾回收机制回收**  
一个经典面试题  
````
var arr = [];
for (var i = 0; i < 3; i++) {  //全局作用域中i只有一个值3
    arr[i] = function () {
        console.log(i);
    };
}
arr[0](); //3
arr[1](); //3
arr[2](); //3
````
上面代码的输出结果都是3.分析一下:
````
//在 arr[0] 函数执行之前,全局上下文的变量对象如下:
globalContext = {
    VO: {
        arr: [...],
        i: 3
    }
}

//在 arr[0] 被调用执行时，其作用域链在函数上下文的创建阶段被创建，其作用域链如下
arr[0]Context = {
    Scope: [arr[0]Context.VO, globalContext.VO]
}

根据作用域链，arr[0] 函数会现在自身的变量对象中寻找 i ，如果找不到，会到全局上下文变量对象中寻找 i，所以最终输出 3
arr[1]、arr[2] 的分析与上述一致
````
利用闭包修改上面代码，使得输出结果是0 1 2
````
var arr = [];
for (var i = 0; i < 3; i++) { 
    arr[i] = (function (i) {
        return function(){
            console.log(i);
        }
    })(i);  //立执行函数，闭包会自动执行
}
arr[0]();  //  0
arr[1]();  //  1
arr[2]();  //  2
````
分析一下:
````
在 arr[0] 函数执行前，全局上下文的变量对象跟之前一样没有任何变化
globalContext = {
    VO: {
        arr: [...],
        i: 3
    }
}

而在 arr[0] 被调用执行时，其作用域链在函数上下文的创建阶段被创建，其作用域链如下
arr[0]Context = {
    Scope: [arr[0]Context.VO, 匿名函数Context.VO, globalContext.VO]
}

可以看到其作用域链发生了变化，arr[0] 的变量对象之后紧跟着匿名函数的变量对象

匿名函数Context = {
    VO: {
        Arguments:{
            0:0,
            length:1
        },
        i: 0
    }
}
在匿名函数的变量对象中，能找到访问的变量 i，所以得到 i 值为 0.
arr[1]、arr[2] 的分析与上述一致，得到的 i 值分别为 1、2
````