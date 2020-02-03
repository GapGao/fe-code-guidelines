建议（should）
=============

这里列出的是一些建议，仍然是告诉你什么写法是错误的，什么写法是正确，但与规则的不同在于所谓的“错误”和“正确”的判断的标准无法做到完全的客观，存在一定的主观性。

大部分情况下这些建议都是适用的，因此也应该尽量遵守。

# [注释]

## 鼓励注释，注释风格不限（除了JsDoc注释之外）

写代码的时候需要遵守条条框框，写注释的时候，可以适当放松一点儿，鼓励写注释，鼓励写有趣的注释，不限制风格（禁止不文明用语）

> 注：某些场景下必须需得按照JsDoc约定的规则写注释，比如函数的注释。

```js
/* 校招面试站点作为具有中国特色的社会主义功能，不搞什么国际化 */
```

## 难以理解的地方应当加注释

其实遇到难以理解的地方，最好的做法是换一种更简单容易理解的做法，但如果迫不得已只能这样写，那么就需要补充注释，以方便其他人维护。

```js
// good
// 表示没有文字的时候或者出现乱码的时候就强制显示原来的图片
if ((/class="text-layer"><\/div>/.test(lagouTestHtml)
  && lagouTestHtml.match(/class="text-layer"><\/div>/g).length > 1)
  || /�/g.test(html)
  // 如果图片是这种格式的，显示图片，文字可能乱码
  || /octet-stream/.test(html)) {
  lagouShouldHideImage = false;
}

// bad
if ((/class="text-layer"><\/div>/.test(lagouTestHtml)
  && lagouTestHtml.match(/class="text-layer"><\/div>/g).length > 1)
  || /�/g.test(html)
  || /octet-stream/.test(html)) {
  lagouShouldHideImage = false;
}
```

常见的难以理解的地方有：
1. 正则表达式，建议注释里补充一个符合pattern的例子
2. 复杂的if判断条件
3. 复杂的React组件的state或props
4. CSS里的各种谜之calc、z-index

这些地方都应该补充注释

## 反常的地方应当加注释

反常的写法，通常都是不得已而为之，比如为了解决一个bug所作的hack，或者是优化性能的一个特殊写法等等，这些信息通常只有代码的owner才知道，属于隐式的逻辑，如果没有任何注释，其他人维护就容易踩坑。

```js
// good
// 0也是合法的输入值，所以要单独判断
const isEmpty = value === 0 || !value;

// bad
const isEmpty = value === 0 || !value;
```

常见的反常的地方有：
1. 禁用了eslint规则
2. 违反了代码规范
3. 必须符合某种奇怪的写法

## 重要的注释应当增加作者的名字

虽然可以通过git blame找到每一行代码的owner，但由于代码可能会因为挪动位置、调整缩进等原因转移“owner”，所以对于比较重要的注释，建议显式地注名owner，方便其他人维护。

```js
// Lishunyang: 这里的代码暂时没有拆分，因为等XXX功能上线后，这些代码将会被废弃掉，到时候直接删除即可，目前为了兼容先留着
```

# [命名]

## 函数名字采用动词+对象或动词的形式

action + resource，含义更明确

```js
// good
function updateUser() {
  ...
}

// bad
function update() {
  ...
}
```

## 表示映射关系推荐使用xxxToYyy的形式

我们经常会遇到表示映射关系的数据结构，比如从userId映射到roleId之类。这里推荐的做法是使用`xxxToYyy`的命名格式，比如：

```js
// good
const userIdToRoleId = {...};
const colorToPrice = {...};
```

这样命名比较容易看出映射关系，可读性好。

## 不要使用过于common的名字

common的名字比如：

```
sleep
exec
read
exit
fetch
```

这些名字大都是一个单词，非常简单，而且没有太具体的含义。这种名字有两个缺点，一是容易跟系统API混淆，因为系统API通常就是这种风格的，二是看不出具体是做什么的，必须看相关的代码才知道。

# [代码逻辑]

## 不要依赖TODO

有些人习惯在代码里写`TODO`，比如：

```js
// TODO 这里需要重构
```

这样的TODO有不少问题：

1. 没有明确的owner。对于多人合作的大型项目，这种TODO该由谁去“do”呢？如果是谁写的谁来do，那么当某个开发者离开了项目，他所遗留下来的TODO怎么办呢？

2. 没有明确的方案。这种TODO通常都写得非常简单，比如只有寥寥几句话，除了留下TODO的人之外很少有其他人明白是要具体做什么。有时候甚至写TODO的人过了一两个月也记不清当时是什么意思了。

3. 没有明确的计划。如果一个朋友问你借2万块，你问他啥时候还，他说“以后还”，你觉得他什么时候会还钱？TODO就像是打了个无限期的欠条，什么还账无法预测。实际情况通常是TODO永远是TODO。

所以，除非你是一个说一不二，写了TODO就一定会do的人，否则，请不要写TODO，因为这些东西很大概率会变成垃圾残留在代码里。如果你真的忍不住想写TODO，请写在wiki或工程优化的backlog里。

## 不要过度设计

什么叫过度设计？过度设计就是喜欢做一些暂时用不着的功能，比如有个表格的展示效果需要实现一个table组件，结果幻想着各种复杂的应用场景最后造出来一个RcTable。

过度设计也有不少问题：

1. 增加开发成本。可能原本是20行代码可以解决的，现在写了2000行代码，变得难以维护。
2. 现在用不着的功能，将来可能还是用不着，即使将来会用到，现在的实现方式未必就是最合适的，很可能需要调整，甚至是无法满足的。结果就是，为了“将来”的功能实现的代码，大概率变成垃圾留在代码仓库里，而且还没人知道为什么要这样写，删又不敢删。

好的设计，确实需要预留一些扩展性，但除了比较基础和通用的代码外，业务逻辑开发的代码其实不需要太注意这一点，因为业务逻辑的代码是跟着业务逻辑的走的，业务逻辑要扩展，代码才需要扩展，业务逻辑不需要扩展，代码有扩展性也没用。而业务逻辑是否需要扩展，是应该在开发之前就讨论确定好的事情。

## 不要过早优化

这个道理大家都懂，就不啰嗦了。

# [语法、特性]

## 尽量让if都带上else

从控制留的角度考虑，如果所有的if都带有else，那分支覆盖率就是100%，不可能出现漏考虑的。情况。

很多时候我们为了省事不写else，但这很容易导致逻辑遗漏。不是一个好的习惯。

```js
// good
if (condition1) {
  ...
} else {
  ...
}

// bad
if (condition1) {
  ...
}
```

## 函数参数超过3个应该使用object传参

object+descruct方式给函数传参，可读性非常好，而且无需关注参数的顺序，调用的时候也更不容易出错。

```js
// good
function foo({ name, shouldSendEmail, shouldSendSms, message}) {
  ...
}

// bad
// 采用普通方式传参，调用的时候很不明确，比如中间的这两个boolean让人摸不着头脑
// 而且如果不小心把参数顺序搞错了，调用就会出错
foo('张三', false, true, 'hello');
```

## 少用this

this是js的糟粕，尽量少用。

## 避免if判断条件的表达式过于复杂

if是非常常见的语句，如果里面的条件太复杂，代码可读性比较差，应该在条件比较复杂的时候适当重构，将一些运算拿到外面去。

```js
// bad
if (a && ((b || c || d) && e) && f) {
  ...
}

// good
const g = (b || c || d) && e;
if (a && g && f) {
  ...
}
```

## 三元运算符表达式尽量简单，更推荐使用if/else

三元运算符写起来简单，但是当条件或者是表达式比较复杂的时候，三元运算符的可读性会急剧下降，尤其是当出现多个三元运算符同时出现的时候。此时推荐用if/else。

```js
// bad
const result = (a && ((b || c || d) && e) && f) ? g + i ? j : k : l;

// good
let result = null;
if (a && ((b || c || d) && e) && f) {
  result = g + i ? j : k;
} else {
  result = l;
}
```

## 慎用递归

递归逻辑不是很好理解，大部分时候尽量用循环吧，可别小看了循环，它不但比递归更容易理解，而且比递归的性能更好（没有调用栈空间开销）。

一个经验规则是：如果一个问题用循环就可以很直观地解决，那就一定不要用递归。

很多人喜欢用递归是觉得递归写起来代码简单，看着更高级，其实要想写出逻辑清晰且无bug的递归不是一件容易的事情。如果迫不得已要写递归的情景，这里有2个建议：

1. 先判断边界条件，再处理递归内容。递归边界决定了什么时候递归应该终止，这部分逻辑应当尽量与递归内容分开，这样做可读性更好而且不容易遗漏。
2. 递归调用的地方要明显且有规律，且越少越好。因为对于阅读代码的人来说，每到一个递归调用的地方，都需要花费额外的脑力记忆一些东西，所以递归调用越少，代码理解起来越容易，而且不容易产生遗漏。

举个例子：

```js
function qsort(array, start, end) {
  // 先判断递归边界，什么时候递归终止
  if (start >= end) {
    return;
  }

  // 然后是递归主体
  const pivot = array[start];
  let l = start;
  let r = end;
  while (l < r) {
    while (l < r && array[r] >= pivot) {
      r--;
    }
    array[l] = array[r];
    while (l < r && array[l] <= pivot) {
      l++;
    }
    array[r] = array[l];
  }
  array[l] = pivot;

  // 递归调用入口尽量有规律
  sort(array, start, l - 1);
  sort(array, l + 1, end);
}
```

## 尽量避免带副作用的函数

副作用的意思是，函数内部的逻辑影响到了函数外部的世界，本质上是由于共享了全局变量导致的。

有副作用的函数会形成调用顺序依赖，比如：

```js
... // I
foo();
... // II
```

如果foo是带有副作用的，我们就不能随意将I和II的代码调换位置，这对于代码重构来说简直是噩梦。一些诡异的bug也会因此产生。

所以尽量不要用带副作用的函数。

如果真的要用带有副作用的函数，请一定确保这个副作用的产生过程非常明显（比如增加注释，或者foo的名字一眼上去就知道是带副作用的）。

## 初始化结束后尽量不要改变object的结构特征

什么叫改变object的结构特征呢？比如：

```js
const a = {
  bar: () => {
    ...
  },
};

// 向a中注入或者移除函数field就算是改变了结构特征
a.foo = () => {
  ...
};
delete a.bar;
```

因为这种逻辑也会产生调用顺序依赖，其实本质上都是因为对数据进行了写操作，数据导致了依赖。

不过如果object本身是用来做mapping的，这个就无所谓了，比如：

```js
const userIdToEmail {
  1: '123@example.com',
};

delete userIdToEmail.1; // 这是没有问题的
```

## 增强代码的局部性

局部性的意思是，相同目的或用处的代码尽量放在一起，有利于理解代码，维护起来也更方便。

变量的申明和使用尽量放在一起就是一个例子。

## 适当添加空行分割代码，避免过于拥挤

代码缺少空行，就好像说话缺少标点，没有停顿和节奏。所以应该适当添加一些空行。

## 尽量代码块的嵌套

代码块嵌套会破坏代码的连贯性，显得参差不齐（缩进多了），增加记忆负担。

应该尽量减少嵌套，让代码更加连贯、平坦。

```js
// good
if (condition1 && condition2 && condition3) {
  ...
}

// bad
if (condition1) {
  if (condition2) {
    if (condition3) {
      ...
    }
  }
}
```

## 查询逻辑尽量多使用树形数据结构（例如Map、Set）以及insert、has等方法，少用线性数据结构（例如Array）以及includes、find等方法

Map、Set等数据结构的查询效率很高，应当多使用。过多使用array的includes、find等方法容易产生性能问题（当数据量大的时候，比如数组长度超过一千，还是比较容易出现的）。

而且Map、Set的方法写起来也更加简洁易懂。

```js
// good
const nameSet = new Set(['Duke', 'Jason', 'Rose']);
nameSet.has('Ross');

// bad
const names = ['Duke', 'Jason', 'Rose'];
names.includes('Ross');
```

其实Set用得更多一些，因为Map可以用js的object替代。

# [React]

## 尽量避免state和props之间的依赖

state依赖props是一种anti-pattern（不好的写法），因为如果props变化了，state就需要做更新，这个逻辑容易被忽略而导致一些奇怪的bug。

比如下面的代码，state的初始值依赖了props：

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      a: props.a,
    };
  }

  componentDidMount() { // 这两个生命周期必须要有，缺一不可
    this.setState({ a: this.props.a });
  }

  componentDidUpdate() { // 这两个生命周期必须要有，缺一不可
    this.setState({ a: this.props.a });
  }
}
```

想想为什么会写出state依赖props的逻辑？能否把state去掉然后将组件重构为stateless的？

如果迫不得已需要state依赖props，除了componentDidMount和componentDidUpdate两个生命周期函数必须修改之外，建议将props的名字改成更加明显的能够体现初始值含义的。比如：

```jsx
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    this.state = {
      a: props.initialA,
    };
  }

  componentDidMount() { // 这两个生命周期必须要有，缺一不可
    this.setState({ a: this.props.initialA });
  }

  componentDidUpdate() { // 这两个生命周期必须要有，缺一不可
    this.setState({ a: this.props.initialA });
  }
}
```

## 局部状态不应该放在redux里

可以认为redux就是React组件的全局状态，同理，不是全局共享的状态就不要写在redux里了。典型的有一些组件内部的loading状态，数据校验状态等。

无论什么东西都丢给redux的结果是让redux变得臃肿、复杂。而redux里的东西是动态注入到组件里的，没有静态分析的办法，到时候想清理redux会非常困难。

此外，每次redux更新都会触发所有connect到redux的组件的mapStateToProps或者是render，也会有不必要的性能损耗。

## mapStateToProps不能存在耗时的计算

这是redux的原理决定的，每次redux里有新的state更新，就会把所有connect组件的mapStateToProps或者render调用一次，如果mapStateToProps里恰好有非常耗时的计算，那么对性能的影响将非常显著。

如果真的有耗时的计算，可以考虑memoize缓存。

## 带生命周期的组件，componentDidUpdate里逻辑应该加上条件判断

因为componentDidUpdate会被触发多次，如果不加判断地就调用某些逻辑，可能会因为意外触发而产生错误结果。

```js
// good
componentDidUpdate(prevProps) {
  if (prevProps.data !== this.props.data) {
    doSomething();
  }
}

// bad
componentDidUpdate(prevProps) {
  doSomething();
}
```

## 筛选条件应该保存于url中

浏览器地址栏其实也可以看作是一个数据源，筛选条件保存在url里而不是state里的好处是当用户刷新页面的时候，选项都在，而且也便于用户收藏。

## 尽量避免大量props透传

props透传的意思是：

```jsx
// MyComponent本身用不到props.a和props.b，这两个会原封不动地传给children
function MyComponent(props) {
  return <div><InnerComponent a={props.a} b={props.b}/></div>;
}
```

透传的问题是让props无关的组件也与props产生了耦合，令人迷惑，看上去也很傻。

其实现实情况里，难免出现props透传的情况，尤其是一些公共组件库内部组合的场景。但如果一个组件有大量的props是透传的，那就需要好好斟酌一下了，是否这是有必要的，能否通过重构去除掉。

## 加载更多逻辑推荐使用cursor

这个其实与后端关系更大。

cursor的使用逻辑为：每次前端请求完一页数据后，后端除了返回这一页的数据外，还会返回一个cursor，当前端再次请求更多数据时，只需要将cursor回传给后端即可。

这样做的好处是下次加载更多的时候只需要传一个cursor，而这个cursor是后端在第一次返回的，相当于整个过程对前端透明。后端可以随意变更cursor的含义或编码，前端当不受影响。

如果不用cursor，通常的做法是前端需要将最后一项的id以及对应排序key的value传给后端，如果要增加其他额外信息（比如不是根据id而是根据name加载更多），前端逻辑就得做修改适配。而cursor方式下是不需要做调整的。

# [样式]

## 尽量降低选择器的优先级

选择器优先级太高的坏处是很难被覆盖，尤其是在做一些公共组件库的时候，自带样式优先级不能太高。

什么情况会增加选择器优先级呢？

```less
/* 情况1，class嵌套 */
.container {
  .title { // 对应的元素若被匹配到，选择器是两个class的优先级
    ...
  }
}

/* 情况2，class组合 */

.title.active { // 对应的元素若被匹配到，选择器是两个class的优先级
  ...
}
```

以上两个例子，如果用户想要覆盖样式，就必须写三个class的选择器才能在优先级上盖过默认样式，十分蛋疼。

好在上面的例子的解决办法也很简单：

```less
/* 情况1，使用CSS Modules */
.title {
  ...
}

/* 情况2，不要用class组合 */
.title-active {
  ...
}
```

## 在开启CSS Modules的状态下，就不要用BEM命名规则了

既然已经开启CSS Modules，就不必担心污染全局作用域了，此时class名字可以尽量简短，写着也方便，BEM规则就没有必要了。

```less
// good
.title {
  ...
}

.dialog {
  ...
}

// bad
.some-module__title {
  ...
}

.other-module__dialog {
  ...
}
```
