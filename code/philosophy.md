哲学（may）
=======

这里列出的是一些哲学性质的思考，主要关注的是黑与白之间的灰色地带，即无所谓明显的对与错，尽量从多方面的角度提供参考，属于经验的积累。

从某种程度上说这里的东西是一种感觉或者直觉，请尽量理解，做到心中有剑。

# 1. “静态”优于“动态”

静态特性指的是在编译阶段生效的特性，而动态特性指的是在运行阶段生效的特性。

以Javascript的模块加载为例：

```js
// CommonJS规范
const lodash = require('lodash');

// ES6规范
import lodash from 'lodash';
```

上面的例子看似没什么区别，但CommonJS是动态的，ES6是静态的，因为CommonJS允许运行时才确定require的路径，甚至允许运行时才确定是否引用一个模块，比如下面这样：

```js
const dependencyName = 'lodash';

// 运行时才确定依赖的名字，这在CommonJS规范里这是合法的
const lodash = require(dependencyName);

// 运行时才确定是否依赖一个模块，这在CommonJS规范里这也是合法的
if (condition) {
  const lodash = require('lodash');
}
```

而ES6无法做到上面这些写法，因为上面的写法无法在编译时确定依赖关系。

> 注：dynamic import不是ES6规范

类似的例子还有很多，比如Javascript的OOP写法：原生Js的原型链和ES6引入的class，其中原型链是动态特性，class是静态特性。因为原型链写法中一个类的行为是在运行时才确定下来的，而class是无法在运行是改变行为的。

静态特性相比于动态特性有以下2个优势：

1. 行为稳定易于理解和优化。在编译阶段分析代码即可确定代码的逻辑行为，到了运行时仍然能保持一致。不论是对人、IDE还是编译器都十分友好。典型的例子是IDE的代码提示和补全。
2. 降低修复错误的成本。在编译时就可以发现各种错误，不用等到上线后才发现问题然后紧急修复。典型的例子是eslint的错误检测。

在多人合作的大型项目里，上面两个优势会被成倍地放大。

因此，当实现一种行为有静态和动态两种方式时，应当优先考虑静态写法。一个坏的例子是webpack的源码，充满了大量动态注入行为，光看代码几乎无法理解，其实那些功能用静态写法也可以做到，只是可能代码量会多一些罢了。

最后，静态特性自然也有不足的地方，比如无法动态修改逻辑，灵活性差。如果代码行为是由运行时的数据决定的，静态写法就必须提前穷举所有可能的情况以确保在运行时能正确执行，写起来很死板很啰嗦，万一数据很难提前穷举，此时静态写法就非常困难。在这种情况下，动态写法是更好的选择。

# 2. “显式”优于“隐式”

“显式”和“隐式”的区别主要在于代码本身的含义是否明显，代码的依赖是否明显。如果很明显，那就是显式的，否则是隐式的。

最简单的例子就是变量命名了：

```js
function iterator(arg1, arg2) {
  ...
}
```

单看函数名字完全不知道是做什么的，如果想知道他是做什么的以及怎么用则必须阅读相关代码，这就是一个隐式的例子，因为代码的效果不够明显。
如果改成下面这样就清楚多了：

```js
function calculateInterviewConflict(arg1, arg2) {
  ...
}
```

除了变量命名外，有的是代码本身的依赖不够明显，比如：

```js
function doSomething1() {
  ...
}

function doSomething2() {
  ...
}
```

此处业务逻辑的关系，隐含了一个限制条件，即这两个函数必须先调用`doSomething1`，再调用`doSomething2`，否则会得到错误的结果。
而这个约定是隐式的，因为单纯从代码上看是无法得知的，除非对代码逻辑本身有足够的了解。

改成显式的做法是：

```js
function doSomething1() {
  ...
}

// 现在至少从函数名字上是显式写法了
function doSomething2AfterSomething1() {
  ...
}
```

因为显式写法需要暴露更多信息，所以通常比隐式写法更加啰嗦。比如变量名更长或者代码行数更多，从这个角度来说会降低开发效率，对开发者来说体验不是很好。但当多人协作时，显式写法节省了大量沟通成本，实际上对开发者而言效率和体验其实更好。

另外有一种“约定大于配置”的开发策略，这种开发策略是显式还是隐式呢？其实要看这个约定是否被大家承认和尊重。世上本没有路，走的人多了自然就成了路。很多写法是隐式的，但如果约定落地得好，就可以不认为是隐式的。约定式开发是试图找到一种既易于理解又写法简洁的平衡。但这个比较依赖规范的执行情况，也是有一定风险和代价。

从某种程度上说，“显式”和“隐式”可以类比与“简单”与“简洁”。

# 3. “单一”优于“多样”

在现实中，“多样”总是优于“单一”。比如多样化饮食比单一结构饮食更有利于健康，比如多元的文化比单一文化更有利于文明进步，等等。
但是在代码世界中，“单一”比“多样”更合适，我们是在写代码不是在写诗。

代码风格要保持统一。对于没有明显好坏之分的写法风格分歧，应该毫不犹豫地选择遵从“传统”，少数服从多数。比如js是否加分号，真的没有什么区别，只是一种风格而已，但是如果大部分代码已经加了分号，那你就应该加分号。

除了代码风格之外，类型也应该保持一致。比如：

```js
// 函数返回类型不统一
function foo() {
  if (condition) {
    return 1;
  } else {
    return '2';
  }
}

// 变量类型不统一
let result = 1;
if (condition) {
  result = '2';
}
```

代码中唯一允许并且被鼓励的多样性是注释，请把你的个性表达在注释里吧。

```js
// 《UNESCO世界非物质文化遗产》
// 《全国重点文物保护单位》
// 《全国AAAAA级旅游景区》
//              —————— CEO赵欧伦同学在mage项目最后的几行代码
// 本文件需要施工请绕过下面几行

// 打卡围观群众
//    Xinkai Chen
//    Eden_cola
//    Lishunyang
//    Baishi Liu
//    Scorpiour
```