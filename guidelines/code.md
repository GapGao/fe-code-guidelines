代码规范
========

首先是本篇文档的规范，也就是代码规范的规范。本篇规范中，所有条目必须给出原因以及好的和不好的代码例子。

# 注释

## //之后请留一个空格

这样的代码更有节奏，可读性更好，不会显得拥挤杂乱

👎
```js
//这是注释
/*这是注释*/
```

👍
```js
// 这是注释
/* 这是注释 */
```

## 鼓励写注释，除个别场景外，注释风格不受限制，可自由发挥

注释有助于理解代码，因此鼓励写注释，既然如此，注释的风格便可自由发挥，写诗写对联都行。

👎
```js
const flag = false;
```

👍
```js
const flag = false; // 用来记录是否面试结果被更新过
```

某些地方的注释是有格式要求的，这些注释不能随意发挥，需要符合对应的格式，比如function的注释：

👎
```js
/**
 * 这是一个用来xxx的函数
 * arg1: blablabla
 * arg2: blablabla
 */
function foo(arg1, arg2) {
  ...
}
```

👍
```js
/**
 * 这是一个用来xxx的函数
 *
 * @param {string} arg1 - blablabla
 * @param {number} arg2 - blablabla
 * @return {Promise<object>} blablabla
 */
function foo(arg1, arg2) {
  ...
}
```

具体要符合什么格式，参见[jsdoc标准](https://devdocs.io/jsdoc/)

## 凡难以理解的地方必须增加注释

其实遇到难以理解的地方，最好的做法是换一种更简单容易理解的做法，但如果迫不得已只能这样写，那么就需要补充注释，以方便其他人维护。

👎
```js
if ((/class="text-layer"><\/div>/.test(lagouTestHtml)
  && lagouTestHtml.match(/class="text-layer"><\/div>/g).length > 1)
  || /�/g.test(html)
  || /octet-stream/.test(html)) {
  lagouShouldHideImage = false;
}
```

👍
```js
// 表示没有文字的时候或者出现乱码的时候就强制显示原来的图片
if ((/class="text-layer"><\/div>/.test(lagouTestHtml)
  && lagouTestHtml.match(/class="text-layer"><\/div>/g).length > 1)
  || /�/g.test(html)
  // 如果图片是这种格式的，显示图片，文字可能乱码
  || /octet-stream/.test(html)) {
  lagouShouldHideImage = false;
}
```

一些常见的难以理解的地方有：
1. 正则表达式，建议注释里补充一个符合pattern的例子
2. 复杂的if判断条件
3. 复杂的React组件的state或props
4. CSS里的各种谜之calc、z-index

这些地方都应该补充注释

## 凡“反常”的地方都必须增加注释

反常的写法，通常都是不得已而为之，比如为了解决一个bug所作的hack，或者是优化性能的一个特殊写法等等，这些信息通常只有代码的owner才知道，属于隐式的逻辑，如果没有任何注释，其他人维护就容易踩坑。

👎
```js
const isEmpty = value === 0 || !value;
```

👍
```js
// 0也是合法的输入值，所以要单独判断
const isEmpty = value === 0 || !value;
```

## 凡重要的注释，建议增加作者的名字

虽然我们可以通过git blame找到每一行代码的owner，但由于代码可能会因为挪动位置、调整缩进等原因转移“owner”，所以对于比较重要的注释，建议显式地注名owner，方便其他人维护。

👍
```js
// Lishunyang: 这里的代码暂时没有拆分，因为等XXX功能上线后，这些代码将会被废弃掉，到时候直接删除即可，目前为了兼容先留着
```

# Naming

命名应该按这样的优先级考虑：

简洁且明确的名字 > 啰嗦但明确的名字 > 简洁但不明确的名字 > 啰嗦且不明确的名字

## Javascript代码采用camelCase

没有什么原因，就是一个约定，为了保持统一

👎
```js
function create_interview() {
  ...
}
```

👍
```js
function createInterview() {
  ...
}
```

## CSS选择器采用kebab-case

没有什么原因，就是一个约定，为了保持统一

👎
```css
.candidate_header {
  ...
}

.candidateHeader {
  ...
}
```

👍
```css
.candidate-header {
  ...
}
```

## 文件名采用camelCase

虽然某些文件系统是不区分大小写的（例如mac默认的文件系统），但对react组件来说，文件名与组件名保持统一可读性更好，所以这里统一选择使用camelCase做文件名

👎
```
candidate_header.js
candidate-header.js
```

👍
```
candidateHeader.js
```

## url采用kebab-case

一个弱弱的原因是url是不区分大小写的，不适合用camelCase，这里约定统一使用kebab-case

👎
```
GET /api/candidate_info
GET /api/candidateInfo
```

👍
```
GET /api/candidate-info
```

## 数组以s或List结尾，体现出这是个数组

增加代码可读性

👎
```js
const item = [1, 2];
const exp = [1, 2];
```

👍
```js
const items = [1, 2];
const expList = [1, 2];
```

这个规则有个推论：不是数组最好不要以s或List结尾

👎
```js
const items = { 1: item1, 2: item2 };
```

但是对于常量，这个规则不适用，因为object的常量以s结尾是一个比较常见的操作

👍
```js
const CANDIDATE_TYPES = {
  1: 'CAMPUS',
  2: 'SOCIAL',
};
```

## Set推荐以Set结尾，体现出这是个Set

增加代码可读性

👎
```js
const numbers = new Set();
```

👍
```js
const numberSet = new Set();
```

## 映射关系的数据结构推荐使用 xxxToYYY 的方式命名

增加代码可读性

👎
```js
const candidateInfo = { 20: candidate1, 22: candidate2 };
```

👍
```js
const ageToCandidate = { 20: candidate1, 22: candidate2 };
```

## 除一些特殊情况外，禁止使用单一字母做变量名

单一字母的变量名是典型的开发一时爽，维护火葬场

👎
```js
const c = blababla;
```

👍
```js
const candidate = blablabla;
```

但是有2个例外

一是已经约定俗成的循环索引i，j，k，当然最好也少写

👍
```js
for (let i = 0; i < 100; i++) { // 这里的i由于已经约定俗成了，不影响代码可读性
  ...
}
```

二是做数据转换时的“一句话”代码，前提是只有一句话

👍
```js
const names = candidates.map((x) => x.name); // 这里的数据转换只有一行代码，命名为x不影响代码可读性
```

如果代码行数超过一行，就不要这样了

👎
```js
// 这里的数据转换不止一行代码，命名为x影响了代码可读性
const names = candidates.map((x) => {
  if (x.age < 18) {
    return x.name + '（未成年）';
  } else {
    x.name;
  }
});
```

## 禁止使用与系统API重名的名字

否则容易让人以为是系统api，令人迷惑，而且可能导致诡异的错误

👎
```js
// encrypt与node api重名
function encrypt() {
  ...
}

// request与fetch api重名
function request() {
  ...
}
```

👍
```js
// 看名字就知道是自己封装的encrypt
function encrypUserInfo() {
  ...
}

// 看名字就知道是自己封装的request
function mageRequest() {
  ...
}
```

## 不要使用过于common的名字

过于common的名字含义不明确，应该避免

👎
```js
// handler是干什么的？看名字完全无法知道
function handler() {
  ...
}
```

👍
```js
// 看名字就知道是处理click事件了
function onClick() {
  ...
}
```

## 名字要与实际用途匹配

不然就会令人迷惑，应该避免

👎
```js
function getMessage(args) {
  args.message = '123'; // 名字是getMessage但是没有return
}
```

👍
```js
function extendWithMessage(args) {
  args.message = '123';
}
```

## 函数的名称必须符合“操作+对象”的模式

操作+对象，含义明确易于理解。

👎
```js
// 只用动词没有操作对象，意义不明确
function update() {
  ...
}

// 只有操作对象没有动词，意义不明确
function interview() {
  ...
}

// 有动词和操作对象了，但是顺序反了，看着别扭
function interviewUpdate() {
  ...
}
```

👍
```js
function updateIntervew() {
  ...
}
```

这个规范还有个推论：非函数不要使用这种模式的名字，以免令人迷惑。

👎
```js
const updateInterview = 3; // 看着是个函数，实际是个变量
```

## DOM引用的命名以$做前缀

DOM的API不太一样，加上$更加明显，有利于理解代码逻辑

👎
```js
const container = document.getElementByClassName('name');
```

👍
```js
const $container = document.getElementByClassName('name');
```

# CSS

## 必须使用CSS Modules

除某些特殊情况外，应该全局使用CSS Modules，好处多多，比如避免间接影响他人代码，加速hmr，方便class命名等。（当然CSS Modules的缺点是不利于代码复用）。

👎
```css
.name { // 没有CSS Modules
  ...
}
```

👍
```css
.name { / 有CSS Modules
  ...
}
```

所谓的特殊情况就是用CSS Modules无法做到或者极难做到而非CSS Modules代码很适合的场景（比如一些全局默认样式设定）

## 禁止使用!imporatnt

!important是毒品，如果你一不小心用了它就会上瘾，但会贻害无穷。因为带有!important的样式完全无法被他人覆盖，比如某一天要对带有!important的样式做调整，不去掉!important吧无法调整，去掉!imporant吧会影响其他哪些样式不知道，可能甚至不如把样式删掉重新写一遍快。凡是吃过!important亏的人都深有体会！

👎
```css
.name {
  color: red; !important
}
```

如果你想提升selector的优先级，可以增加额外的class

👍
```css
.name.prior-fix { // 增加了一个叫prior-fix的class，让优先级更高了
  color: red
}
```

## 不要使用类似stylus中的placeholder语法

placeholder语法写起来爽，但是维护起来很麻烦，因为class名称都是动态拼接的，维护起来非常麻烦，比如可能无法通过搜索定位

👎
```stylus
.candidate
  &__header
    &__info
      ...
    &__button
      ...
  &__footer
    ...
```

👍
```stylus
.candidate__header
  ...

.candidate__header__info
  ...

.candidate__header__button
  ...

.candidate__footer
  ...
```

## 在启用CSS Modules的情况下，class名字应该精简，不要使用BEM命名方式

因为CSS Modules保证了class名字不会冲突，也就没有必要用BEM命名法，没啥意义反倒写起来啰嗦，徒增class长度。

👎
```css
.candidate__header__info {
  ...
}

.candidate__header__button {
  ...
}
```

👍
```css
.info {
  ...
}

.button {
  ...
}
```

## 在启用CSS Modules的情况下，没有特殊原因不要嵌套选择器

嵌套选择器的目的通常是为了避免class名字冲突，但是已经启用CSS Modules了，这个问题就不存在了，此时嵌套选择器不但没有意义，反而增加了选择器的优先级，让维护和扩展变得更加麻烦。

👎
```stylus
.candidate {
  .info {
    ...
  }
  .button {
    ...
  }
}
```

👍
```stylus
.info {
  ...
}

.button {
  ...
}
```

## 禁止使用tag做选择器

tag不是一个很稳定的选择器，非常容易受dom结构的变化干扰（比如其他人不知道，多加了一层div可能就让选择器失效了），维护成本很高，多写一个class又不会累死。

👎
```css
.candidate span {
}
```

👍
```css
.candidate-name { // 换成一个class选择器，可读性又好，还不会受dom结构变化影响
  ...
}
```

## 禁止使用float做定位

float这个属性原本被发明出来的作用就不是用来定位的，这个属性有很多副作用（因此出现了各种五花八门的清除浮动的方法），过去人们使用是因为要照顾脑残的IE浏览器，现在已经基本不需要考虑这些问题了，应该使用专门用来定位的CSS属性（例如flex、position）。你知道float会带来什么副作用吗？还有你知道200种清除浮动的写法吗？如果你不会，那就不要用float做定位！

👎
```css
.name {
  float: right;
}
```

👍
```css
.name {
  position: absolute;
  right: 0;
}
```

## CSS属性的顺序应该遵从一定的规则

这个规范是为了让CSS文件更加整齐，增加代码的可读性。

属性的顺序应该遵从破坏性属性的在前面，非破坏性的属性在后面。进一步细化就是：

1. 盒模型。比如display、flex等
2. 定位。比如position及相关属性，例如left、top等
3. 元素尺寸。比如width、height、padding、margin等
4. 文字样式。比如font-size、line-height、color等
5. 其他样式。比如border、background等

👎
```css
.name {
  color: red;
  font-size: 22px;
  width: 100px;
  height: 200px;
  border: 1px solid blue;
  display: inline-block;
  position: fixed;
}
```

👍
```css
.name {
  position: fixed;
  display: inline-block;
  width: 100px;
  height: 200px;
  font-size: 22px;
  color: red;
  border: 1px solid blue;
}
```

# React

## 组件名首字母大写，即使functional component也是一样

保持统一，且容易理解

👎
```js
class candidateHeader extends React.Component {
  ...
}

function candidateHeader(props) {
  ...
}
```

👍
```js
class CandidateHeader extends React.Component {
  ...
}

function CandidateHeader(props) {
  ...
}
```

## 组件必须要写PropType定义

便于理解代码，否则其他人如何使用组件是不确定的

👎
```js
class MyComponent extends React.Component {
  ...
};
```

👍
```js
class MyComponent extends React.Component {
  ...
};

MyComponent.propTypes = {
  a: PropTypes.number,
  b: PropTypes.string,
};
```

## 组件的jsx超过200行，或者组件本身超过500行，应该考虑拆分

过大的jsx和组件很难维护，阅读和理解代码很困难。

👎
```
├──MyComponent.js # 1000行
```

拆分的方式，建议以原来的组件名称简历目录，然后在这个目录中拆分成多个组件，在index.js中导出组件。这样拆分的好处是组件的引用方式不变，就像下面这样：

👍
```
├── MyComponent
│   ├── Header.js # 300行
│   ├── Footer.js # 300行
│   ├── Body.js # 400行
│   └── index.js # 10行
```

## 一个文件只export一个组件

这是一个会带来潜在好处的规则。如果不做限制，一个文件可以导出多个组件，那大家就容易把一些“相关”的组件都放在一个文件里，导致这个文件过大。如果遵守这个规范，那么就必须拆分成多个文件，从一定程度上可以避免文件过长的情况。

如果你觉得拆分成多个组件看着乱，那么可以新建一个目录然后将拆分后的一系列组件放在这个目录下，形成一个小的局部。

👎
```js
export class MyComponent1 {
  ...
}

export class MyComponent2 {
  ...
}
```

👍
```js
// 一个文件只导出一个组件
export class MyComponent1 {
  ...
}
```


## 禁止在render中出现副作用

render应该是纯函数，因为它会被频繁调用，这是React这个框架的运行特性决定的。如果在render函数中引入副作用，~~你的亲人将会远离你，朋友将会唾弃你~~各种诡异的问题将接踵而来。

👎
```js
class candidateHeader extends React.Component {
  render() {
    this.setState({ msg: 'hello' });
  }
}
```

可以考虑将副作用的代码放在生命周期函数里（比如componentDidUpdate，componentDidMount）。

## 避免使用`componentWillMount`，`componentWillReceiveProps`，`componentWillUpdate`

为什么以及如何替代见React的[官方blog](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)

幸运地是React16里已经将这几个API追加了`UNSAFE_`的前缀，不要头铁去用他们。

## 避免props透传

透传props的意思是某个组件没有用到props而是将prop原封不动地又传递给了children，这种写法增加了组件之间的无意义耦合，不论是开发还是维护成本都很高。组件透传props就好像一个对外声称100平的房子实际有50平是公摊面积。

👎
```js
class ParentComponent extends React.Component {
  render() {
    const { a, b, c, d } = this.props; // a, b, c, d在当前组件中没有用到，原封不动传给了ChildComponent
    return <ChildComponent a={a} b={b} c={c} d={d}>
  }
}
```

通常产生这种写法是因为组件间共享了状态，并且共享的状态距离组件有些远，就像下面这样：

```
状态state存在component1，但是component4和component5才是真正用到的地方

          component1(state)
             /      \
component2(prop)  component3(prop)
       |                |
component4(prop)  component5(prop)
```

其实这种情况还是蛮常见的，也没什么好办法，但如果component1的state来自react-redux，更好的做法是用直接从store里取，不要吝惜connect。

## 禁止用spread运算符传递props

spread运算符无法显式地看到究竟传了什么props，维护成本很高。

👎
```js
// 这里我们无法明确得知究竟给MyComponent传了什么props，必须得看data的定义
<MyComponent {...data}>
```

👍
```js
// 这样虽然多写了点代码，但是明确多了
<MyComponent a={data.a} b={data.b} c={data.c}>
```

## 禁止无意义的jsx嵌套

没有意义，反而增加了代码缩进。

👎
```js
// 假设这里的div嵌套是无意义的
<div>
  <MyComponent>
</div>
```

👍
```js
// 应该去掉无意义的div
<MyComponent>
```

## Redux action 遵从FSA标准

[FSA(Flux Standard Action)标准](https://github.com/acdlite/flux-standard-action)约定的action应该是如下形式：

👍
```js
{
  type: UPDATE_CANDIDATES, // action类型
  payload: { candidates }, // 数据部分，以object形式传入
  meta: { transition }, // 配置部分，以object形式传入
}
```

## 局部的，不需要全局共享的状态不要放在redux里

redux管理全局状态的地方，相当于是一个公共的储物空间，如果不加以限制，任何东西都塞在公共空间里，那每个人想在里面找自己的东西就很困难，有时候可能还会错拿其他人的东西。而且，由于redux是全局的，内存回收比较困难，容易引发性能问题。因此，对于那些局部的（或者只在相邻几个组件里共享的）状态，没必要也不应该放在redux里。

很多时候loading状态就是一个典型的局部状态，类似的还有表单的校验错误，请求的状态等。

# 模块系统

## 除非特殊原因，总是使用import而不是require

ES6的模块系统是静态的模块系统，可以在编译阶段被解析静态分析，有利于及早发现错误，而且对IDE很友好，而CommonJS规范的模块系统是动态加载，只有在运行时才确定最终的引用关系。

👎
```js
const a = require('./a.js');
```

👍
```js
import a from './a.js';
```

## named export/import 和 default export/import 必须正确匹配

按照ES6的标准，named import只能与named export匹配，比如：

👍
```js
// a.js
export { a };

// b.js
import { a } from './a.js';
```

default import只能与default export匹配，比如：

👍
```js
// a.js
export default a;

// b.js
import a from './a.js';
```

但由于babel的bug，二者混用也可以正常编译，比如：

👎
```js
// a.js
export default a;

// b.js
import { key } from './a.js';
```

因为上面的写法会被babel转义成大概是下面这种等价形式：

```js
const { key } = require('./a.js');
```

相当于是加载了一个模块，然后将其当成了object立即执行了解构操作，在代码存在环状依赖的情况下，可能遇到执行报错的情况（因为此时模块尚未被加载，解构报错了）。不过这个现象在前端通常不会遇到，因为前端模块的依赖层级通常比较浅，较少出现复杂依赖导致的环状引用的情况。后端node代码比较容易出现这个问题。

所以，`named import/export` 和 `default import/export` 不能混用。

## 先import npm依赖，其次import本地依赖

这只是一个规则，让代码更加整齐，便于理解。

👎
```js
import a from './a.js';
import lodash from 'lodash';
```

👍
```js
import lodash from 'lodash';
import a from './a.js';
```

# 变量

## 禁止magic number/string

不是所有的raw string/number都是magic number/string，只有可枚举的raw number/string才算是magic number/string。

magic number/string的问题是维护性太差，首先是代码不易理解，其次是如果写错了没有任何提示。正确的做法是定义成constant常量。

👎
```js
// 如果把success不小心拼错了，写成了succses，编译时不会有任何报错，执行的时候也不会报错
if (status === 'success') {
  ...
}

// 3代表什么含义？完全不清楚
if (code === 3) {
  ...
}
```

👍
```js
// 用IDE写代码时，敲了STATUS.能自动补全SUCCESS，想写错都难，而且如果敲错了，IDE会直接报错
if (status === STATUS.SUCCESS) {
  ...
}

// 可以看常量的出处得知是什么意思
if (code === CODE.ONSITE) {
  ...
}
```

## 变量必须赋初始值

有些情况下，没有初始值的变量可能导致意想不到的bug

👎
```js
let a;
```

👍
```js
let a = [];
```

## 变量的类型应该始终保持一致

一会是number类型，一会是string类型，写出来的代码非常令人迷惑，维护成本很高。

👎
```js
let status = null; // 这里status的初始值是null，是一个引用类型的空值

if (condition) {
  status = new Error(); // 类型变成了Error
} else {
  status = 'success'; // 类型是string
}
```

# 函数

# 分支