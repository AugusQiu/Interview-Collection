https://juejin.im/post/5a8e9b6d5188257a5f1ed826
# js实现私有属性和方法
命名约定默认 **以下划线_为前缀**的变量是私有变量，但这只是开发者之间的默契
## Symbol
可以使用 Symbol 作为 key 的方式创建实例上的属性。这可以防止该属性在遍历或使用 JSON.stringify 时可见,不过这种技术需要为每个私有属性创建一个 Symbol。如果您在类外可以访问该 Symbol，那你还是可以拿到这个私有属性
````
const widthSymbol = Symbol('width');
const heightSymbol = Symbol('height');

class Shape {
  constructor(width, height) {
    this[widthSymbol] = width;
    this[heightSymbol] = height;
  }
  get area() {
    return this[widthSymbol] * this[heightSymbol];
  }
}

const square = new Shape(10, 10);
console.log(square.area);         // 100
console.log(square.widthSymbol);  // undefined
console.log(square[widthSymbol]); // 10
````
## 闭包
````
function Shape() {
  // 私有变量集
  const this$ = {};

  class Shape {
    constructor(width, height) {
      this$.width = width;
      this$.height = height;
    }

    get area() {
      return this$.width * this$.height;
    }
  }

  return new Shape(...arguments);
}

const square = new Shape(10, 10);
console.log(square.area);  // 100
console.log(square.width); // undefined
````
## Proxy
Proxy允许你有效地将对象**包装在名为 Proxy 的对象中，并拦截与该对象的所有交互**  
Proxy 可以拦截许多不同类型的交互，但我们要关注的是 get 和 set，Proxy 允许我们分别拦截对一个属性的读取和写入操作。创建 Proxy 时，你将提供两个参数，第一个是您打算包裹的实例，第二个是您定义的希望拦截不同方法的 “处理器” 对象
````
class Shape {
  constructor(width, height) {
    this._width = width;
    this._height = height;
  }
  get area() {
    return this._width * this._height;
  }
}

const handler = {
  get: function(target, key) {
    if (key[0] === '_') {
      throw new Error('Attempt to access private property');
    }
    return target[key];
  },
  set: function(target, key, value) {
    if (key[0] === '_') {
      throw new Error('Attempt to access private property');
    }
    target[key] = value;
  }
}

const square = new Proxy(new Shape(10, 10), handler);
console.log(square.area);             // 100
console.log(square instanceof Shape); // true
square._width = 200;                  // 错误：试图访问私有属性
````
## TypeScript处理
TypeScript 是 JavaScript 的一个超集，它会编译为原生 JavaScript 用在生产环境。允许指定私有的、公共的或受保护的属性是 TypeScript 的特性之一
````
class Shape {
  private width;
  private height;

  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  get area() {
    return this.width * this.height;
  }
}
const square = new Shape(10, 10)
console.log(square.area); // 100
````
使用 TypeScript 需要注意的重要一点是，它只有在 编译 时才获知这些类型，而私有、公共修饰符在编译时才有效果。如果你尝试访问 square.width，你会发现，居然是可以的。只不过 TypeScript 会在编译时给你报出一个错误，但不会停止它的编译