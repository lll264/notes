# 概述

## 简介

Vue (读音 /vjuː/，类似于 view) 是一套 **构建用户界面 ** 的 **渐进式框架**基于数据**渲染出用户可以看到的**界面

## 导入

Vue是一个类似于Jquery的一个JS框架，所以，如果想使用Vue，则在当前页面导入Vue.js文件即可。

```js
<!-- 在线导入 -->
<!-- 开发环境版本，包含了用帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>

<!-- 本地导入 -->
<script src="node_modules/vue/dist/vue.js"></script>

```

## 基本使用

**核心步骤（4步）：**

1. 准备容器
2. 引包（官网） — 开发版本/生产版本
3. 创建Vue实例  new Vue()
4. 指定配置项，渲染数据
   1. el:指定挂载点
   2. data提供数据

![image-20250409164356761](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250409164356761.png)

## 核心特性

### 响应式

数据变化，视图自动更新

![image-20250409213728407](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250409213728407.png)

# 基本语法

## 插值表达式

### 作用

插值表达式用户把vue中所定义的数据,显示在页面上. 插值表达式允许用户输入JS代码片段的表达式

### 语法

插值表达式语法：{{ 表达式 }}

```vue
<h3>{{title}}<h3>

<p>{{nickName.toUpperCase()}}</p>

<p>{{age >= 18 ? '成年':'未成年'}}</p>

<p>{{obj.name}}</p>

<p>{{fn()}}</p>
```

### 注意

```vue
1.在插值表达式中使用的数据 必须在data中进行了提供
<p>{{hobby}}</p>  //如果在data中不存在 则会报错

2.支持的是表达式，而非语句，比如：if   for ...
<p>{{if}}</p>

3.不能在标签属性中使用 {{  }} 插值 (插值表达式只能标签中间使用)
想要设置属性就要使用v-bind
<p title="{{username}}">我是P标签</p>
```

## Vue中的常用指令

**概念：**指令（Directives）是 Vue 提供的带有 **v- 前缀** 的 特殊 标签**属性**。

为啥要学：提高程序员操作 DOM 的效率。vue 中的指令按照不同的用途可以分为如下 6 大类：

### **内容渲染指令（v-html、v-text）**

不同于插值表达式，可以解析html标签

```vue
<div id="app">
  <div v-html="msg"></div>
</div>
const app = new Vue({
  el: '#app',
  data: {
    msg: `
      <h3>学前端~来黑马！</h3>  `
  }
})


事件绑定指令（v-on）

属性绑定指令 （v-bind）

双向绑定指令（v-model）

列表渲染指令（v-for）

指令是 vue 开发中最基础、最常用、最简单的知识点。
```

### **条件渲染**

通过v-show/if/else/else-if = "表达式" 的条件判断方式决定是否显示元素。

```vue
<!-- 
    v-show底层原理：切换 css 的 display: none 来控制显示隐藏
    v-if  底层原理：根据 判断条件 控制元素的 创建 和 移除（条件渲染）
  -->

<div id="app">
  <div v-show="flag" class="box">我是v-show控制的盒子</div>
  <div v-if="flag" class="box">我是v-if控制的盒子</div>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      flag: false,
    },
  });
</script>
```

```vue
<div id="app">
  <p v-if="gender === 1">性别：♂ 男</p>
  <p v-else>性别：♀ 女</p>
  <hr />
  <p v-if="score >= 90">成绩评定A：奖励电脑一台</p>
  <p v-else-if="score >= 70">成绩评定B：奖励周末郊游</p>
  <p v-else-if="score >= 60">成绩评定C：奖励零食礼包</p>
  <p v-else>成绩评定D：惩罚一周不能玩手机</p>
</div>

<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      gender: 2,
      score: 95,
    },
  });
</script>
```

### 事件绑定 v-on

**语法**

```vue
<!--完整写法-->
<button v-on:事件名="函数名/vue表达式">点我</button>
<!--简化写法-->
<button @事件名="函数名/vue表达式">点我</button>
```

```js
<button @click="count--">-</button>
<button @click="fn">切换显示隐藏</button>
//还可以指定参数
<button @click="buy(5)">可乐5元</button>
```

**案例**

```vue
<div id="app">
  <button @click="count--">-</button>
  <span>{{ count }}</span>
  <button v-on:click="count++">+</button>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      count: 100,
    },
  });
</script>
```

```vue
<div id="app">
  <button @click="fn">切换显示隐藏</button>
  <h1 v-show="isShow">黑马程序员</h1>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app4 = new Vue({
    el: "#app",
    data: {
      isShow: true,
    },
    methods: {
      fn() {
        // 让提供的所有methods中的函数，this都指向当前实例
        // console.log('执行了fn', app.isShow)
        // console.log(app3 === this)
        this.isShow = !this.isShow;
      },
    },
  });
</script>
```

### 属性绑定 v-bind

#### 基本使用

**语法**

```vue
<!--完整写法-->
<标签名 v-bind:标签属性名="vue实例中的数据属性名"/>
<!--简化写法-->
<标签名 :标签属性名="vue实例中的数据属性名"/>
```

插值表达是不能设置属性，因此想要动态设置元素属性可以通过这种方式

**案例**

```vue
<div id="app">
  <!-- v-bind:src   =>   :src -->
  <img v-bind:src="imgUrl" v-bind:title="msg" alt="" />
  <img :src="imgUrl" :title="msg" alt="" />
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: "#app",
    data: {
      imgUrl: "./imgs/10-02.png",
      msg: "hello 波仔",
    },
  });
</script>
```

#### 操作class

为了方便开发者进行样式控制， Vue 扩展了 v-bind 的语法，可以针对 **class 类名** 和 **style 行内样式** 进行控制 。

**语法**

```vue
<div> :class = "对象/数组">这是一个div</div>
//绑定对象
<div class="box" :class="{ 类名1: 布尔值, 类名2: 布尔值 }"></div>
//绑定数组
<div class="box" :class="[ 类名1, 类名2, 类名3 ]"></div>
```

**案例**

```vue
    <ul>
      <li v-for="(item, index) in list" :key="item.id" @click="activeIndex = index">
        <a :class="{ active: index === activeIndex }" href="#">{{ item.name }}</a>
      </li>
    </ul>
```

```vue
<div id="app">
  <div class="box" :class="class1">黑马程序员</div>
  <div class="box" :class="class2">黑马程序员</div>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      class1: { pink: true, big: true },
      class2: ['pink', 'big']
    }
  })
</script>
```

#### 操作style

**语法**

```vue
<div class="box" :style="{ CSS属性名1: CSS属性值, CSS属性名2: CSS属性值 }"></div>
```

**案例**

```vue
  <div id="app">
    <div class="box" :style="{ width: '400px', height: '400px', backgroundColor: 'green' }"></div>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
  <script>
    const app = new Vue({
      el: '#app',
      data: {

      }
    })
  </script>
```



### 循环遍历 v-for

#### 基本使用

**基于数据循环， 多次渲染整个元素**

**语法**：

```javascript
v-for="item in items"
//遍历数组
v-for="(item,index) in items"
//遍历对象
v-for="(value,key,index) in object"
```

items：要迭代的数组
item：存储数组元素的变量名
index：迭代到的当前元素索引，从0开始。

**案例**

```vue
  <div id="app">
    <h3>小黑水果店</h3>
    <ul>
      <li v-for="(item, index) in list">
        {{ ietm }} - {{ index }}
      </li>
    </ul>

    <ul>
      <li v-for="item in list">
        {{ item }}
      </li>
    </ul>
  </div>

  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        list: ['西瓜', '苹果', '鸭梨', '榴莲']
      }
    })
  </script>
```

#### 使用key

**语法**：key属性 = "唯一标识"

**作用**：给列表项添加的唯一标识。便于Vue进行列表项的正确排序复用。

```vue
<li v-for="name in list" :key="name">
  <input type="checkbox"> {{name}}
</li>
```

**v-for 的默认行为会尝试 原地修改元素 （就地复用）**，比如不加key点击删除列表数据，vue默认会删除最后一个元素。加了key才会删除对应的元素

### 表单双向绑定 v-model

#### 使用

Vue的双向绑定可以实现: 数据变化的时候, 页面会自动刷新, 页面变化的时候，数据也会自动变化.

```vue
账户：<input type="text" v-model="username"> <br><br>
密码：<input type="password" v-model="password"> <br><br>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
const app = new Vue({
  el: '#app',
  data: {
    username: '',
    password: ''
  }
})
</script>
```

```
输入框  input:text   ——> value
文本域  textarea	 ——> value
复选框  input:checkbox  ——> checked
单选框  input:radio   ——> checked
下拉菜单 select    ——> value
```

1. 双向绑定, 只能绑定**“文本框,单选按钮,复选框,文本域,下拉列表”**等
2. **文本框/单选按钮/textarea**, 绑定的数据是字符串类型
3. **单个复选框,** 绑定的是boolean类型
4. **多个复选框,** 绑定的是数组
5. **select单选对应字符串，多选对应也是数组**

#### **案例**

```vue
<div id="app">
  <h3>小黑学习网</h3>

  姓名：
    <input type="text" v-model="username"> 
    <br><br>
  是否单身：
    <input type="checkbox" v-model="isSingle"> 
    <br><br>
  <!-- 
    前置理解：
      1. name:  给单选框加上 name 属性 可以分组 → 同一组互相会互斥
      2. value: 给单选框加上 value 属性，用于提交给后台的数据
    结合 Vue 使用 → v-model
  -->
  性别: 
    <input v-model="gender" type="radio" name="gender" value="1">男
    <input v-model="gender" type="radio" name="gender" value="2">女
    <br><br>
  <!-- 
    前置理解：
      1. option 需要设置 value 值，提交给后台
      2. select 的 value 值，关联了选中的 option 的 value 值
    结合 Vue 使用 → v-model
  -->
  所在城市:
    <select v-model="cityId">
      <option value="101">北京</option>
      <option value="102">上海</option>
      <option value="103">成都</option>
      <option value="104">南京</option>
    </select>
    <br><br>

  自我描述：
    <textarea v-model="desc"></textarea> 

  <button>立即注册</button>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      username: '',
      isSingle: false,
      gender: "2",
      cityId: '102',
      desc: ""
    }
  })
</script>
```



## 指令修饰符

所谓指令修饰符就是通过“.”指明一些指令**后缀** 不同的**后缀**封装了不同的处理操作  —> 简化代码

### 按键修饰符

```vue
<div id="app">
  <h3>@keyup.enter  →  监听键盘回车事件</h3>
  <input @keyup.enter="fn" v-model="username" type="text">
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      username: ''
    },
    methods: {
      fn (e) {
        if (e.key === 'Enter') {
          console.log('按下了enter')
        }
        console.log('键盘回车的时候触发', this.username)
      }
    }
  })
</script>
```

### v-model修饰符

- v-model.trim  —>对获取到的vue字符串去除首位空格
- v-model.number —>将获取到的字符串转数字

```vue
    姓名：<input v-model.trim="username" type="text"><br>
    年纪：<input v-model.number="age" type="text"><br>
```

### 事件修饰符

- @事件名.stop —> 阻止冒泡
- @事件名.prevent  —>阻止默认行为
- @事件名.stop.prevent —>可以连用 即阻止事件冒泡也阻止默认行为

## computed计算属性

**概念**

是基于**现有的数据**，计算出来的**新属性**。 **依赖**的数据变化，计算属性会**自动**重新计算。

**语法**

1. 声明在 **computed 配置项**中，一个计算属性对应一个函数
2. 使用起来和普通属性一样使用  {{ 计算属性名}}  

**案例**

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250410154419960.png" alt="image-20250410154419960" style="zoom:67%;" />

```vue
<div id="app">
  <h3>小黑的礼物清单</h3>
  <table>
    <tr>
      <th>名字</th>
      <th>数量</th>
    </tr>
    <tr v-for="(item, index) in list" :key="item.id">
      <td>{{ item.name }}</td>
      <td>{{ item.num }}个</td>
    </tr>
  </table>

  <!-- 目标：统计求和，求得礼物总数 -->
  <p>礼物总数：{{ totalCount }} 个</p>
  也可以直接调用方法，但是不会缓存，性能低
  <p>礼物总数：{{ totalCount() }} 个</p>  
</div>
<script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
<script>
  const app = new Vue({
    el: '#app',
    data: {
      // 现有的数据
      list: [
        { id: 1, name: '篮球', num: 1 },
        { id: 2, name: '玩具', num: 2 },
        { id: 3, name: '铅笔', num: 5 },
      ]
    },
    computed: {
      totalCount () {
        // 基于现有的数据，编写求值逻辑
        // 计算属性函数内部，可以直接通过 this 访问到 app 实例
        // console.log(this.list)

        // 需求：对 this.list 数组里面的 num 进行求和 → reduce
        let total = this.list.reduce((sum, item) => sum + item.num, 0)
        return total
      }
    }
  })
</script>
```

或者也可以通过插值表达式调用method

## watch侦听器（监视器）

**监视数据变化**，执行一些业务逻辑或异步操作

**语法**

1. watch同样声明在跟data同级的配置项中

2. 简单写法： 简单类型数据直接监视

3. 完整写法：添加额外配置项

**案例**

当数据发生变化时，自动调用监听器的方法

```js
const app = new Vue({
  el: '#app',
  data: {
    // words: ''
    obj: {
      words: ''
    }
  },
  // 具体讲解：(1) watch语法 (2) 具体业务实现
  watch: {
    // 该方法会在数据变化时调用执行
    // newValue新值, oldValue老值（一般不用）
    words (newValue) {
      console.log('变化了', newValue)
    }
    'obj.words' (newValue) {
      console.log('变化了', newValue)
    }
  }
})
```

## vue生命周期

Vue生命周期过程中，会**自动运行一些函数**，被称为【**生命周期钩子**】→  让开发者可以在【**特定阶段**】运行**自己的代码**

![image-20250410171849889](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250410171849889.png)

```vue
<div id="app">
    <h3>{{ title }}</h3>
    <div>
      <button @click="count--">-</button>
      <span>{{ count }}</span>
      <button @click="count++">+</button>
    </div>
  </div>
  <script src="https://cdn.jsdelivr.net/npm/vue@2/dist/vue.js"></script>
  <script>
    const app = new Vue({
      el: '#app',
      data: {
        count: 100,
        title: '计数器'
      },
      // 1. 创建阶段（准备数据）
      beforeCreate(){}
	  created(){}
      // 2. 挂载阶段（渲染模板）
      beforeMount(){}
	  mounted(){}
      // 3. 更新阶段(修改数据 → 更新视图)
      beforeUpdate(){}
	  updated(){}
      // 4. 卸载阶段
      beforeDestroy(){}
      destroyed(){}
    })
  </script>
```

### 主要用途

在创建vue数据后，发送请求获取数据

# 组件化开发

## 概述

组件化：一个页面可以拆分成一个个组件，每个组件有着自己独立的结构、样式、行为。

好处：便于维护，利于复用 → 提升开发效率。

组件分类：普通组件、根组件。

一个页面，可以把所有的代码都写在一个页面中，但是这样显得代码比较混乱，难易维护。咱们可以按模块进行组件划分

## 组件注册

### 局部注册

只能在注册的组件内使用

![68222796681](D:\E\学习\Go\笔记\技术栈\前端\assets\1682227966812-1744290721707-2.png)

```js
// 导入需要注册的组件
import 组件对象 from '.vue文件路径'
import HmHeader from './components/HmHeader'

export default {  // 局部注册
  components: {
   '组件名': 组件对象,
    HmHeader:HmHeaer,
    HmHeader
  }
}
```

### 全局注册

全局注册的组件，在项目的**任何组件**中都能使用。

在main.js中

```js
// 导入需要全局注册的组件
import HmButton from './components/HmButton'
Vue.component('HmButton', HmButton)
```

# 组件通信

## 概念

组件通信，就是指**组件与组件**之间的**数据传递**

- 组件的数据是独立的，无法直接访问其他组件的数据。
- 想使用其他组件的数据，就需要组件通信

## 父向子通信

父组件通过v-bind指定要传递的属性

子组件通过props接收对应的属性，然后就可以使用了

![image-20250410213443799](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250410213443799.png)

## 子向父通信

父组件注册事件，并实现对应方法修改对应属性

子组件通过$emit向父组件发送对应修改事件的通知，触发修改事件

![image-20250410214544674](D:\E\学习\Go\笔记\技术栈\前端\assets\image-20250410214544674.png)

## 组件之间v-model双向绑定实现

### v-model原理

v-model本质上是一个语法糖。例如应用在输入框上，就是value属性 和 input事件 的合写。**$event 用于在模板中，获取事件的传递的参数**

```vue
<template>
  <div id="app" >
    <input v-model="msg" type="text">

    <input :value="msg" @input="msg = $event.target.value" type="text">
  </div>
</template>
```

### 父子之间双向绑定实现

提供数据的双向绑定

- 数据变，视图跟着变 :value
- 视图变，数据跟着变 @input

子组件通过prop接收父组件的value，并用v-bind进行绑定

当value发生改变时，触发事件通过父组件修改

### 普通写法

App.vue 父组件

注册事件等待子组件修改数据

```vue
<template>
  <div class="app">
    <BaseSelect :selectId="selectId" @事件名="selectedId=$event"></BaseSelect>
  </div>
</template>

<script>
import BaseSelect from './components/BaseSelect.vue'
export default {
  data() {
    return {
      selectId: '102',
    }
  },
  components: {
    BaseSelect,
  },
}
</script>

<style>
</style>
```

BaseSelect.vue 子组件

```vue
<template>
  <div>
    <select :value="selectId" @change="handleChange">
      <option value="101">北京</option>
      <option value="102">上海</option>
      <option value="103">武汉</option>
      <option value="104">广州</option>
      <option value="105">深圳</option>
    </select>
  </div>
</template>

<script>
export default {
    props: {'selectId'},
    method: {
        handleChange(e) {
            this.$emit('父组件中事件名', e.target.value)
        }
    }
}
</script>

<style>
</style>
```



### 简化写法

子组件

```vue
<select :value="value" @change="handleChange">...</select>
props: {
  value: String
},
methods: {
  handleChange (e) {
    this.$emit('input', e.target.value)
  }
}
```

父组件

```vue
<BaseSelect v-model="selectId"></BaseSelect>
```

等价

```vue
    <BaseSelect :selectId="selectId" @input="selectedId=$event"></BaseSelect>
```

### sync写法

父组件

```vue
//.sync写法
<BaseDialog :visible.sync="isShow" />
--------------------------------------
//完整写法
<BaseDialog 
  :visible="isShow" 
  @update:visible="isShow = $event" 
/>
```

子组件

```vue
props: {
  visible: Boolean
},

this.$emit('update:visible', false)
```

# props

## props校验

为组件的 prop 指定**验证要求**，不符合要求，控制台就会有**错误提示**  → 帮助开发者，快速发现错误

### 语法

- **类型校验**
- 非空校验
- 默认值
- 自定义校验

```js
//指定类型
props: ['age'] 改成如下
props: {
  age: Number
}
```

完整写法

```js
props: {
  校验的属性名: {
    type: 类型,  // Number String Boolean ...
    required: true, // 是否必填
    default: 默认值, // 默认值
    validator (value) {
      // 自定义校验逻辑
      return 是否通过校验
    }
  }
},
```

### 单向数据流

**口诀：谁的数据谁负责**

![68232373422](D:\E\学习\Go\笔记\技术栈\前端\assets\1682323734228-1744294371805-2.png)

# ref和refs获取dom元素

**作用**

利用ref 和 $refs 可以用于 获取 dom 元素 或 组件实例

**特点**

**document.querySelector 会查找项目中所有的元素，而$refs只会在当前组件查找元素**

**语法**

```
1.给要获取的盒子添加ref属性
<div ref="chartRef">我是渲染图表的容器</div>

2.获取时通过 $refs获取  this.\$refs.chartRef 获取
console.log(this.$refs.chartRef)
```



# 异步更新 & $nextTick

## vue异步更新

vue的dom更新是异步的，也就是说vue在执行一个更新dom元素的语句的时候，可能还没更新完就去同时执行后面的语句了。因此会产生问题。

比如这个案例，设置编辑框显示之后，立刻获取焦点是不能成功的！

**原因：Vue 是异步更新DOM  (提升性能)**

```vue
<template>
  <div class="app">
    <div v-if="isShowEdit">
      <input type="text" v-model="editValue" ref="inp" />
      <button>确认</button>
    </div>
    <div v-else>
      <span>{{ title }}</span>
      <button @click="editFn">编辑</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      title: '大标题',
      isShowEdit: false,
      editValue: '',
    }
  },
  methods: {
    editFn() {
        // 显示输入框
        this.isShowEdit = true  
        // 获取焦点
        this.$refs.inp.focus() 
    }  },
}
</script> 
```

## 解决方案

$nextTick：**等 DOM更新后**， 立刻执行此方法里的函数体

**语法:** this.$nextTick(函数体)

```js
this.$nextTick(() => {
  this.$refs.inp.focus()
})
```

**注意：**$nextTick 内的函数体 一定是**箭头函数**，这样才能让函数内部的this指向Vue实例

# 自定义指令

## 概念

自己定义的指令，可以**封装一些DOM操作**，扩展额外的功能

## 基础用法

**注册指令时不用加v-前缀，但使用时一定要加v-前缀**

### 全局注册

```
//在main.js中
Vue.directive('指令名', {
  //inserted表示指令所绑定的元素被添加到页面的时候调用
  "inserted" (el) {
    // 可以对 el 标签，扩展额外功能
    el.focus()
  }
})
```

### 局部注册

```vue
//在Vue组件的配置项中
directives: {
  "指令名": {
    inserted () {
      // 可以对 el 标签，扩展额外功能
      el.focus()
    }
  }
}
```

### 案例

在main.js中注册

```js
// 1. 全局注册指令
Vue.directive('focus', {
  // inserted 会在 指令所在的元素，被插入到页面中时触发
  inserted (el) {
    // el 就是指令所绑定的元素
    // console.log(el);
    el.focus()
  }
})
```

```vue
<template>
  <div>
    <h1>自定义指令</h1>
    <input v-focus ref="inp" type="text">
  </div>
</template>
```

## 指令传值

### 语法

1.在绑定指令时，可以通过“等号”的形式为指令 绑定 具体的参数值

```html
<div v-color="color">我是内容</div>
```

2.通过 **binding.value** 可以拿到指令值，**指令值修改会 触发 update 函数**

```js
directives: {
  color: {
    inserted (el, binding) {
      el.style.color = binding.value
    },
    update (el, binding) {
      el.style.color = binding.value
    }
  }
}
```

### 代码示例

实现一个 color 指令 - 传入不同的颜色, 给标签设置文字颜色

```vue
<template>
  <div>
     <!--显示红色--> 
    <h2 v-color="color1">指令的值1测试</h2>
     <!--显示蓝色--> 
    <h2 v-color="color2">指令的值2测试</h2>
     <button>
        改变第一个h1的颜色
    </button>
  </div>
</template>

<script>
export default {
  data () {
    return {
      color1: 'red',
      color2: 'blue'
    }
  }
}
</script>

<style>

</style>
```

# 插槽

让组件内部的一些 **结构** 支持 **自定义（不直接写死）**

## 基本语法

1. 组件内需要定制的结构部分，改用**<slot></slot>**占位
2. 使用组件时, **<MyDialog></MyDialog>**标签内部, 传入结构替换slot
3. 给插槽传入内容时，可以传入**纯文本、html标签、组件**
4. v-slot写起来太长，vue给我们提供一个简单写法 **v-slot —> #**

![68241032979](D:\E\学习\Go\笔记\技术栈\前端\assets\1682410329794-1744362487047-1.png)



## 默认值

在 <slot> 标签内，放置内容, 作为默认显示内容。

外部使用组件时，传东西了，则slot整体会被换掉

## 结构中多个插槽

如果一个组件内有多处结构，需要外部传入标签，进行定制 。就使用了多处插槽。此时可以用name属性区分

![1682413391727](D:\E\学习\Go\笔记\技术栈\前端\assets\1682413391727.png)

template配合v-slot:名字来分发对应标签

![1682413411921](D:\E\学习\Go\笔记\技术栈\前端\assets\1682413411921.png)

## 作用域插槽

### 作用

定义slot 插槽的同时, 是可以**传值**的。给 **插槽** 上可以 **绑定数据**，将来 **使用组件时可以用**

### 使用步骤

1. 给 slot 标签, 以 添加属性的方式传值

   ```vue
   <slot :id="item.id" msg="测试文本"></slot>
   ```

2. 所有添加的属性, 都会被收集到一个对象中

   ```vue
   { id: 3, msg: '测试文本' }
   ```

3. 在template中, 通过  ` #插槽名= "obj"` 接收，默认插槽名为 default

   ```vue
   <MyTable :list="list">
     <template #default="obj">
       <button @click="del(obj.id)">删除</button>
     </template>
   </MyTable>
   ```

### 案例

```vue
<template>
  <table class="my-table">
    <thead>
      <tr>
        <th>序号</th>
        <th>姓名</th>
        <th>年纪</th>
        <th>操作</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, index) in data" :key="item.id">
        <td>{{ index + 1 }}</td>
        <td>{{ item.name }}</td>
        <td>{{ item.age }}</td>
        <td>
          <!-- 1. 给slot标签，添加属性的方式传值 -->
          <slot :row="item" msg="测试文本"></slot>

          <!-- 2. 将所有的属性，添加到一个对象中 -->
          <!-- 
             {
               row: { id: 2, name: '孙大明', age: 19 },
               msg: '测试文本'
             }
           -->
        </td>
      </tr>
    </tbody>
  </table>
</template>
```

```vue
<template>
  <div>
    <MyTable :data="list">
      <!-- 3. 通过template #插槽名="变量名" 接收 -->
      <template #default="obj">
        <button @click="del(obj.row.id)">
          删除
        </button>
      </template>
    </MyTable>
    
    <MyTable :data="list2">
      <template #default="{ row }">
        <button @click="show(row)">查看</button>
      </template>
    </MyTable>
  </div>
</template>

<script>
import MyTable from './components/MyTable.vue'
export default {
  data () {
    return {
      list: [
        { id: 1, name: '张小花', age: 18 },
        { id: 2, name: '孙大明', age: 19 },
        { id: 3, name: '刘德忠', age: 17 },
      ],
      list2: [
        { id: 1, name: '赵小云', age: 18 },
        { id: 2, name: '刘蓓蓓', age: 19 },
        { id: 3, name: '姜肖泰', age: 17 },
      ]
    }
  },
  methods: {
    del (id) {
      this.list = this.list.filter(item => item.id !== id)
    },
    show (row) {
      // console.log(row);
      alert(`姓名：${row.name}; 年纪：${row.age}`)
    }
  },
  components: {
    MyTable
  }
}
</script>

```

# 路由

## 概念

Vue中的路由：**路径和组件**的**映射**关系

## 基本使用

### VueRouter的使用（5+2）

下载 VueRouter 模块到当前工程，vue2用版本3，vue3用版本4

```
npm install vue-router@3
```

main.js中引入VueRouter

```vue
import VueRouter from 'vue-router'
```

安装注册

```vue
Vue.use(VueRouter)
```

创建路由对象

```vue
const router = new VueRouter()
```

注入，将路由对象注入到new Vue实例中，建立关联

```vue
new Vue({
  render: h => h(App),
  router:router
}).$mount('#app')
```

当我们配置完以上5步之后 就可以看到浏览器地址栏中的路由 变成了 /#/的形式。表示项目的路由已经被Vue-Router管理了

### 两个重要步骤

1. 在创建router时，可以通过routes指定路由匹配

<img src="D:\E\学习\Go\笔记\技术栈\前端\assets\1682479639666.png" alt="1682479639666" style="zoom:67%;" />

2.配置导航，配置路由出口(路径匹配的组件显示的位置)

```vue
<div class="footer_wrap">
  <a href="#/find">发现音乐</a>
  <a href="#/my">我的音乐</a>
  <a href="#/friend">朋友</a>
</div>
<div class="top">
  <router-view></router-view>
</div>
```

### 案例

```js
import Find from './views/Find'
import My from './views/My'
import Friend from './views/Friend'
import VueRouter from 'vue-router'
Vue.use(VueRouter) // VueRouter插件初始化

const router = new VueRouter({
  // routes 路由规则们
  // route  一条路由规则 { path: 路径, component: 组件 }
  routes: [
    { path: '/find', component: Find },
    { path: '/my', component: My },
    { path: '/friend', component: Friend },
  ]
})

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  router
}).$mount('#app')
```

### 路由模式

路由的路径看起来不自然, 有#，能否切成真正路径形式?

- hash路由(默认)        例如:  http://localhost:8080/#/home
- history路由(常用)     例如: http://localhost:8080/home   (以后上线需要服务器端支持，开发环境webpack给规避掉了history模式的问题)

**语法**

```js
const router = new VueRouter({
    mode:'histroy', //默认是hash
    routes:[]
})
```



### 组件存放目录

.vue文件分为2类，都是 **.vue文件（本质无区别）**

- 页面组件 （配置路由规则时使用的组件）放在views中
- 复用组件（多个组件中都使用到的组件）放在compoents中

## 声明式导航

**概念**

如果使用a标签进行跳转的话，需要给当前跳转的导航加样式，同时要移除上一个a标签的样式，太麻烦！！！

vue-router 提供了一个全局组件 router-link (取代 a 标签)

- **能跳转**，配置 to 属性指定路径(**必须**) 。本质还是 a 标签 ，**to 无需 #**
- **能高亮**，默认就会提供**高亮类名**，可以直接设置高亮样式

**语法：** <router-link to="path的值">发现音乐</router-link>

```vue
  <div>
    <div class="footer_wrap">
      <router-link to="/find">发现音乐</router-link>
      <router-link to="/my">我的音乐</router-link>
      <router-link to="/friend">朋友</router-link>
    </div>
    <div class="top">
      <!-- 路由出口 → 匹配的组件所展示的位置 -->
      <router-view></router-view>
    </div>
  </div>
```

**意义**

使用router-link跳转后，我们发现。当前点击的链接默认加了两个class的值 `router-link-exact-active`和`router-link-active`

我们可以给其中一个class属性添加高亮样式即可实现功能



**它们的区别**

1.router-link-active**模糊匹配（用的多）**

to="/my"  可以匹配 /my    /my/a    /my/b    ....  

只要是以/my开头的路径 都可以和 to="/my"匹配到

2.router-link-exact-active**精确匹配**

to="/my" 仅可以匹配  /my

## 路由传参

### 查询参数传参

在跳转路由时，进行传参。比如：现在我们在搜索页点击了热门搜索链接，跳转到详情页，**需要把点击的链接的内容带到详情页**，改怎么办呢？

**用法**

- 如何传参？

  <router-link to="/path?参数名=值"></router-link>

- 如何接受参数

  固定用法：在跳转到的页面 **$router.query.参数名**

### 动态路由传参

- 配置动态路由

  > 动态路由后面的参数可以随便起名，但要有语义

  ```js
  const router = new VueRouter({
    routes: [
      ...,
      { 
        path: '/search/:words', 
        component: Search 
      }
    ]
  })
  ```

- 配置导航链接

  to="/path/参数具体值"

- 对应页面组件**接受参数**

  $route.**params**.参数名

  > params后面的参数名要和动态路由配置的参数保持一致

**注意：**

**动态路由传参必须要传参数**。如果不传参数，也希望匹配，可以加个可选符"？"

### 两种传参方式比较

1. 查询参数传参  (比较适合传**多个参数**) 

   1. 跳转：to="/path?参数名=值&参数名2=值"
   2. 获取：$route.query.参数名

2. 动态路由传参 (**优雅简洁**，传单个参数比较方便)

   1. 配置动态路由：path: "/path/:参数名" 
   2. 跳转：to="/path/参数值"
   3. 获取：$route.params.参数名 

   注意：动态路由也可以传多个参数，但一般只传一个

## 重定向

**重定向** → 匹配 / 后, 强制跳转 /home 路径

```js
{ path: 匹配路径, redirect: 重定向到的路径 },
比如：
{ path:'/' ,redirect:'/home' }
```

## 通配符匹配

path: "*"   (任意路径) – 前面不匹配就命中最后这个

当路径找不到匹配时，给个提示页面

```js
import NotFind from '@/views/NotFind'

const router = new VueRouter({
  routes: [
    ...
    { path: '*', component: NotFind } //最后一个
  ]
})
```

## 两种路由跳转方式

如果我们的路由跳转不是通过链接跳转，而是主动跳转有两种方式。

### path路径跳转

特点：简易方便

```js
//简单写法
this.$router.push('路由路径')

//完整写法
this.$router.push({
  path: '路由路径'
})
```



### name命名跳转

特点：适合 path 路径长的场景

语法：

- 路由规则，必须配置name配置项

  ```js
  { name: '路由名', path: '/path/xxx', component: XXX },
  ```

- 通过name来进行跳转

  ```js
  this.$router.push({
    name: '路由名'
  })
  ```

### 传参

#### **path路径跳转传参（query传参）**

```js
//简单写法
this.$router.push('/路径?参数名1=参数值1&参数2=参数值2')
//完整写法
this.$router.push({
  path: '/路径',
  query: {
    参数名1: '参数值1',
    参数名2: '参数值2'
  }
})
```

接受参数的方式依然是：$route.query.参数名

#### path路径跳转传参（动态路由传参）

```js
//简单写法
this.$router.push('/路径/参数值')
//完整写法
this.$router.push({
  path: '/路径/参数值'
})
```

接受参数的方式依然是：$route.params.参数值

**注意：**path不能配合params使用

## 缓存组件

### 重要

**问题**

当路由被**跳转**后，原来所看到的组件就**被销毁**了（会执行组件内的beforeDestroy和destroyed生命周期钩子），**重新返回**后组件又被**重新创建**了（会执行组件内的beforeCreate,created,beforeMount,Mounted生命周期钩子），**所以数据被加载了**

**解决方案**

利用keep-alive把原来的组件给缓存下来

### keep-alive使用

#### 概念

keep-alive 是 Vue 的内置组件，当它包裹动态组件时，**会缓存不活动的组件实例，而不是销毁**它们。

keep-alive 是一个抽象组件：它自身不会渲染成一个 DOM 元素，也不会出现在父组件中。

**优点：**

在组件切换过程中把切换出去的组件保留在内存中，防止重复渲染DOM，

减少加载时间及性能消耗，提高用户体验性。

```vue
<template>
  <div class="h5-wrapper">
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
  </div>
</template>
```

#### keep-alive的三个属性

① include  ： 组件名数组，只有匹配的组件**会被缓存**

② exclude ： 组件名数组，任何匹配的组件都**不会被缓存**

③ max       ： 最多可以**缓存多少**组件实例

```vue
<template>
  <div class="h5-wrapper">
    <keep-alive :include="['LayoutPage']">
      <router-view></router-view>
    </keep-alive>
  </div>
</template>
```

#### 额外的两个生命周期钩子

**keep-alive的使用会触发两个生命周期函数**

**activated** 当组件被激活（使用）的时候触发 →  进入这个页面的时候触发

**deactivated** 当组件不被使用的时候触发      →  离开这个页面的时候触发

组件**缓存后**就**不会执行**组件的**created, mounted, destroyed** 等钩子了

所以其提供了**actived 和deactived**钩子，帮我们实现业务需求。

# vuex

## 概念

多组件共享数据的插件

## 基本使用

### 初始化

1.安装

```
yarn add vuex@3 或者 npm i vuex@3
```

2.新建 `store/index.js` 专门存放 vuex

3.创建仓库 `store/index.js` 

```js
// 导入 vue
import Vue from 'vue'
// 导入 vuex
import Vuex from 'vuex'
// vuex也是vue的插件, 需要use一下, 进行插件的安装初始化
Vue.use(Vuex)

// 创建仓库 store
const store = new Vuex.Store()

// 导出仓库
export default store
```

4.在 main.js 中导入挂载到 Vue 实例上

```js
import Vue from 'vue'
import App from './App.vue'
import store from './store'

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
  store
}).$mount('#app')
```

### 提供数据

State提供唯一的公共数据源，所有共享的数据都要统一放到Store中的State中存储。

打开项目中的store.js文件，在state对象中可以添加我们要共享的数据。

```js
// 创建仓库 store
const store = new Vuex.Store({
  // state 状态, 即数据, 类似于vue组件中的data,
  // 区别：
  // 1.data 是组件自己的数据, 
  // 2.state 中的数据整个vue项目的组件都能访问到
  state: {
    count: 101
  }
})
```

### 访问数据

```js
获取 store：
 1.Vue模板中获取 this.$store
 2.js文件中获取 import 导入 store


模板中：     {{ $store.state.xxx }}
组件逻辑中：  this.$store.state.xxx
JS模块中：   store.state.xxx
```



## mapstate使用

1.第一步：导入mapState (mapState是vuex中的一个函数)

```js
import { mapState } from 'vuex'
```

2.第二步：采用数组形式引入state属性

```js
mapState(['count']) 
```

> 上面代码的最终得到的是 **类似于**

```js
count () {
    return this.$store.state.count
}
```

3.第三步：利用**展开运算符**将导出的状态映射给计算属性，将store数据转换为计算属性

```js
  computed: {
    ...mapState(['count'])
  }
```

```vue
 <div> state的数据：{{ count }}</div>
```

## mutations

### 定义

用于修改存储的数据

```js
const store  = new Vuex.Store({
  state: {
    count: 0
  },
  // 定义mutations
  mutations: {
    addCount (state) {
      state.count += 1
    },
    
    addCountN (state, n) {
      state.count += n
    }, 
      //参数只能是一个, 如果有多个参数要传, 可以传递一个对象
    addCountObj (state, obj) {
      state.count += obj.n
	}
  }
  
})
```

### 组件中调用mutation

```js
this.$store.commit('addCount')
//带参数
this.$store.commit('addCount', 参数)
```



## actions

> state是存放数据的，mutations是同步更新数据 (便于监测数据的变化, 更新视图等, 方便于调试工具查看变化)，
>
> actions则负责进行异步操作

## vuex分模块

### 使用案例

定义两个模块   **user** 和  **setting**

user中管理用户的信息状态  userInfo  `modules/user.js`

```jsx
const state = {
  userInfo: {
    name: 'zs',
    age: 18
  }
}

const mutations = {}

const actions = {}

const getters = {}

export default {
  state,
  mutations,
  actions,
  getters
}

```

setting中管理项目应用的  主题色 theme，描述 desc， `modules/setting.js`

```jsx
const state = {
  theme: 'dark'
  desc: '描述真呀真不错'
}

const mutations = {}

const actions = {}

const getters = {}

export default {
  state,
  mutations,
  actions,
  getters
}
```

在`store/index.js`文件中的modules配置项中，注册这两个模块

```js
import user from './modules/user'
import setting from './modules/setting'

const store = new Vuex.Store({
    modules:{
        user,
        setting
    }
})
```

使用模块中的数据,  可以直接通过模块名访问 `$store.state.模块名.xxx`  =>  `$store.state.setting.desc`

使用模块中的mutations **$store.commit('模块名/xxx ',  额外参数)**

# 工具插件

vue.js Devtools

安装之后可以F12后看到多一个Vue的调试面板

![68188948344](D:\E\学习\Go\笔记\技术栈\前端\assets\1681889483446.png)

# VueCli 自定义创建项目

1.安装脚手架 (已安装)

```
npm i @vue/cli -g
```

2.创建项目

```
vue create hm-exp-mobile
```

+ 选项

```js
Vue CLI v5.0.8
? Please pick a preset:
  Default ([Vue 3] babel, eslint)
  Default ([Vue 2] babel, eslint)
> Manually select features     选自定义
```

+ 手动选择功能

![68294185617](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941856172.png)

+ 选择vue的版本

```jsx
  3.x
> 2.x
```

+ 是否使用history模式

![image-20201025150602129](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941888453.png)

+ 选择css预处理

![image-20220629175133593](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941900018.png)

+ 选择eslint的风格 （eslint 代码规范的检验工具，检验代码是否符合规范）
+ 比如：const age = 18;   =>  报错！多加了分号！后面有工具，一保存，全部格式化成最规范的样子

![68294191856](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941918562.png)

+ 选择校验的时机 （直接回车）

![68294193579](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941935794.png)

+ 选择配置文件的生成方式 （直接回车）

![68294194798](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941947985.png)

- 是否保存预设，下次直接使用？  =>   不保存，输入 N

![68294196155](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941961551.png)

+ 等待安装，项目初始化完成

![68294197476](D:\E\学习\Go\笔记\技术栈\前端\assets\1682941974763.png)

+ 启动项目

```
npm run serve
```



## 一个新的项目需要安装的东西

## anxios

```
npm install axios
```

## less-loader

less语法支持css嵌套

```
npm install less less-loader --save-dev
```

## rooter

```
npm install vue-router@3
```

## vant

```
npm i vant@latest-v2 -S
```

## vuex

```
npm i vuex@3
```

