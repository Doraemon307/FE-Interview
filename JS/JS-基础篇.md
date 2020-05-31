### 1. JS原始数据类型有哪些？引用数据类型有哪些？
- 在 JS 中，存在着 6 种原始值，分别是：
1. boolean
2. null
3. undefined
4. number
5. string
6. symbol（ES6新增）
- 引用数据类型: 对象Object <br>
包含普通对象-Object，数组对象-Array，正则对象-RegExp，日期对象-Date，数学函数-Math，函数对象-Function
### 2. typeof类型判断
- typeof 对于原始类型来说，除了 null 都可以显示正确的类型
```
typeof 1 // 'number'
typeof '1' // 'string'
typeof undefined // 'undefined'
typeof true // 'boolean'
typeof Symbol() // 'symbol'
```
> `typeof null   //'object'  `
- typeof 对于对象来说，除了函数都会显示 object，所以说 typeof 并不能准确判断变量到底是什么类型
```
typeof [] // 'object'
typeof {} // 'object'
typeof console.log // 'function'
```
### 3. 如何判断一个对象的正确类型？
如果我们想判断一个对象的正确类型，这时候可以考虑使用 instanceof，因为内部机制是通过**原型链**来判断的
```
const Person = function() {}
const p1 = new Person()
p1 instanceof Person // true

var str = 'hello world'
str instanceof String // false

var str1 = new String('hello world')
str1 instanceof String // true
```
> 对于原始类型来说，你想直接通过 instanceof来判断类型是不行的
#### instanceof判断基本数据类型的方法：
```
class PrimitiveNumber {
  static [Symbol.hasInstance](x) {
    return typeof x === 'number'
  }
}
console.log(111 instanceof PrimitiveNumber) // true
```
其实就是自定义instanceof行为的一种方式，这里将原有的instanceof方法重定义，换成了typeof，因此能够判断基本数据类型。

### 4. 手动实现一下instanceof的功能？
**核心: 原型链的向上查找。**
```
function myInstanceof(left, right) {
    //基本数据类型直接返回false
    if(typeof left !== 'object' || left === null) return false;
    //getProtypeOf是Object对象自带的一个方法，能够拿到参数的原型对象
    let proto = Object.getPrototypeOf(left);
    while(true) {
        //查找到尽头，还没找到
        if(proto == null) return false;
        //找到相同的原型对象
        if(proto == right.prototype) return true;
        proto = Object.getPrototypeof(proto);
    }
}
```
测试：
```
console.log(myInstanceof("111", String)); //false
console.log(myInstanceof(new String("111"), String));//true
```

### 5. Object.is 和 === 以及 ==的区别？
- Object.is 和 === 的区别：
Object在严格等于的基础上修复了一些特殊情况下的失误：
主要的区别就是`+0！=-0 而NaN==NaN`
- === 和 == 的区别：
1. === 叫做严格相等，是指：左右两边不仅值要相等，类型也要相等
2. == 不像===那样严格，对于一般情况，只要值相等，就返回true，但==还涉及一些类型转换，它的转换规则如下：
- 两边的类型是否相同，相同的话就比较值的大小，例如1==2，返回false
- 判断的是否是null和undefined，是的话就返回true
- 判断的类型是否是String和Number，是的话，把String类型转换成Number，再进行比较
- 判断其中一方是否是Boolean，是的话就把Boolean转换成Number，再进行比较
- 如果其中一方为Object，且另一方为String、Number或者Symbol，会将Object转换成字符串，再进行比较

### 6. 对象转原始类型是根据什么流程运行的？
对象转原始类型，会调用内置的[ToPrimitive]函数，对于该函数而言，其逻辑如下：
1. 如果Symbol.toPrimitive()方法，优先调用再返回
2. 调用valueOf()，如果转换为原始类型，则返回
3. 调用toString()，如果转换为原始类型，则返回
4. 如果都没有返回原始类型，会报错
> 可以对这些函数进行重定义。
```
var obj = {
  value: 3,
  valueOf() {
    return 4;
  },
  toString() {
    return '5'
  },
  [Symbol.toPrimitive]() {
    return 6
  }
}
console.log(obj + 1); // 输出7
```
#### 应用：让if(a == 1 && a == 2)条件成立？
```
var a = {
  value: 0,
  valueOf: function() {
    this.value++;
    return this.value;
  }
};
console.log(a == 1 && a == 2);//true
```

### 7. 闭包是什么？
- 当在一个函数内定义另外一个函数就会产生闭包。
- MDN对闭包的定义是：闭包是指那些能够访问自由变量的函数，自由变量是指在函数中使用的，但既不是函数参数又不是函数的局部变量的变量。
- 闭包是指有权访问另外一个函数作用域中的变量的函数。
- 闭包就是函数的局部变量集合，只是这些局部变量在函数返回后会继续存在。闭包就是函数的“堆栈”在函数返回后并不释放，我们也可以理解为这些函数堆栈并不在栈上分配而是在堆上分配。

### 8. JavaScript 继承的几种实现方式？
1. **原型链继承**：将子构造函数的原型对象指向父构造函数的实例对象，那么子构造函数的实例对象可继承父类上的属性及方法。
- 核心：实例是父类的实例，也是子类的实例。
- 优点：简单，易于实现。
- 缺点：
  - 要想为子类新增属性和方法，不能放到构造器中
  - 无法实现多继承
  - 来自原型对象的所有属性被所有实例共享
  - 创建子类实例时，无法向父类构造函数传参
```
// 父类
function Father(name, age) {
    this.name = name;
    this.age = age;
    this.color = ['green', 'blue'];
}
// 父类原型上的方法
Father.prototype.getName = function() {
    return this.name;
}
// 子类
function Child(skill) {
    this.skill = skill;
}
// 继承实现：将子构造函数的原型对象指向父构造函数的实例对象
Child.prototype = new Father('Rose', 18);
var c1 = new Child('dance');
// 可以继承到原型上的属性以及方法
console.log(c1.getName());    //Rose
var c2 = new Child('sing');
c1.color.push('red');
// 来自原型对象的所有属性被所有实例共享，修改c1，c2也会改变
console.log(c2.color);    //[ 'green', 'blue', 'red' ]
```
2. **构造函数继承**：通过在子类中使用对父构造函数使用call方法来调用，并且修改this指针指向子类，同时可以传递参数。
- 核心：使用父类的构造函数来增强子类实例，等于是复制父类的实例属性给子类（没用到原型）
- 优点：
  - 解决了子类实例共享父类引用属性的问题
  - 创建子类实例时，可以向父类传递参数
  - 可以实现多继承（call多个父类对象）
- 缺点：
  - 实例并不是父类的实例，只是子类的实例
  - 只能继承父类的实例属性和方法，不能继承原型属性/方法
  - 无法实现函数复用，每个子类都有父类实例函数的副本，影响性能
```
// 父类
function Father(name, age) {
    this.name = name;
    this.age = age;
    this.color = ['green', 'blue'];
    this.getAge = function() {
        return this.age;
    }
}
// 父类原型上的方法
Father.prototype.getName = function() {
    return this.name;
}
// 子类
function Child(name, age, skill) {
    // 继承实现：在子类中使用对父构造函数使用call方法来调用
    Father.call(this, name, age);
    this.skill = skill;
}
var c1 = new Child('Rose', 18, 'dance');
// 可以继承到父类的属性以及方法
console.log(c1.getAge()); //18
// 不可以继承到父类原型上的属性以及方法
console.log(c1.getName());    //报错
var c2 = new Child('Mike', 18, 'sing');
c1.color.push('red');
// 来自原型对象的所有属性不会被所有实例共享
console.log(c2.color);    //[ 'green', 'blue' ]
```
3. **组合继承**：通过结合了原型链继承和构造函数继承。
- 核心：通过调用父类构造，继承父类的属性并保留传参的优点，然后通过将父类实例作为子类原型，实现函数复用
- 优点：
  - 可以继承实例属性/方法，也可以继承原型属性/方法
  - 既是子类的实例，也是父类的实例
  - 不存在引用属性共享问题
  - 可传参
  - 函数可复用
```
// 父类
function Father(name, age) {
    this.name = name;
    this.age = age;
    this.color = ['green', 'blue'];
    this.getAge = function() {
        return this.age;
    }
}
// 父类原型上的方法
Father.prototype.getName = function() {
    return this.name;
}
// 子类
function Child(name, age, skill) {
    Father.call(this, name, age);
    this.skill = skill;
}
Child.prototype = new Father();
Child.prototype.constructor = Child;
var c1 = new Child('Rose', 18, 'sing');
// 可以继承到父类的属性以及方法
console.log(c1.getAge()); //18
// 可以继承到父类原型上的属性以及方法
console.log(c1.getName()); //Rose
var c2 = new Child('Mike', 18, 'sing');
c1.color.push('red');
// 来自原型对象的所有属性不会被所有实例共享
console.log(c2.color); //[ 'green', 'blue' ]
```
4. **原型式继承**：将以参数形式传入的对象作为创建对象的原型。
- 核心：用一个函数包装一个对象，然后返回这个函数的调用，这个函数就变成了可以随意增添属性的实例或对象。object.create()就是这个原理
- 特点：类似于复制一个对象，用函数来包装。
- 缺点：
  - 所有实例都会继承原型上的属性。
  - 无法实现复用。（新实例属性都是后面添加的）
```
function createObj(o) {
    function F() {}; //在内部创建一个临时构造函数
    F.prototype = o; //将传入的对象作为这个构造函数的原型
    return new F(); //最后返回这个临时类型的新实例
}
// 父类
var person = {
    name: 'Rose',
    friend: ['a', 'b']
}
var p1 = createObj(person);
var p2 = createObj(person);
// 添加实例新属性
p1.name = 'Mike';
p1.age = 18;
console.log(p1.name);
console.log(p1.name);
p1.friend.push('c');
// 继承原型上的引用属性被所有实例共享。
console.log(p1.friend);
console.log(p2.friend);
```
5. 寄生式继承
寄生：在函数内返回对象然后调用
组合：1、函数的原型等于另一个实例。2、在函数中用apply或者call引入另一个构造函数，可传参
```
function Father(name, age) {
    this.name = name;
    this.age = age;
    this.friend = ['a', 'b'];
}
Father.prototype.getName = function() {
    return this.name;
}
function Child(name, age, skill) {
    Father.call(this, name, age);
    this.skill = skill;
}
(function() {
    var Super = function() {};
    Super.prototype = Father.prototype;
    Child.prototype = new Super();
})();
Child.prototype.constructor = Child;
var c1 = new Child('Rose', 20, 'dance');
// 可以继承到父类原型上的方法
console.log(c1.getName());    //Rose
var c2 = new Child('Mike', 18, 'sing');
c2.friend.push('c');
// 父类的引用属性不会被所有实例共享
console.log(c1.friend);      //[ 'a', 'b' ]
```

### 5. JS中类型转换有哪几种？
JS中，类型转换只有三种：
- 转换成数字
- 转换成布尔值
- 转换成字符串
转换具体规则如下:
![alt 属性文本](http://47.98.159.95/my_blog/015/type.jpg)
> 总结：转换为布尔值结果为**false**:
> +0、-0、NAN、""（空字符串）、undefined、null

### 6. 如何解决异步回调地狱？
- 回调地狱：回调当中嵌套着回调。<br>
缺点：代码可读性、可维护性都很差（由于嵌套层次太多）；每次任务完成时，需要在回调函数中对每个任务的失败情况进行处理。
- 解决方法：
  1. promise
  2. generator
  3. async/await

### 7. 事件流。
1. 事件流：事件流描述的是从页面中接收事件的顺序。<br>
2. DOM2级事件流包括下面几个阶段。
- 事件捕获阶段：从window对象传导到目标节点（上层传到底层）
- 处于目标阶段：在目标节点上触发
- 事件冒泡阶段：从目标节点传导回window对象（从底层传回上层）
3. DOM标准事件流的触发的先后顺序为：先捕获再冒泡，点击DOM节点时，事件传播顺序：事件捕获阶段，**从上往下传播**，然后到达事件目标节点，最后是冒泡阶段，**从下往上传播**。
> 在冒泡中，内部元素先被触发，然后再触发外部元素。
> 捕获中，外部元素先被触发，在触发内部元素。
![alt 属性文本](https://images0.cnblogs.com/blog/315302/201411/010945436598199.png)
![alt 属性文本](https://images0.cnblogs.com/blog/315302/201411/010945579257474.png)
> 事件冒泡走向：由子节点向父节点去触发**同名事件**。
> 事件捕获走向：由父节点向子节点去触发**同名事件**。

### 8. 事件监听的方法？
1. 【最常用】element.addEventListener(event, function, useCapture); => 用于向指定元素添加事件句柄，它可以更简单的控制事件
- 第一个参数是事件的类型(如 "click" 或 "mousedown").
- 第二个参数是事件触发后调用的函数。
- 第三个参数是个布尔值，表示监听函数是否在捕获阶段（capture）触发，默认为false（监听函数只在冒泡阶段被触发）。该参数可选。
> 该函数特点：
  1. 可以针对当前对象的同一个事件，添加多个不同的监听函数。【先添加先触发】
  2. 能够指定在哪个阶段（捕获阶段还是冒泡阶段）触发监听函数。
  3. 若为同一个事件多次添加多个相同的监听函数，则该监听函数只会执行一次，多余的监听函数自动被去除。（无需使用removeEventListener()）
2. HTML的 on- 属性<br>
`<body onload="doSomething()">`【监听函数不要忘记加括号】
缺点：只在冒泡阶段触发。
3. 元素节点的事件属性<br>
`window.onload = doSomething;` 【监听函数是函数名】
- 缺点：
  1. 只在冒泡阶段触发
  2. 同一个事件只能定义一个监听函数，也就是说，如果定义两次onclick属性，后一次定义会覆盖前一次。
> 这种方法与 HTML 的on-属性的差异是，它的值是函数名（doSomething），而不像后者，必须给出完整的监听代码（doSomething()）。

### 9. 事件模型常用属性及方法？
- 属性：
1. Event.currentTarget属性返回事件当前所在的节点，即正在执行的监听函数所绑定的那个节点。
2. Event.target属性返回原始触发事件的那个节点，即事件最初发生的节点。【在事件冒泡过程中这个值不变】
> - 事件传播过程中，不同节点的监听函数内部的Event.target与Event.currentTarget属性的值是不一样的，前者总是不变的，后者则是指向监听函数所在的那个节点对象。
> - 只有被点击时目标元素的target才会等于currentTarget
- 方法：
1. Event.preventDefault()：取消浏览器对当前事件的默认行为。
2. Event.stopPropagation()：阻止事件在 DOM 中继续传播，防止再触发定义在别的节点上的监听函数，但是**不包括在当前节点上其他的事件监听函数**。
3. Event.stopImmediatePropagation()：**阻止同一个事件的其他监听函数被调用，不管监听函数定义在当前节点还是其他节点**。也就是说，**该方法阻止事件的传播，比Event.stopPropagation()更彻底。**
  > 如果同一个节点对于同一个事件指定了多个监听函数，这些函数会根据添加的顺序依次调用。只要其中有一个监听函数调用了Event.stopImmediatePropagation方法，其他的监听函数就不会再执行了。

### 10. DOM节点同时绑定了两个事件监听函数，一个用于捕获，一个用于冒泡，这2个事件的执行顺序？
对于执行顺序的问题，如果DOM节点同时绑定了两个事件监听函数，一个用于捕获，一个用于冒泡，那么两个事件的执行顺序真的是先捕获在冒泡吗，答案是否定的，**绑定在被点击元素的事件是按照代码添加顺序执行的**，其他函数是先捕获再冒泡

### 11. 如何让事件先冒泡后捕获?
对于同一个事件，分别监听捕获和冒泡。<br>
给一个元素绑定2个addEventListener()，其中一个第3个参数为false（即冒泡）；另一个的第3个参数为true（即捕获）。然后调整代码顺序，将设为false的监听函数放在设置为true的监听函数前面。这样代码执行顺序就为先冒泡后捕获。

### 12. 事件代理/事件委托是什么？使用的好处是什么？
- 事件代理：利用了**事件冒泡**，只指定一个事件处理程序，就可以管理某一类型的事件。简而言之，就是说我们将事件添加到本来要添加的事件的父节点，**将事件委托给父节点来触发处理函数**
> 举例：页面上有这么一个节点树，div>ul>li>a;比如给最里面的a加一个click点击事件，那么这个事件就会一层一层的往外执行，执行顺序a>li>ul>div，有这样一个机制，那么我们给最外面的div加点击事件，那么里面的ul，li，a做点击事件的时候，都会冒泡到最外层的div上，所以都会触发，这就是事件代理，代理它们父级代为执行事件。
- 好处：
  1. 减少内存消耗，减少事件注册。
  2. 比较合适动态元素的绑定，**新添加的子元素也会有监听函数，也可以有事件触发机制**。
  3. 当用事件委托的时候，根本就不需要去遍历元素的子节点，只需要给父级元素添加事件就好了，其他的都是在js里面的执行，这样可以大大的减少dom操作，这才是事件委托的精髓所在。
- 经典应用：
1. 最经典的就是ul和li标签的事件监听，比如我们在添加事件时候，采用事件委托机制，不会在li标签上直接添加，而是在ul父元素上添加。<br>
Event对象提供了一个属性叫target，可以返回事件的目标节点，我们成为事件源，也就是说，**target就可以表示为当前的事件操作的dom**，但是不是真正操作dom，当然，这个是有兼容性的，标准浏览器用ev.target，IE浏览器用event.srcElement，此时只是获取了当前节点的位置，并不知道是什么节点名称，这里我们用nodeName来获取具体是什么标签名，这个返回的是一个大写的，我们需要转成小写再做比较（习惯问题）【通过判断e.target.nodeName来判断发生的具体元素】
```
window.onload = function(){
　　var oUl = document.getElementById("ul1");
　　oUl.onclick = function(ev){
　　　　var ev = ev || window.event;
　　　　var target = ev.target || ev.srcElement;
　　　　if(target.nodeName.toLowerCase() == 'li'){
　 　　　　　　 alert(123);
　　　　　　　  alert(target.innerHTML);
　　　　}
　　}
}
```
> 相关博文：[js中的事件委托或是事件代理详解
](https://www.cnblogs.com/liugang-vip/p/5616484.html)
2. 采用事件代理，为页面中的所有a标签绑定click事件。
```
document.addEventListener("click", function(e) {
	if (e.target.nodeName == "A")
		console.log("a");
}, false);
```
问题：若a标签里面仍有span、img等其他元素，上述代码中，单击span、img等其他元素不能触发click事件。<br>
原因：单击span、img等其他元素时，e.target指向的是触发click事件的元素（span、img等其他元素），而不是a标签。<br>
解决方法：从触发click事件的元素开始，逐级向上查找，直到找到a标签为止。<br>
```
document.addEventListener("click", function(e) {
	var node = e.target;
	while (node.parentNode.nodeName != "BODY" || node.nodeName == 'A') {
		if (node.nodeName == "A") {
			console.log("a");
			break;
		}
		node = node.parentNode;
	}
}, false);
```

### 13. 图片的懒加载和预加载？
- 预加载：在网页全部加载出来之前，提前加载图片，当用户需要查看时可直接从本地缓存中渲染，以提供给用户更好的体验，减少等待的时间。
- 懒加载：延迟加载图片 或者 符合某些条件时才加载某些图片。 懒加载的主要目的是作为服务器前端的优化，减少请求数或延迟请求数。<br>
两种技术的本质：两者的行为是相反的，一个是提前加载，一个是迟缓甚至不加载。<br>
**懒加载对服务器前端有一定的缓解压力作用，预加载则会增加服务器前端压力。**

### 14. mouseover和mouseenter的区别?
- mouseover：只要当鼠标移入**元素或其子元素**都会触发事件，所以有一个重复触发，冒泡的过程。对应的移除事件是mouseout
- mouseenter：只有当鼠标移入**元素本身（不包含元素的子元素）**会触发事件，也就是不会冒泡，对应的移除事件是mouseleave
> 鼠标移除的区别类同。
> 相关博文：[JS事件mouseover ,mouseout ,mouseenter,mouseleave的区别](https://www.jianshu.com/p/2f9c3c765459)

### 15. new操作符做了什么事情 / new操作符的原理？
1. 创建一个类的实例：创建一个空对象obj，然后把这个空对象的__proto__设置为构造函数的prototype。<br>
```
var obj = new Object();
obj._proto_ = Func.prototype;
```
2. 初始化实例：构造函数被传入参数并调用，关键字this被设定指向该实例obj。<br>
```
var result = Func.apply(obj,arguments);
```
3. 返回实例obj。<br>
`return result instanceof Object ? result : obj`

### 16. this指向 哪几种？
**this永远指向的是最后调用他的对象。**【看它执行的时候是谁调用的】
- 默认绑定：全局环境中，this默认绑定到window。
- 隐式绑定：一般地，被直接对象所包含的函数调用时，也称为方法调用，this隐式绑定到该直接对象。
- 隐式丢失：隐式丢失是指被隐式绑定的函数丢失绑定对象，从而默认绑定到window。- 显式绑定：通过call()、apply()、bind()方法把对象绑定到this上，叫做显式绑定。
- new绑定：如果函数或者方法调用之前带有关键字new，它就构成构造函数调用。对于this绑定来说，称为new绑定。
  1. 构造函数通常**不使用return关键字**，它们通常初始化新对象，当构造函数的函数体执行完毕时，它会显式返回。**在这种情况下，构造函数调用表达式的计算结果就是这个新对象的值**。
  2. 如果构造函数**使用return语句但没有指定返回值，或者返回一个原始值**，那么这时将忽略返回值，同时**使用这个新对象作为调用结果**。
  3. 如果构造函数**显式地使用return语句返回一个对象，那么调用表达式的值就是这个对象**。

### 17. 改变函数内部this指针的指向函数（bind，apply，call的区别）？
1. func.call(this, arg1, arg2...)<br>
call的第一个参数就是this所要指向的那个对象，后面的参数则是函数调用时所需的参数。
2. func.apply(this, [arg1, arg2, ...])<br>
第一个参数也是this所要指向的那个对象，如果设为null或undefined，则等同于指定全局对象。第二个参数则是一个数组，该数组的所有成员依次作为参数，传入原函数。
> call 、 apply 的区别：原函数的参数，在call方法中必须一个个添加，但是在apply方法中，必须以数组形式添加。
3. fnc.bind(this, arg1, arg2):用于将函数体内的this绑定到某个对象，然后返回一个新函数。这个函数不会立即执行，需要自己调用。
>bind和apply的区别:
  1. 返回不同：bind返回是函数
  2. 参数不同：apply(A, arguments)，bind(A, args1,args2)