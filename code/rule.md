规则（must）
===========

这里列出的是明确的代码规范，告诉你什么写法是错误的，什么写法是正确的。

这些规范除极少数特殊情况外都必须被严格遵守。

# [目录和文件]

## 使用UTF-8字符编码，换行符为\n

基本上是共识了，没什么好说的

## 文件以换行符（\n）结尾

文件以换行符结尾的好处是做文件内容拼接的时候容易区分不同的文件

```
// 文件1的内容：
abcd

// 文件2的内容：
efgh

// 如果文件以换行结尾，则两个文件拼接后的内容是：
abcd
efgh

// 如果文件不以换行结尾，则两个文件拼接后的内容是：
abcdefgh
```

这其实是一个非常细节的地方，不过为了统一这里还是规定一下

## 一个文件的行数不得超过1千行（除constant）

代码行数可以大致反映复杂程度，这个规则主要是避免写出大泥球式的巨型组件、模块，因为他们很难维护

## 使用ES6 Modules而不是CommonJS

ES6 Modules见[文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import)

ES6的模块系统是纯静态的模块系统，可以在编译阶段就完全确定模块依赖情况从而发现和排除很多问题，CommonJS是动态的模块系统，在编译阶段是无法完全确定代码的模块依赖情况的，比如CommonJS允许这样的写法：

```js
// require的包是动态决定的
const filePath = '../components/Dialog';
const Dialog = require(filePath);

// require行为是动态决定的
if (condition) {
  require('some-module')();
}
```

上面两种写法，只有在运行时才可以确定，ES6 Modules规范都是不允许的。

```js
// good
import a from 'some/module';

// bad
const a = require('some/module');
```

## import顺序必须按照第三方依赖、本地依赖、非js依赖的顺序排列，相同类型下常见的放前面不常见的放后面

这个规则主要是为了提升代码的美观程度，就好比写字，字写得好看和难看通常没有功能上的区别，但是有美学上的区别。

```js
// 先引入第三方依赖
import React from 'react';
import lodash from 'lodash';
// 然后引入本地依赖
import AComponent from './components/MyComponent';
import AnotherComponent from './components/AnotherComponent';
// 最后引入非js依赖
import styles from './styles.css';
```

## 一个React组件一个文件

这个规则的目的同样是为了避免写出大泥球式的代码，一个文件里的React组件越多，通常逻辑越复杂，越难以维护

有些人可能会觉得一个文件就放个几十行的组件会不会太浪费了，这个大可不必担心，新建文件不需要交钱也不会累skr人，不要在这上面节省。

## React组件使用default export，其他情况使用named export

因为React组件通常一个组件一个文件，所以用default export写起来比较符合直觉，其他情况下，比如util或constant应该用named export保证名字稳定

```js
// good
// React组件使用default export
export default MyComponent;
// constant和utils使用named export
export { xxxHelper, ACTIONS };

// bad
// React组件使用了named export
export { MyComponent };
// constant和utils使用了default export
export default { xxxHelper, ACTIONS };
```

# [注释]

## 注释符合JSDoc规范，尤其是函数和类型的注释

Javascript是动态类型语言，编辑器很难推导出代码中的类型信息，最简单的解决办法就是通过注释来补充说明。

[JSDoc](https://jsdoc.app/)本身是一个能根据代码注释自动生成文档的工具，其规定的注释风格能够被大部分主流IDE识别，因此我们的注释也应该符合JSDoc规范。

比如在VSCode中，没有当鼠标hover在一个没有注释的函数时，给出的函数信息非常少（如下图中红框区域）：

![](../assets/no_comment.png)

函数的第三个参数`role`是个什么东西呢？完全看不出来。

如果按照JSDoc格式补充了注释，就可以看到非常丰富的信息了（尤其是其中的类型信息）：

![](../assets/has_comment.png)

这下我们知道了，role是一个number，而不是string或object类型。

JSDoc规定了非常详细的注释语法，其实大部分语法都用不到，我们只要求在两个场景下的注释符合JSDoc规范。

1. 函数注释。尤其是有代码复用目的的函数，必须有注释。
2. 类型注释。尤其是接口请求数据和返回数据，必须有注释。

这是函数注释的例子：

```js
/**
 * 新建user
 * @param {string} name - 用户姓名
 * @param {string} email - 用户邮箱
 * @param {number} role - 角色
 * @return {Promise<boolean>} 是否创建成功
 */
async function createUser(name, email, role) {
  return true;
}
```

这是类型注释的例子：

```js
/**
 * 用户信息
 * @typedef {object} UserInfo
 * @property {string} name
 * @property {string} email
 * @property {number} role
 */

/**
 * @type {UserInfo}
 */
const user = { ... };
```

需要注意，Javascript的primitive type是小写单词，例如`boolean`,`number`,`string`,`symbol`,`null`。除此以外的类型是首字母大写单词，例如`Array`,`Promise`。具体的规则参考Google Closure Compiler文档中的[类型部分](https://github.com/google/closure-compiler/wiki/Types-in-the-Closure-Type-System#the-javascript-type-language)


## //必须接一个空格

这也是一个非常琐碎的细节，为了保持代码统一，这里明确一下。带有空格显得代码没有那么拥挤，相对稍微有利于阅读一些。

```js
// good
// 前面有一个空格

// bad
//前面没有空格
```

## disable eslint规则的地方都必须添加注释

如果disable eslint，那想必是有什么迫不得已的原因，如果没有说明为什么，未来其他人维护代码的时候就会非常困难，重构的时候都是坑。

```js
// good
// 这里禁用了camelCase的eslint检查，因为access_token之后会参与sql拼接，必须是snake_case
let access_token = ''; // eslint-disable-line camel-case

// bad
let access_token = ''; // eslint-disable-line camel-case

// even worse
let access_token = ''; // eslint-disable-line
```

# [命名]

## 常量名全大写，单词之间以`_`做分隔符

很基础的规则，没有太多可说的

```js
// good
const ROLE_HR = 'hr';
const USER_TYPE = 3;

// bad
const roleHr = 'hr'; // 没有用全大写
const USERTYPE = 3; // 没有_分隔，不好理解
```

## 函数名、变量名遵从小驼峰（lowerCamelCase）

小驼峰的意思是首字母小写的驼峰

很基础的规则，没有太多可说的

```js
// good
const userInfo = ...;
function updateUser() {
  ...
}

// bad
const user_info = ...;
function update_user() {
  ...
}
```

## 类名、组件名遵从大驼峰（UpperCamelCase）

大驼峰的意思是首字母大写的驼峰

很基础的规则，没有太多可说的

```js
// good
class UserSelector {
  ...
}

function UserSelector(props) {
  ...
}

// bad
class userSelector {
  ...
}

function userSelector {
  ...
}
```

## 路由名遵从蛇型（snake_case）

url是不区分大小写的，camelCase是依赖大小写存储信息的，因此用camelCase不太合适。

连字符`-`和下划线`_`二者其实没有太大差别，你看他们在键盘上甚至是共享一个按键的。

目前已知的一些区别有：

1. 文本分隔的效果。hyphen-example是无法双击选中整行的，lodash_example可以。

比如你可以尝试分别用鼠标双击下面的两个例子：

```
hyphen-example
lodash_example
```

一种说法是，`-`能让搜索引擎爬虫更好地分隔url，为页面打上标签，有利于SEO，不过我本人对这个说法持怀疑态度，搜索引擎应该不至于傻到这种程度。

2. 是否构成单词。比如`-`会参与某些单词的组成，比如front-end，但是`_`不会。因此如果用`-`的话，面对本身带`-`的单词可能会有麻烦。

比如shield.io就使用`-`做url编码的分隔符，格式为`{label}-{message}-{color}`，比如这样一个徽章：

![](https://img.shields.io/badge/fe--code--guideline-v0.0.1-brightgreen)

因为徽章里的文本已经有`-`了，shields.io的解决办法是用两个`-`表示一个出现在单词中的`-`，略有些不便。

> 其实这里如果换成`_`做分隔符也会遇到同样的问题，这里只是举一个例子

3. 输入体验。`-`比`_`在输入的时候会略微容易一些，不需要按住shift，恰好`_`的按键和shift按键隔得比较远，很难单手操作。这对某些追求极致手速的开发者来说会带来不爽。

综上，在没有太多差别的情况下，我们就选择了`_`作为一个统一的标准，对，只是一个约定的标准，不要再问了，just do it

## default export class的文件名与class名保持一致，其他文件名遵从小驼峰

React组件因为是一个组件一个文件，所以有必要让组件名与文件名保持一致，这样更有利于理解和检索代码，比如：

```sh
# good
.
├── UserSelector.jsx
└── LocationPanel.jsx

# bad
.
├── user-selector.jsx
└── location_panel.jsx
```

对于非组件类型的其他文件，文件名命名规范与变量、函数一样，采用小驼峰。这个规则主要是为了减少大家的选择负担，与代码的默认命名方式保持一致。

```sh
# good
.
├── utils.js
└── dateFormatter.js

# bad
.
├── Utils.js
└── date_formatter.js
```

## 路由目录与路由名保持一致，组件目录名与组件名保持一致（大驼峰），其他目录名遵从小驼峰

目录的命名规范请参考“代码目录结构规范”，这里不做过多解释，遵守就是了。

假设项目的路由是 /user_info和/interview_setting
```sh
# good
.
└── pages/ # pages下目录名与路由保持一致
    ├── user_info/
    │   └── ...
    └── interview_setting/
        └── ...

# bad
.
└── pages/ # pages下目录名与路由没有保持一致
    ├── userInfo/
    │   └── ...
    └── interviewSetting/
        └── ...
```

```sh
# good
.
└── InterviewTable/ # 组件目录名与组件名保持一致（遵从大驼峰）
    ├── TableHeader.js
    ├── TableBody.js
    └── index.js

# bad
.
└── interviewTable/ # 组件目录名没有与组件名保持一致（遵从大驼峰）
    ├── TableHeader.js
    ├── TableBody.js
    └── index.js
```

```sh
# good
.
└── xhrTool/ # 其他目录名遵从小驼峰
    └── ...

# bad
.
└── xhr_tool/ # 其他目录名没有遵从小驼峰
    └── ...
```

## 目录名称都用单数（除非特别强调复数含义的）

英文名词复数可能有特殊形式，甚至有些名词是不可数的，这比较考验开发者的英文水平，但我们是在写代码不是在写英语作文，所以在没有特别强调复数含义的时候，都统一用单数，轻松加愉快。

与编程语言类似，自然语言也有糟粕，比如名词单复数变化规则不统一就是一种。

```sh
# good
.
├── constant/ # 目录名是单数
│   └── ...
└── util/
    └── ...

# bad
.
├── constants/ # 目录名不是单数
│   └── ...
└── utils/
    └── ...
```

## 动词统一用现在时（除非特别强调其他时态含义的）

英文动词时态千变万化，而且不同时态的写法也不一样，同样地，我们是在开发，不是在写英文作文，所以统一使用一般时态即可。

动词时态不规则变化同样也是语言的糟粕。

```js
// good
const updateAt = Date.now();
const changed = Date.now();

// bad
const updatedAt = Date.now();
const hasChanged = Date.now();
```

## 采用驼峰命名规则时禁止全大写某个单词

在驼峰规则里大小写是作为单词的分割的标记。例如：

```js
const userId = 1; // user + id
const sendHttpStatus = 0; // send + http + status
```

因此我们可以利用这个规则从驼峰式转换到其他形式：

```
userId <=> user_id
sendHttpStatus <=> send_http_status
```

如果其中有单词大写了，就会打破这个规则，转换的结果变为：

```
sendHTTPStatus <=> send_h_t_t_p_status
```

因此：

```js
// good
const candidateId = 1;
const htmlContent = '';

// bad
const candidateID = 1;
const HTMLContent = '';
```

## DOM节点的引用以`$`开头

在使用React时，通常是不需要直接操作DOM元素的，如果代码非常依赖对DOM操作，大概率是代码写的不够“React”，其他人理解代码的成本就会上升（不能再按照React的生命周期思考和理解代码逻辑）。

所以，这里规定对DOM元素的引用的名字前面都加一个`$`，目的是使代码逻辑更加明显，提醒维护者这段代码逻辑涉及DOM操作。

```js
// good
const $name = document.getElementById('name');

// bad
const name = document.getElementById('name');
```

## 禁止用2表示“to”、4表示“for”

2和4如果用来表示"to"和"for"，唯一的意义是少写几个字母，可是真的要表示"2"和"4"的含义的时候怎么办呢？比如`http2Status`，是`httpToStatus`还是`http2Status`呢？

```js
// good
const httpToStatus = {};
function updateForHr() {
  ...
}

// bad
const http2Status = {};
function update4Hr() {
  ...
}
```

## 禁止使用与系统API相同或是相像的名字

与系统API重名或者相似的命名，极易迷惑其他人，百害而无一利。所以这种做法非傻即坏。

```js
// good
class MyRequest {
  ...
}

// bad
class Request { // Request是浏览器环境的Fetch接口
  ...
}
```

## 名字的字面含义必须与实际相符

例如：
```js
// 函数看着像是return什么东西但是没有return
function getUserInfo() {
  ... // 没有return任何东西
}

// 变量看着像是object其实是个array
const user = [
  ...
]
```

我们称这种行为是“说一套做一套”，请不要这样，不然亲人会远离你，朋友会厌恶你。

## 禁止使用一个字母作为名字的变量（有特例）

先说特例，特例是`i`、`j`、`k`作为循环变量。这是约定俗称的，大家已经达成了共识，这么写不会对大家理解代码有什么干扰，所以没问题。

```js
for (let i = 0; i < candidates.length; i++) {
  ...
}
```

其他情况禁止使用单一字母做变量的名字，因为代码可读性很差，尤其是当代码逻辑变多以后。

# [格式]

## 必须使用分号

没有什么特殊的理由，就是单纯的规则。

```js
// good
const user = new User();

// bad
const user = new User()
```

## 2个空格为一个缩进单位

没有什么特殊的理由，就是单纯的规则，所有括号括起来的部分，都应该产生缩进

```js
// good
function foo() {
  return 0;
}

// bad
function foo() {
    return 0;
}
```

这里需要注意几个地方：

1. swtich的case也要缩进（因为有些eslint规则要求switch的case没有缩进，这种写法不符合这里的规则）

```js
// good
switch (status) {
  case 0:
    ...
  case 1:
    ...
  default:
    ...
}

// bad
switch (status) {
case 0:
  ...
case 1:
  ...
default:
  ...
}
```

2. 一个语句如果要换行，需要添加缩进

```js
// good
request('/api/demo')
  .get();

const status = (a && b)
  || c;

// bad
request('/api/demo')
.get();

const status = (a && b)
|| c;
```

## 禁止垂直对齐

所谓的垂直对齐指的是这种风格：

```js
const a   = 1;
const aa  = 2;
const aaa = 3;
```

甚至也会出现在注释里：

```js
/**
 * @param {number}     a - blablabla
 * @param {boolean[]}  b - blablabla
 */
```

看着确实美观，可读性好，但是容易产生额外的维护成本（比如某一项变量导致整体都要调整），有时候会产生额外的缩进，让一行变得更长，行内比较稀疏，反而不利于阅读了。所以不要使用垂直对齐的写法。

```js
// good
const a = 1;
const aa = 2;
const aaa = 3;

// bad
const a   = 1;
const aa  = 2;
const aaa = 3;
```

## 括号默认遵从K&R风格（特殊情况除外）

没有什么特殊的理由，就是单纯的规则。

```js
// good
function foo() {
  ...
}

if (condition) {
  ...
} else {
  ...
}

// bad
function foo()
{
  ...
}

if (condition)
{
  ...
}
else
{
  ...
}
```

所谓的例外，通常是一些字面量的定义，因为此时如果仍然使用KR风格，可读性并不会很好，用MS风格也是可以的。

```js
// 如果全部使用传统的KR风格，这里的缩进让人感觉很奇怪
const users = [{
  id: 1,
  name: 'a',
}, { // <--
  id: 2,
  name: 'b',
}];

// 如果内部使用MS风格，这里的缩进看上去就自然多了
const users = [
  {
    id: 1,
    name: 'a',
  },
  {
    id: 2,
    name: 'b',
  },
];
```

## 控制语句（if、else、for、do、while）、运算符两侧都必须有括号

没有什么特殊的理由，就是单纯的规则。这样做的好处是避免代码过于拥挤。

```js
// good
if (status === 0) {
  ...
}

// bad
if(status===0){
  ...
}
```

## 括号里不需要空格

没有什么特殊的理由，就是单纯的规则。

```js
// good
const a = [1, 2, 3];
if (condition) {
  ...
}

// bad
const a = [ 1, 2, 3 ];
if ( condition ) {
  ...
}
```

## 箭头函数即使只有一个参数也要有括号

没有什么特殊的理由，就是单纯的规则。

```js
// good
const foo = (arg) => {
  console.log(arg);
}

// bad
const foo = arg => {
  console.log(arg);
}
```

## 用的时候再申明变量，而不是在一开始就申明

这样做可以让代码更紧凑，相关的代码在一起。

```js
// good
function foo() {
  ...
  const a = 1; // 下面要用到a了，此时申明变量
  if (a) {
    ...
  }
  ...
}

// bad
function foo() {
  const a = 1; // 函数一开头就申明了变量，到很后面才用到
  ...
  if (a) {
    ...
  }
  ...
}
```

## 申明变量时必须设置初始值

申明变量赋初始值就像是东西用完了放回原处一样，绝对是个好习惯。能够减少出bug的可能性。

```js
// good
const user = null;
const status = -1;

// bad
const user;
const status;
```

## 禁止一行出现多个语句或者一次申明多个变量

js里，一个语句可以占多行，同样地，多个语句也可以占一行（以分号分隔）
一行多个语句会降低代码可读性，添加注释也很困难，所以这种行为是被禁止的。变量申明也是如此。

```js
// good
const name = '';
const email = '';
const phone = '';

if (condition) {
  ...
} else {
  ...
}

// bad
const name = '', email = '', phone = '';

if (condition) { ... } else { ... }
```

## 一行代码不超过160个字符

一行的字符数太多会降低代码可读性，传统的80个字符的限制不是很合适，因为真的很容易超过，所以这里放宽到了160个字符。

例子就不用举了。

## 禁止连续空行

连续的空行会让代码变得稀疏，降低可读性。如果你具体的需要有多个空行区分不同的逻辑，那可以考虑重构成两个函数。

```js
// good
function foo() {
  ...

  ...
}

// bad
function foo() {
  ...


  ...
}
```

## 禁止行尾空格

行尾空格通常都是多余的，多余的东西没有存在的价值，留着还会显得乱。

建议配置IDE，在保存的时候自动清除行尾空格。

## 自定义callback的第一个参数是error

自定义回调函数的第一个参数是error，这是源自nodejs的写法约定。如果大家都遵从这个约定，有助于提升代码的可读性，而且可以做一些meta programming的。

```js
// good
function callback(err, arg1, arg2) { // 第一个参数位留给error
  if (err) { // 先处理error
    ...
  }

  ... // 然后是正常逻辑
}

// bad
function callback(arg1, arg2, err) { // err不是第一个参数
  ...
}
```

## 如果函数参数有callback，那么callback必须是最后一个参数

这也是源自nodejs的编程约定。

```js
// good
function sendRequest(value, type, callback) { // callback是最后一个参数
  ...
}

function sendRequest(callback, value, type) { // callback不是最后一个参数
  ...
}
```

# [语法、特性]

## 使用===，禁止使用==

## 使用const和let，禁止使用var

## 优先使用arrow function，尤其是class property

## 禁止使用eval和Function(...string)

## 禁止magic number/string

## 禁止显式使用undefined

## 禁止使用常量做循环条件

## 变量的初始值必须与类型匹配

## 禁止改变变量的类型

## 禁止一个函数返回不同类型的结果

## 禁止在函数参数中使用rest操作符

## 禁止动态调用对象方法

比如 a['test']

## 禁止向object动态注入function

## 禁止修改prototype

## 禁止抛出Error以外的东西

## 禁止擅自引入新的stage-x特性（必须与leader沟通讨论）

## 禁止擅自引入npm包（必须与leader沟通讨论）

# [React]

## 组件必须写propTypes

## 组件的propTypes必须按照样式、配置、数据、动作的顺序排列

## 禁止在render函数中产生副作用

## 禁止使用spread操作符给组件传参

## 禁止UGC业务使用dangerouslyInnerHTML

## 禁止index做key

## 禁止不必要的jsx嵌套

## redux action必须遵守FSA标准

[FSA(Flux Standard Action)标准](https://github.com/acdlite/flux-standard-action)

# [样式]

## 样式属性必须按照定位、盒模型、文字、其他的顺序排列

## 必须使用CSS Modules

## 禁止使用tag做选择器

## 禁止使用!important

## 禁止不必要的选择器嵌套

## 禁止使用stylus的&拼接选择器

## 禁止使用float做定位

# [网络]

## 自定义HTTP头部必须以X开头
