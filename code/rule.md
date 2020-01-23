规则（must）
===========

这里列出的是明确的代码规范，告诉你什么写法是错误的，什么写法是正确的。

这些规范除极少数特殊情况外都必须被严格遵守。

# [目录和文件]

## 使用UTF-8字符编码，换行符为\n

## 文件以\n结尾

## 一个文件的行数不得超过1千行（除constant）

## 使用ES6 Modules而不是CommonJS

ES6 Modules见[文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/import)

## import顺序必须按照第三方依赖、本地依赖、非js依赖的顺序排列

## 一个React组件一个文件

## React组件使用default export，其他情况使用named export

# [注释]

## 注释符合JSDoc规范，尤其是函数和类型的注释

JSDoc见[文档](https://jsdoc.app/)

## 注释后必须有一个空格

## disable eslint规则的地方都必须添加注释

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
const a = 1,
      b = 2;

## import时，首先是third-party dependency，然后是local file，最后是非js依赖

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
