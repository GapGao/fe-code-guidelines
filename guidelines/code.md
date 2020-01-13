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
4. CSS里的各种谜之calc

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

虽然我们可以通过git blame找到每一行代码的owner，但由于代码可能会因为挪动位置、调整缩进等原因转移“owner”，所以对于比较重要的注释，建议显式地著名owner，方便其他人维护。

👍
```js
// Lishunyang: 这里的代码暂时没有拆分，因为等XXX功能上线后，这些代码将会被废弃掉，到时候直接删除即可，目前为了兼容先留着
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

# 模块系统

# 变量

# 函数

# 分支