* 在Vue中，一个核心的概念就是：数据驱动，避免手动操作DOM元素。这样的话，可以让前端程序员可以更多的时间去关注数据的业务逻辑，而不是关心 DOM 是如何渲染的了。![](./Vue/1.png)

* Vue的系统指令

  * 插值表达式 {{}}

    数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值。例如：\<span>Message: {{ msg }}\</span>，Mustache 标签将会被替代为对应数据对象上 msg 属性（msg定义在data对象中）的值。 无论何时，绑定的数据对象上 msg 属性发生了改变，插值处的内容都会自动更新。
    {{}}对JavaScript 表达式支持，但是有个限制就是，每个绑定都只能包含单个表达式

  * v-cloak

    保持和元素实例的关联，直到结束编译后自动消失。

    v-cloak指令和CSS 规则一起用的时候，能够解决差值表达式闪烁的问题（即：可以隐藏未编译的标签直到实例准备完毕）。比如说，插值表达式{{name}}中的name内容，在网速很慢的情况下，一开始会直接显示{{name}}这个内容，等网络加载完成了，才会显示对应的值。那这个闪烁的问题该怎么解决呢？解决办法是：通过v-cloak隐藏{{name}}这个内容，当加载完毕后，再显示出来。

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <head>
      <meta charset="UTF-8">
      <title>Document</title>
      <style>
        /*2、在样式表里设置：只要是有 v-cloak 属性的标签，我都让它隐藏。
        直到 Vue实例化完毕以后，v-cloak 会自动消失，那么对应的css样式就会失去作用，最终将span中的内容呈现给用户 */
        [v-cloak] {
          display: none;
        }
      </style>
    </head>
    <body>
      <div id="app">
        <!-- 1、给 span 标签添加 v-cloak 属性 -->
        <span v-cloak>{{name}}</span>
      </div>
    </body>
    <script src="vue2.5.16.js"></script>
    <script>
      new Vue({
        el: '#app',
        data: {
          name: 'smyhvae'
        }
      });
    </script>
    </html>
    ```

  * v-text

    v-text可以将一个变量的值渲染到指定的元素中。

    插值表达式和 v-text 的区别

    * v-text 没有闪烁的问题，因为它是放在属性里的。
    * 插值表达式只会替换自己的这个占位符，并不会把整个元素的内容清空。v-text 会覆盖元素中原本的内容。

  * v-html

    v-text是纯文本，而v-html会被解析成html元素。

    注意：使用v-html渲染数据可能会非常危险，因为它很容易导致 XSS（跨站脚本） 攻击，使用的时候请谨慎，能够使用{{}}或者v-text实现的不要使用v-html。

  * v-bind

    v-bind：用于绑定属性。比如说：

    ```html
    <img v-bind:src="imageSrc +'smyhvaeString'">
    <div v-bind:style="{ fontSize: size + 'px' }"></div>
    或
    <img :src="imageSrc">
    <div :style="{ fontSize: size + 'px' }"></div>
    ```

    上方代码中，给属性加了 v-bind 之后，属性值里的整体内容是表达式，属性值里的imageSrc和size是Vue实例里面的变量。也就是说， v-bind的属性值里，可以写合法的 js 表达式。属性值的写法如下面两种例子所示

    Vue中通过属性绑定为元素设置class 类样式

    * 直接传递一个数组。
    * 在数组中使用三元表达式
    * 在数组中使用对象来代替三元表达式
    * 直接使用对象

    Vue中通过属性绑定为元素设置 style 行内样式

    * 直接在元素上通过 :style 的形式，书写样式对象
    * 将样式对象，定义到 data 中，并直接引用到 :style 中
    * 在 :style 中通过数组，引用多个 data 上的样式对象

  * v-on

    绑定事件，并提供了很多事件修饰符来辅助实现一些功能

    ```html
     <button v-on:click="change">改变name的值</button>
    可以简写成：
        <button @click="change">改变name的值</button>
    
     <!-- 阻止默认行为，没有表达式 -->
            <form @submit.prevent></form>
            <!--  串联修饰符 -->
            <button @click.stop.prevent="doThis"></button>
    ```

    既然.stop和.self都可以阻止冒泡，那二者有什么区别呢？区别在于：前者能够阻止整个冒泡行为，而后者只能阻止自己身上的冒泡行为

  * v-model

    我现在要做的是：我在<input>标签里修改内容，要求data里的name的值自动更新。从而实现双向数据绑定。该怎么做呢？这就可以利用v-model这个属性。

    区别：

    * v-bind：只能实现数据的单向绑定，从 M 自动绑定到 V。
    * v-model：只有v-model才能实现双向数据绑定。注意，v-model 后面不需要跟冒号
    * 注意：v-model 只能运用在表单元素中，或者用于自定义组件。常见的表单元素包括：input(radio, text, address, email....) 、select、checkbox 、textarea。

  * v-for

    * 普通数组的遍历
    * 对象数组的遍历
    * 对象的遍历
    * 遍历数字

    注意：在 Vue 2.2.0+ 版本里，当在组件中使用 v-for 时，key 属性是必须要加上的。这样做是因为：每次 for 循环的时候，通过指定 key 来标示当前循环这一项的唯一身份。当 Vue.js 用 v-for 正在更新已渲染过的元素列表时，它默认用 “就地复用” 策略。如果数据项的顺序被改变，Vue将不是移动 DOM 元素来匹配数据项的顺序， 而是简单复用此处每个元素，并且确保它在特定索引下显示已被渲染过的每个元素。为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素，你需要为每项提供一个唯一 key 属性。key的类型只能是：string/number，而且要通过 v-bind 来指定。

  * v-if

    v-if：设置元素的显示和隐藏（添加/删除DOM元素）

    作用：根据表达式的值的真假条件，来决定是否渲染元素，如果为false则不渲染（达到隐藏元素的目的），如果为true则渲染。在切换时，元素和它的数据绑定会被销毁并重建

  * v-show

    v-show：设置元素的显示和隐藏（在元素上添加/移除style="display:none"属性）

    作用：根据表达式的真假条件，来切换元素的 display 属性。如果为false，则在元素上添加 display:none属性；否则移除display:none属性。

    v-if和v-show都能够实现对一个元素的隐藏和显示操作。

    v-if和v-show的区别：

    * v-if：每次都会重新添加/删除DOM元素
    * v-show：每次不会重新进行DOM的添加/删除操作，只是在这个元素上添加/移除style="display:none"属性，表示节点的显示和隐藏。
    * v-if：有较高的切换性能消耗。这个很好理解，毕竟每次都要进行dom的添加／删除操作。
    * v-show：有较高的初始渲染消耗。也就是说，即使一开始v-show="false"，该节点也会被创建，只是隐藏起来了。而v-if="false"的节点，根本就不会被创建。

    总结：

    * 如果元素涉及到频繁的切换，最好不要使用 v-if, 而是推荐使用 v-show
    * 如果元素可能永远也不会被显示出来被用户看到，则推荐使用 v-if

  * 注意事项

    在Vue的实例中，如果想要获取data里的属性、methods里面的方法，都要通过this来访问。这里的this指向的是Vue的实例对象。

    VM实例，会监听自己身上 data 中所有数据的改变，只要数据一发生变化，就会自动把最新的数据，从 data 上同步到页面中去。这样做 的好处是：程序员只需要关心数据，不需要考虑如何重新渲染DOM页面；减少DOM操作。

* 过滤器

  概念：Vue.js 允许我们自定义过滤器，可被用作一些常见的文本格式化。过滤器可以用在两个地方：mustache 插值表达式、 v-bind表达式。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符指示。

* 自定义按键修饰符&自定义指令

* vue生命周期

  vue实例的生命周期：从Vue实例创建、运行、到销毁期间，总是伴随着各种各样的事件，这些事件，统称为生命周期。

  生命周期钩子：就是生命周期事件的别名而已。

  生命周期钩子 = 生命周期函数 = 生命周期事件。

* vue动画

* Vue组件的定义和注册

* vue组件之间的传值

* Vue-router路由

  后端路由，就是把所有url地址都对应到服务器的资源，这个对应关系就是路由。

  前端路由：就是SPA（单页应用）的路径管理器。

  对于单页面应用程序来说，主要通过URL中的hash（url地址中的#号）来实现不同页面之间的切换。同时，hash有一个特点：HTTP请求中不会包含hash相关的内容。所以，单页面程序中的页面跳转主要用hash实现。

  总结：在单页应用程序中，这种通过hash改变来切换页面的方式，称作前端路由（区别于后端路由）。