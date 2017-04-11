# Svelte简介

## 前言

前几天在刷github趋势榜，意外的发现了这个开源项目，通过阅读文档与相关资讯了解到Svelte的用法，好处以及缺陷，并对其思想深深的吸引，虽然框架存在诸多不足，生态圈也不算完善，但是我个人认为，这不失为一种web组件化方向的探索。在寻找资料的过程中，我发现Svelte几乎没有什么中文相关资料和中文文档，于是我决定先将其文档翻译成中文，这样能让更多的人了解到Svelte框架。大家也可以一起为它添砖加瓦。

[相关资料](https://github.com/tgxpuisb/sveltejs-documentation-zh-CN/blob/master/other-info.md)也会长期更新。

以下是翻译的内容。如果你有什么好的想法，或者翻译中存在什么错误，欢迎提[iusse](https://github.com/tgxpuisb/sveltejs-documentation-zh-CN/issues)指正，非常感谢。


## 什么是Svelte
如果您曾经构建过JavaScript应用程序，你或多或少使用过React，Angular，Vue和Ractive。与Svelte很像，这些工具都有一个目标，就是可以轻松的构建交互式用户界面。

但Svelte有一个关键的区别：你的应用程序会在打包构建时（build）被转化成原生的可执行的javascript代码，且不需要一个运行时（runtime）来解释你的应用程序代码。这意味着你无需负担由于框架抽象或者在你的应用首次加载时产生的性能损耗。

并且，由于没有额外的使用成本，你可以轻松地在现有的程序中逐渐采用Svelte，或者将小部件作为独立的组件使用到任何的地方。

[阅读介绍性博客文章](https://svelte.technology/blog/frameworks-without-the-framework)，了解更多关于Svelte的目标和哲学。

[中文介绍](https://github.com/tgxpuisb/sveltejs-documentation-zh-CN/blob/master/author-blog.md)

## 认识与理解svelte组件
在Svelte中，应用程序由一个或多个组件组成。组件是封装了标签的，样式，与行为的可复用的自包含代的码块。(很像.vue文件)。

像Ractive和Vue一样，Svelte也崇尚单文件组件的概念：组件只是一个`.html`文件。下面是一个简单的例子：

```html
<!-- App.html -->
<h1>Hello {{name}}!</h1>
```

Svelte会把它转换为可以一个可直接被引入到你的应用程序中的JavaScript模块：

```
// main.js
import App from './App.html';

const app = new App({
  target: document.querySelector( 'main' ),
  data: { name: 'world' }
});

// 更改与模板相关的数据
app.set({ name: 'everybody' });

// 卸载组件并做一些清理
app.destroy();
```

恭喜你，你刚刚已经了解了一半的SvelteAPI！

## 入门
通常来说，这里会说你需要使用`<script>`标签把框架引入到你的页面。但是由于Svelte在构建时就已经打包成了可运行的代码，所以它的工作方式会有所不同。

Svelte的最好的使用方式是将其集成到你的构建系统中，这里有针对`Rollup`，`Browserify`，`Gulp`等的打包工具，[更多资料](https://github.com/sveltejs/svelte/#svelte)，请参阅这里获取最新的列表。

现在，为了演示的目的，我们将使用svelte-cli命令行工具。

> 你需要安装[node.js](https://nodejs.org/en/)并熟悉一些简单的命令行操作

首先，安装`svelte-cli`

```
npm install -g svelte-cli
```

然后为项目创建一个文件夹

```
mkdir my-svelte-project
cd my-svelte-project
```

进入`my-svelte-project`文件夹，创建一个`HelloWorld.html`的文件，并且输入如下内容：

```html
<h1>Hello {{name}}</h1>
```

编译它

```
svelte compile --format iife HelloWorld.html > HelloWorld.js
```

`--format iife`是指生成一个立即调用的函数表达式，这样做可以允许我们使用一个`<script>`标签来使用这个组件（默认情况下，Svelte被编译成一个JavaScript模块，建议在更高级的应用中使用，但需同时也需要额外的步骤）。

创建一个`index.html`页面并且引入刚才生成的代码

```html
<!doctype html>
<html>
<head>
  <title>My first Svelte app</title>
</head>
<body>
  <main></main>
  <script src='HelloWorld.js'></script>
  <script>
    var app = new HelloWorld({
      target: document.querySelector( 'main' ),
      data: {
        name: 'world'
      }
    });
  </script>
</body>
</html>
```

最后，在浏览器里面打开该页面，并在控制台中输入`app`可以查看更多API。

## 组件API

正如我们上面看到的，你通过`new`关键字创建了一个组件实例：

```js
import MyComponent from './MyComponent.html';

const component = new MyComponent({
  // `target` 是唯一的必填字段 – 类似vue中的el配置
  // 用来描述组件将渲染在哪里
  target: document.querySelector( 'main' ),

  // `data` 是一个选填字段。一个组件也能有
  // 默认 data – 我们稍后会接触到
  data: {
    questions: [
      'life',
      'the universe',
      'everything'
    ],
    answer: 42
  }
});
```

除了你添加的[自定义方法](https://svelte.technology/guide#custom-methods)之外，每一个组件实例都提供少量的方法，你能控制这些方法操作组件。

### component.set(data)

该方法会根据传入的新`data`值更新组件状态并引起DOM的改变。`data`必须是一个纯粹的`javascript`对象。任何没有包含在`data`之内的属性将会保持和原来一样。

```js
component.set({
  questions: [
    'why is the sky blue?',
    'how do planes fly?',
    'where do babies come from?'
  ],
  answer: 'ask your mother'
});
```

> 如果你之前使用过`Ractive`，这里会与`ractive.set(...)`非常类似，但是你必须始终使用`ractive.set(...)`而不能使用`ractive.set('foo', 'bar')`这种方式，并且你不是直接给对象设置关键字（比如，不能使用ES6中用{'class':1}这种）。它也非常类似于`React`的`setState`，除了svelte会导致同步更新，这意味着DOM总是处于可预测状态。

### component.get(key)

返回当前`key`的值

```
console.log( component.get( 'answer' ) ); // 'ask your mother'
```

这也会取出[计算属性](https://svelte.technology/guide#computed-properties)的值


### component.observe(key, callback[, options])

此方法允许你响应状态更改，结合生命周期钩子和双向绑定时这将会特别有用

```
const observer = component.observe( 'answer', answer => {
  console.log( `the answer is ${answer}` );
});
// 会立即出发修改过后的answer
// -> 'the answer is ask your mother'

component.set({ answer: 'google it' });
// -> 'the answer is google it'

observer.cancel(); // further changes will be ignored
```

回调函数有两个参数，当前值和上一个值。（如果是第一次调用第二个参数会是`undefined`）:

```js
thermometer.observe( 'temperature', ( newValue, oldValue ) => {
  if ( oldValue === undefined ) return;
  console.log( `it's getting ${newValue > oldValue ? 'warmer' : 'colder'}` );
});
```

如果你不希望在首次添加`observer`时触发回调，你可以设置`init:false`：

```
thermometer.observe( 'temperature', ( newValue, oldValue ) => {
  console.log( `it's getting ${newValue > oldValue ? 'warmer' : 'colder'}` );
}, { init: false });
```

> 对于字符串和数值类型的值，只有当值更改的时候才会触发`observer`的回调。但是由于值可能是一个对象或者数组，而传入的值如果引用与之前是相同的时候Svelte会谨慎处理（因为引用相同没则需要判断内部是否每个值都相同）也就是说，如果你调用`component.set({foo: component.get('foo')})`，而`foo`是一个对象或者数组的时候，任何一个`foo`的`observer`都将会触发。

默认情况下，`observer`会在DOM更新之前被调用，让你有机会执行任何其他更新，而不需要修改DOM。 在某些情况下，你需要在DOM变更之后再触发回调，例如，如果在DOM更新后需要计算元素可以使用`defer：true`：

```js
function redraw () {
  canvas.width = drawingApp.get( 'width' );
  canvas.height = drawingApp.get( 'height' );
  updateCanvas();
}

drawingApp.observe( 'width', redraw, { defer: true });
drawingApp.observe( 'height', redraw, { defer: true });
```

如果需要监听嵌套的组件，可以使用`refs`:
```html
<Widget ref:widget/>
<script>
  export default {
    oncreate () {
      this.refs.widget.observe( 'xxx', () => {...});
    }
  };
</script>
```

### component.on(eventName, callback)

允许你响应事件

```js
const listener = component.on( 'thingHappened', event => {
  console.log( `A thing happened: ${event.thing}` );
});

// some time later...
listener.cancel();
```

### component.fire(eventName, event)

与`component.on(...)`有关：
```js
component.fire( 'thingHappened', {
  thing: 'this event was fired'
});
```

乍一看，`component.on(...)`和`component.fire(...)`不是特别有用，但是当我们了解嵌套组件时，它会变得非常好用。（因为没有类似Vuex的东西组件间通信就靠它了）

> `component.on(...)`与`component.observe(...)`看起来很相似，但是它们有不同的用途，`Observers`被用来响应应用的数据流的变化和任何时候持续的变化，然而事件在处理离散的时刻会更好用，比如用户做了一个选择，选择引发了很多变动。

### component.destroy()
把组件从DOM中移除，同时也会移除所有创建的`observers`和事件监听，这也会触发一个`destory`事件。

```js
component.on( 'destroy', () => {
  alert( 'goodbye!' ); // please don't do this
});

component.destroy();
```

## 模板语法

`Svelte`不重复造轮子，学习`Svelte`模板只需要在`HTML`，`CSS`和`JavaScript`基础上少量的学习一些新知识即可。

### 标签
标签允许你把数据绑定到模板上，无论何时修改数据（比如，在component.set（...）之后），DOM将自动更新。你可以在模板中使用任何JavaScript表达式，并且也会自动地更新：

```html
<p>{{a}} + {{b}} = {{a + b}}</p>
```

你还可以使用标签属性

```html
<h1 style='color: {{color}};'>{{color}}</h1>
```

虽然使用`{{`、`}}`分隔标签，但Svelte不使用Mustache语法。 标签只是JavaScript表达式。

### Triples（直接输出html）

普通的分隔标签将会生成存文本，如果你需要将表达式转化成HTML，你可以把他包含在三个`{{{`,`}}}`中

```html
<p>This HTML: {{html}}</p>
<p>Renders as: {{{html}}}</p>
```

与普通标签一样，你也可以在其中使用Javascript表达式，并且当数据变化的时候也会自动更新内容

> 使用三个花括号的形式将不会对HTML进行转义！如果你想显示用户输入，你有义务先对数据进行转义，否则你可能会遭受不同形式的攻击

### 条件渲染

通过将其包装在`if`块来控制模板的一部分是否被渲染。

```html
{{#if user.loggedIn}}
  <a href='/logout'>log out</a>
{{/if}}

{{#if !user.loggedIn}}
  <a href='/login'>log in</a>
{{/if}}
```

你也可以把上面两个`block`通过`{{else}}`合并：

```html
{{#if user.loggedIn}}
  <a href='/logout'>log out</a>
{{else}}
  <a href='/login'>log in</a>
{{/if}}
```

你也可以使用`{{elseif ...}}`:

```html
{{#if x > 10}}
  <p>{{x}} is greater than 10</p>
{{elseif 5 > x}}
  <p>{{x}} is less than 5</p>
{{else}}
  <p>{{x}} is between 5 and 10</p>
{{/if}}
```

### 列表渲染

循环数据列表

```html
<h1>Cats of YouTube</h1>

<ul>
  {{#each cats as cat}}
    <li><a target='_blank' href='{{cat.video}}'>{{cat.name}}</a></li>
  {{/each}}
</ul>
```

你可以使用`name,index`的方式来访问当前元素的索引

```js
<div class='grid'>
  {{#each rows as row, y}}
    <div class='row'>
      {{#each columns as column, x}}
        <code class='cell'>
          {{x + 1}},{{y + 1}}:
          <strong>{{row[column]}}</strong>
        </code>
      {{/each}}
    </div>
  {{/each}}
</div>
```

### 指令

最后，Svelte模板语法不同于传统HTML的地方在于：指令允许你添加添加事件处理程序，双向绑定，`refs(引用)`等特殊申明。我们在后面将会逐一介绍，你现在需要知道的是，可以通过`:`字符的方式来识别指令：

```html
<p>Count: {{count}}</p>
<button on:click='set({ count: count + 1 })'>+1</button>
```

> 从技术上来说，`:`用来表示HTML属性的命名空间，如果遇到的话，将不会被视为指令

## 样式作用域

Svelte的一个重要原则就是组件是独立的，可以在不同的上下文中复用，因此它有一个独特的CSS隔离机制，这样就不会意外的干扰到相同选择器的其他页面了

### 添加样式

你的组件可以有一个`<style>`标签，像这样：
```html
<div class='foo'>
  Big red Comic Sans
</div>

<style>
  .foo {
    color: red;
    font-size: 2em;
    font-family: 'Comic Sans MS';
  }
</style>
```

### 如何工作的

打开示例，检查元素以查看发生了什么，你会发现，Svelte已经向元素添加了一个svelte-[uniqueid]属性。并相应地转换了CSS选择器。因为页面上没有其他元素可以共享该选择器，所以页面上`class ='foo'`的任何其他元素都不会受到我们的样式的影响。

这比通过[Shadow DOM](http://caniuse.com/#search=shadow%20dom)实现相同的效果要简单得多，并且在没有polyfills的地方使用。

> Svelte将会向含有该组件的页面添加一个<style>标签。如果您的网站具有[内容安全策略](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)，则动态添加样式可能是不可能的。如果是这样，您可以使用[服务器渲染CSS](https://svelte.technology/guide#rendering-css)。并在编译时使用`css：false`选项，或使用CLI的`--no-css`操作。

### 级联规则

通常的级联机制仍然适用，任何全局的`.foo`样式仍然可以应用，如果我们的模板具有嵌套的组件与`class='foo'`元素，它们将继承我们的样式。

> 样式隔离不是动态的，组件是一个已经编译好的实例，因此你不能在CSS中使用`{{tag}}`.

## 行为

除了样式与模板之外，组件还可以封装行为逻辑，为此，我们添加了一个`<script>`标签元素并导出了一个对象

```js
<div>
  <!-- template goes here -->
</div>

<script>
  export default {
    // behaviours go here
  };
</script>
```

### 默认数据

通常，组件具有默认数据是有意义的，它将会通过一个函数表达式来返回一个纯粹的JS对象

```html
<p>Count: {{count}}</p>
<button on:click='set({ count: count + 1 })'>+1</button>

<script>
  export default {
    data () {
      return {
        count: 0
      };
    }
  };
</script>
```

实例化`new Component(...)`传入的数据优先级高于默认值

> 上面的代码中使用了ES6的语法,Svelte会生成ES5的代码运行在各种平台上，它本身不会将ES6转化成ES5，所以如果你想使用ES6的语法，你需要了你的构建项目中使用[Babel](https://babeljs.io/)

### 计算属性

通常你的程序使用的某个值依赖了其他的某个值，举个例子，您可能有一个过滤的列表，这取决于列表和过滤器。通常在JavaScript中，当任何依赖关系发生变化时，您必须添加逻辑来更新依赖属性。这样是常见的错误来源，随着应用的逐渐扩大，它会变得原来越糟糕。

Svelte允许您在计算属性中表达这些依赖关系，每当这些依赖关系更改时，它们将重新计算：

```js
<p>
  The time is
  <strong>{{hours}}:{{minutes}}:{{seconds}}</strong>
</p>

<script>
  export default {
    data () {
      return {
        time: new Date()
      };
    },

    computed: {
      hours: time => time.getHours(),
      minutes: time => time.getMinutes(),
      seconds: time => time.getSeconds()
    }
  };
</script>
```

注意，我们需要告诉Svelte，`hours`，`minutes`，`seconds`都依赖time，`time`会作为参数传入函数中，至于依赖关系追踪的过程会在编译的时候解决

> `computed`字段**必须**是一个可迭代的对象，同时属性必须都是函数或者箭头函数表达式

### 生命周期钩子

Svelte提供两个控制逻辑的生命周期钩子`oncreate`，`ondestory`：

```js
<p>
  The time is
  <strong>{{hours}}:{{minutes}}:{{seconds}}</strong>
</p>

<script>
  export default {
    oncreate () {
      this.interval = setInterval( () => {
        this.set({ time: new Date() });
      }, 1000 );
    },

    ondestroy () {
      clearInterval( this.interval );
    },

    data () {
      return {
        time: new Date()
      };
    },

    computed: {
      hours: time => time.getHours(),
      minutes: time => time.getMinutes(),
      seconds: time => time.getSeconds()
    }
  };
</script>
```

### Helpers

Helpers是一个小巧的函数，可以用于模板，下面的例子中，我们需要确保分钟和秒时刻都是两位的，我们需要自动补0，因此，我们添加了一个名为`leftPad`的Helper

```js
<p>
  The time is
  <strong>{{hours}}:{{leftPad(minutes, 2, '0')}}:{{leftPad(seconds, 2, '0')}}</strong>
</p>

<script>
  import leftPad from 'left-pad';

  export default {
    helpers: {
      leftPad
    },

    oncreate () {
      this.interval = setInterval( () => {
        this.set({ time: new Date() });
      }, 1000 );
    },

    ondestroy () {
      clearInterval( this.interval );
    },

    data () {
      return {
        time: new Date()
      };
    },

    computed: {
      hours: time => time.getHours(),
      minutes: time => time.getMinutes(),
      seconds: time => time.getSeconds()
    }
  };
</script>
```

当然你也可以在计算属性中使用leftPad，而不是在模板中使用。什么时候你应该使用Helper还是计算属性，这点并没有硬性规定，也没有表示哪一个比较快的规则，你如何做，取决于你的组件怎样容易让下一个开发者更容易理解。

> helper 函数应该是一个纯函数，换句话说就是在使用过程中不应该产生副作用，而他的返回值也仅仅只和传入参数有关。


### 自定义方法

除了[内置的方法](https://svelte.technology/guide#component-api)之外，你还可以使用自定义的方法。

```html
<script>
  export default {
    methods: {
      say: function ( message ) {
        alert( message ); // again, please don't do this
      }
    }
  };
</script>
```

这些方法将会成为组件API的一部分。

```js
import MyComponent from './MyComponent.html';

var component = new MyComponent({
  target: document.querySelector( 'main' )
});

component.say( '👋' );
```

任何方法，都能被内部的事件[处理函数](https://svelte.technology/guide#event-handlers)调用。

```js
<button on:click='say("hello")'>say hello!</button>
```

### 自定义事件处理

我们过后会学习[事件处理](https://svelte.technology/guide#event-handlers)，如果你需要，可以先跳过这里，最后再回过头来看！

大多数时候，你可以使用标准的DOM事件，但是有时候你可能需要自定义的时间去处理手势之类的操作。

自定义事件只是将节点和回调作为参数的函数，并返回一个具有`destroy`方法的对象，当该元素从页面中删除时，该方法被调用：

```html
<button on:longpress='set({ done: true })'>click and hold</button>

{{#if done}}
  <p>clicked and held</p>
{{/if}}

<script>
  export default {
    events: {
      longpress ( node, callback ) {
        function onmousedown ( event ) {
          const timeout = setTimeout( () => callback( event ), 1000 );

          function cancel () {
            clearTimeout( timeout );
            node.removeEventListener( 'mouseup', cancel, false );
          }

          node.addEventListener( 'mouseup', cancel, false );
        }

        node.addEventListener( 'mousedown', onmousedown, false );

        return {
          destroy () {
            node.removeEventListener( 'mousedown', onmousedown, false );
          }
        };
      }
    }
  };
</script>
```

### 命名空间

假设组件在HTML命名空间中，如果你的组件是设计给`<svg>`元素使用的，则需要指定命名空间。

App.html

```html
<svg viewBox='0 0 1000 1000' style='width: 100%; height: 100%;'>
  <SmileyFace x='70' y='280' size='100' fill='#f4d9c7'/>
  <SmileyFace x='800' y='250' size='150' fill='#40250f'/>
  <SmileyFace x='150' y='700' size='110' fill='#d2aa7a'/>
  <SmileyFace x='875' y='730' size='130' fill='#824e2e'/>
  <SmileyFace x='450' y='500' size='240' fill='#d2b198'/>
</svg>

<script>
  import SmileyFace from './SmileyFace.html';

  export default {
    components: { SmileyFace }
  };
</script>

```

SmileyFace.html
```html
<!-- CC-BY-SA — https://commons.wikimedia.org/wiki/File:718smiley.svg -->
<g transform='translate({{x}},{{y}}) scale({{size / 366.5}})'>
  <circle r="366.5"/>
  <circle r="336.5" fill="{{fill}}"/>
  <path d="m-41.5 298.5c-121-21-194-115-212-233v-8l-25-1-1-18h481c6 13 10 27 13 41 13 94-38 146-114 193-45 23-93 29-142 26z"/>
  <path d="m5.5 280.5c52-6 98-28 138-62 28-25 46-56 51-87 4-20 1-57-5-70l-423-1c-2 56 39 118 74 157 31 34 72 54 116 63 11 2 38 2 49 0z" fill="#871945"/>
  <path d="m-290.5 -24.5c-13-26-13-57-9-85 6-27 18-52 35-68 21-20 50-23 77-18 15 4 28 12 39 23 18 17 30 40 36 67 4 20 4 41 0 60l-6 21z"/>
  <path d="m-132.5 -43.5c5-6 6-40 2-58-3-16-4-16-10-10-14 14-38 14-52 0-15-18-12-41 6-55 3-3 5-5 5-6-1-4-22-8-34-7-42 4-57.6 40-66.2 77-3 17-1 53 4 59h145.2z" fill="#fff"/>
  <path d="m11.5 -23.5c-2-3-6-20-7-29-5-28-1-57 11-83 15-30 41-52 72-60 29-7 57 0 82 15 26 17 45 49 50 82 2 12 2 33 0 45-1 10-5 26-8 30z"/>
  <path d="m198.5 -42.5c4-5 5-34 4-50-2-14-6-24-8-24-1 0-3 2-6 5-17 17-47 13-58-9-7-16-4-31 8-43 4-4 7-8 7-9 0 0-4-2-8-3-51-17-105 20-115 80-3 15 0 43 3 53z" fill="#fff"/>
  <path d="m137.5 223.5s-46 40-105 53c-66 15-114-7-114-7s14-76 93-95c76-18 126 49 126 49z" fill="#f9bedd"/>
</g>

<script>
  export default {
    // you can either use the shorthand 'svg', or the full
    // namespace: 'http://www.w3.org/2000/svg'. (I know
    // which one I prefer.)
    namespace: 'svg',

    data () {
      return {
        x: 100,
        y: 100,
        size: 100
      };
    }
  };
</script>
```

## 嵌套组件

除了嵌套元素（条件渲染，列表渲染），Svelte组件可以还包含其他Svelte组件。

```html
<div class='widget-container'>
  <Widget foo bar='static' baz='{{dynamic}}'/>
</div>

<script>
  import Widget from './Widget.html';

  export default {
    data () {
      return {
        dynamic: 'this can change'
      }
    },

    components: {
      Widget
    }
  };
</script>
```

上面的例子等同于下面的内容。

```html
import Widget from './Widget.html';

const widget = new Widget({
  target: document.querySelector( '.widget-container' ),
  data: {
    foo: true,
    bar: 'static',
    baz: 'this can change'
  }
});
```

Svelte将确保在父组件`baz`中`dynamic`的值会被同步，并且在父组件被销毁时先销毁子组件。

> 组件名称应该按照javascript构造函数的形式约定首字母大写，这也是一种区分元素和模板的简单方法。

### Yield tags

组件可以包含 `{{yeild}}` 标签，来支持父组件插住内容。

APP.html
```html
{{#if showModal}}
  <Modal on:destroy='set({ showModal: false })'>
    <h2>Hello!</h2>
    <p>This is a modal dialog box. it can contain anything</p>
  </Modal>
{{else}}
  <button on:click='set({ showModal: true })'>show modal</button>
{{/if}}

<script>
  import Modal from './Modal.html';

  export default {
    components: { Modal }
  };
</script>
```

modal.html
```html
<div class='modal-background' on:click='destroy()'></div>

<div class='modal'>
  {{yield}} <!-- content is injected here -->
  <button on:click='destroy()'>close modal</button>
</div>

<style>
  .modal-background {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.3);
  }

  .modal {
    position: absolute;
    left: 50%;
    top: 50%;
    width: calc(100% - 4em);
    max-width: 32em;
    transform: translate(-50%,-50%);
    padding: 1em;
    border-radius: 0.2em;
    background: white;
    text-align: center;
  }
</style>
```

### <:Self> tags

有时候一个组件需要能够嵌套自身。例如，如果你有一个树状的数据结构。在Svelte，这是通过<：Self>标签完成的：

```html
{{#if countdown > 0}}
  <p>{{countdown}}</p>
  <:Self countdown='{{countdown - 1}}'/>
{{else}}
  <p>liftoff!</p>
{{/if}}
```

### <:Window> tags

特殊的<：Window>标签为您提供了一种方便的方式来向窗口声明性地添加事件侦听器。当组件被销毁时，事件侦听器将被自动删除。

```html
<:Window on:keydown='set({ key: event.key, keyCode: event.keyCode })'/>

{{#if key}}
  <p><kbd>{{key === ' ' ? 'Space' : key}}</kbd> (code {{keyCode}})</p>
{{else}}
  <p>click in this window and press any key</p>
{{/if}}

<style>
  kbd {
    background-color: #eee;
    border: 2px solid #f4f4f4;
    border-right-color: #ddd;
    border-bottom-color: #ddd;
    font-size: 2em;
    margin: 0 0.5em 0 0;
    padding: 0.5em 0.8em;
    font-family: Inconsolata;
  }
</style>
```

目前为止，你可以绑定`innerWidth`，`outerWidth`，`innerHeight`，`outerHeight`，`scrollX`和`scrollY`

```html
<:Window bind:scrollY='y'/>

<div class='background'></div>
<p class='fixed'>user has scrolled {{y}} pixels</p>

<style>
  .background {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 9999px;
    background: linear-gradient(to bottom, #7db9e8 0%,#0a1d33 100%);
  }

  .fixed {
    position: fixed;
    top: 1em;
    left: 1em;
    color: white;
  }
</style>
```

## 元素指令

Svelte的指令是指元素或组件级指令，它们看起来像属性，只是多了一个`:`字符。

### 事件处理程序

在大多数应用中，你需要响应用户的操作。在Svelte中，这是通过指令`on:[event]`来实现的。

```html
<p>Count: {{count}}</p>
<button on:click='set({ count: count + 1 })'>+1</button>
```

当用户点击按钮时，Svelte使用提供的参数来调用`component.set(...)`。你可以调用属于组件的任何一个方法（无论是[内置](https://svelte.technology/guide#component-api)的还是[自定义](https://svelte.technology/guide#custom-methods)的）以及范围内的任何数据属性（包括计算属性）。

```html
<p>Select a category:</p>

{{#each categories as category}}
  <button on:click='select( category )'>select {{category}}</button>
{{/each}}

<script>
  export default {
    data () {
      return {
        categories: [
          'animal',
          'vegetable',
          'mineral'
        ]
      }
    },

    methods: {
      select ( name ) {
        alert( `selected ${name}` );
      }
    }
  };
</script>

```

你也可以在调用过程中访问`event`对象：
```html
<div on:mousemove='set({ x: event.clientX, y: event.clientY })'>
  coords: {{x}},{{y}}
</div>

<style>
  div {
    border: 1px solid purple;
    width: 100%;
    height: 100%;
  }
</style>
```

目标节点可以使用`this`来引用，这意味着你可以这样做。

```js
<input on:focus='this.select()'>
```

### 自定义事件

你可以定义你自己的自定义事件来处理复杂的用户交互，如拖拽和滑动。更多信息，可以参考有关[自定义事件处理程序](https://svelte.technology/guide#custom-event-handlers)的部分。


### 组件的事件

对于[嵌套组件](https://svelte.technology/guide#nested-components)子组件向父组件通信，使用事件是一个很好的方式。我们来看一个早期的例子，但是把它变成了一个`<CategoryChooser>`组件：

```html
<!-- CategoryChooser.html -->
<p>Select a category:</p>

{{#each categories as category}}
  <button on:click='fire( "select", { category } )'>select {{category}}</button>
{{/each}}

<script>
  export default {
    data () {
      return {
        categories: [
          'animal',
          'vegetable',
          'mineral'
        ]
      }
    }
  };
</script>
```

当用户点击按钮时，组件将会触发select事件，事件对象具有`category`属性，任何嵌套了`<CategoryChooser>`的组件都能像如下的方式监听该事件：

```html
<CategoryChooser on:select='playTwentyQuestions( event.category )'/>

<script>
  import CategoryChooser from './CategoryChooser.html';

  export default {
    components: {
      CategoryChooser
    },

    methods: {
      playTwentyQuestions ( category ) {
        // TODO implement
      }
    }
  };
</script>
```

### Refs（引用）

引用是存储对特定的DOM节点或组件的一种方便的途径，通过`ref:[name]`申明一个`ref`,并能在组件中通过`this.ref.[name]`来访问到他：

```html
<canvas ref:canvas width='200' height='200'></canvas>

<script>
  export default {
    oncreate () {
      const canvas = this.refs.canvas;
      const ctx = canvas.getContext( '2d' );

      let destroyed = false;
      this.on( 'destroy', () => destroyed = true );

      function loop () {
        if ( destroyed ) return;
        requestAnimationFrame( loop );

        const imageData = ctx.getImageData( 0, 0, canvas.width, canvas.height );

        for ( let p = 0; p < imageData.data.length; p += 4 ) {
          const i = p / 4;
          const x = i % canvas.width;
          const y = i / canvas.height >>> 0;

          const t = window.performance.now();

          const r = 64 + ( 128 * x / canvas.width ) + ( 64 * Math.sin( t / 1000 ) );
          const g = 64 + ( 128 * y / canvas.height ) + ( 64 * Math.cos( t / 1000 ) );
          const b = 128;

          imageData.data[ p + 0 ] = r;
          imageData.data[ p + 1 ] = g;
          imageData.data[ p + 2 ] = b;
          imageData.data[ p + 3 ] = 255;
        }

        ctx.putImageData( imageData, 0, 0 );
      }

      loop();
    }
  }
</script>
```

> 由于只有一个元素或组件可以使用给定的引用，因此请勿在`{{#each}}`中使用它们，不过你依然可以在`{{#if}}`中使用。

### 双向数据绑定

目前流行的观点认为：应该避免在场景中使用双向绑定，因为它会引起各种难以调试的BUG，并降低程序运行效率，并且自上而下的单向数据流更容易让人理解。这其实是一个高级的废话。
确实双向绑定做得很糟糕，有各种各样的问题，而且大型应用因为深度嵌套可能会导致状态变化时影响到了与应用不相关的部分。所以它们从禁止组件深度嵌套的约束中受益。
但是当正确使用双向绑定时，它能极大的简化很多事情。

使用`bind:[attribute]`来申明指令。

```html
<input bind:value='name' placeholder='enter your name'>
<p>Hello {{name || 'stranger'}}!</p>
```

> 双向绑定还未能完全的实现，后续会实现全套可用的绑定功能。

除了DOM元素，你还可以给组件绑定数据属性。

```
<CategoryChooser bind:category='category'/>
```

如果绑定的属性和数据共用一个名字，你还可以使用简写：

```html
<CategoryChooser bind:category/>
```

下面是一个完整的在表单中使用双向绑定的例子。

```html
<form on:submit='handleSubmit( event )'>
  <input bind:value='test' type='text'>
  <button type='submit'>Store</button>
</form>

<script>
export default {
  methods: {
    handleSubmit: function ( event ) {
      // prevent the page from reloading
      event.preventDefault();

      var value = this.get( 'test' );
      console.log( 'value', value );
    }
  }
};
</script>
```

## 插件

todo...（作者写的todo）

## 服务端渲染

到目前为止，我们一直都在讨论在浏览器客户端上创建Svelte组件，但是你也可以在Node.js中渲染Svelte组件。这可以让你感到更好的性能上的提升，应用的页面在下载的时候就可以开始渲染了，这个动作比任何javascript代码执行都要早。在某些情况下，这也利于SEO，对于那些因各种原因不能运行javascript的浏览器来说也是有利的。

### 渲染HTML

要想使用服务端渲染，我们必须先注册它，这意味着当你`require`你的`.html`组件的时候，它已经被服务端渲染编译器拦截到了：

```js
require( 'svelte/ssr/register' );
```

在这之后，你可以这样加载组件：
```js
const thing = require( './components/Thing.html' );
```

组件在Node.js中具有不同的API,创建的实例没有`set(...)`和`get(...)`方法。组件是一个拥有`render(data)`方法的对象，该方法会返回HTML，数据对象`data`与在浏览器中实例化组件时使用的一样的，是可选的：

```js
const data = { answer: 42 };
const html = thing.render( data );
```

任何[默认数据](https://svelte.technology/guide#default-data)，[计算属性](https://svelte.technology/guide#computed-properties)，[助手](https://svelte.technology/guide#helpers)，[嵌套组件](https://svelte.technology/guide#nested-components)都能按照预期工作。


> SSR编译器将为你的每个组件生成一个CommonJS模块，这意味着导入和导出语句将会转化为`require`和`module.exports`等选项。如果你的组件依赖了非组件，则他们必须在Node中作为CommonJS模块。如果使用了ES2015模块，建议你将其自动转化成CommonJS。

### 渲染CSS

你也可以渲染组件[隔离](https://svelte.technology/guide#scoped-styles)的CSS包括任何嵌套组件：

```js
const { css, components } = thing.renderCss();
```

你可以将生成的CSS放在单独的样式表中，或者将他们包含在`<style>`标签的页面中。如果这样做，你可能希望防止客户端编辑器再次包含CSS，对于`svelte-cli`，使用`--no-css`标志。在构建工具中（如`rollup-plugin-svelte`）,传递`css:false`选项。

> 上面的`components`数组，包含了每一个嵌套组件的样式的对象，允许你去除重复多余的样式，不过在大多数时候，你无需这么做。

## todo

此文档仍是一项正在进行的工作，就想Svelte本身一样。如果有特定的东西丢失或可以改进，那么请在[GitHub上](https://github.com/sveltejs/svelte.technology)提出一个issue！