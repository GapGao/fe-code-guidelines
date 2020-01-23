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

ES6的模块系统是纯静态的模块系统，可以在编译阶段就完全确定模块依赖情况从而发现和排除很多问题，CommonJS是动态的模块系统

```js
// good
import a from 'some/module';

// bad
const a = require('some/module');
```

## import顺序必须按照第三方依赖、本地依赖、非js依赖的顺序排列，相同类型下常见的放前面不常见的放后面

这个规则主要是为了提示代码的美观程度，就好比写字，字写得好看和难看通常没有功能上的区别，但是有美学上的区别。

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

## 常量名大写，以`_`做分隔符

## 函数名、变量名遵从小驼峰（lowerCamelCase）

## 类名、组件名遵从大驼峰（UpperCamelCase）

## 路由名遵从蛇型（snake_case）

## default export class的文件名与class名保持一致，其他文件名遵从小驼峰

## 路由相关目录与路由名保持一致，其他目录名遵从小驼峰

## 名词统一用单数（除非特别强调复数含义的）

## 动词统一用现在时（除非特别强调其他时态的）

## 禁止全大写缩写单词

## 表示私有变量或临时变量以`_`（一个下划线）开头

## DOM节点的引用以`$`开头

## 禁止用2表示“to”、4表示“for”

## 禁止使用与系统API相同或是相像的名字

## 名字的字面含义必须与实际相符

## 禁止使用一个字母作为名字的变量（有特例）

# [格式]

## 必须使用分号

## 2个空格为一个缩进单位

## switch的case会带来额外的一层缩进

## 括号遵从K&R风格（埃及风格）

## 控制语句（if、else、for、do、while）都必须有括号

## 箭头函数即使只有一个参数也要有括号

## 用的时候再申明变量，而不是在一开始就申明

## 申明变量时必须设置初始值

## 禁止一行申明多个变量

## 禁止一行出现多个语句

## 一行代码不超过120个字符

## 禁止连续空行

## 禁止行尾空格

## 禁止垂直对齐

例如：
```js
const a = 1,
      b = 2;
```

## callback的第一个参数是error

## callback是最后一个参数

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
