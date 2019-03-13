---
title: vue漫漫采坑路
date: 2018-03-06 16:54:09
tags: vue
categories: [技术, vue]
---

**注：**在学习及日常工作中遇到的vue问题--个人的学习笔记（写写东西加深理解  更多的是给自己看的，里面可能会有些理解上的错误~~）

### 事件修饰符

```javascript
Vue中事件修饰符
事件的执行阶段：捕获阶段（父元素） --> 事件源阶段（被点击的内部子元素） --> 事件冒泡阶段
1. stop	  阻止冒泡
	如：
		<div id="inner" @click="innerClick">
          	 <input type="button" value="按钮" @click.stop="btnClick">
         </div>
        
2. prevent   默认事件
	如：
	   <a href="http://www.baidu.com" @click.prevent="linkClick">阻止a元素跳转的默认行为</a>

3. capture   事件监听器使用事件捕获模式
	事件捕获模式 和 事件冒泡模式  只能二选一
	如：
	   鼠标点击input元素, 先触发外层元素的innerclick事件
	    <div id="inner" @click.capture="innerClick">
          	 <input type="button" value="按钮" @click="btnClick">
         </div>

4. self   当事件在该元素本身触发时才会触发自生绑定的事件

5. once   只触发一次
	如：第一次点击时才会被触发
	    <input type="button" value="按钮" @click.once="btnClick">
```

### 过滤器

```javascript
1. 全局过滤器
	Vue.filter('过滤器名称', function() {})

2. 私有过滤器
	var vm = new Vue({
      data: {},
      filters: {
        filterA() {}
      }
	});
	vm.$mount('app')
3. 参数问题
	UI界面上 ---- {{ message | filterA('arg1', arg2) }}
	// filterA 被定义为接收三个参数的过滤器函数。其中 message 的值作为第一个参数，普通字符串 'arg1' 作为第二个参数，表达式 arg2 的值作为第三个参数
```

### vue实例--设置渲染区域的两种写法

```javascript
// 创建 Vue 实例，得到 ViewModel   vm
1.   var vm = new Vue({
        el: '#app'
      });

2.   var vm2 = new Vue()
   	 vm2.$mount('#app2')   // 按需挂载即手动挂载   推荐使用
// 注：el和$mount并没有本质上的不同。但$mount挂载前可进行些其他操作、判断等
```

### 自定义指令

```javascript
// v-focus='value'
1. 全局指令
    Vue.directive('指令名称（如focus）', {
      bind: function(el, binging) {
          // 当解析到元素绑定的自定义指令时调用
          el.style.color = 'red'
          el.focus()
          binging.name   // 指令名称 不包含 v-
          binging.value	 // 指令的值 value	
      },
      inserted: function(el) {
          // 当指令绑定的元素被到插入到其父元素时调用
          el.focus()
      }
    })
2. 私有指令
	var vm = new Vue({
        data: {},
        directives: {
          	focus: {
              	bind: {...},
                inserted: {...}
          	}
        }
	});
/* 注：
  1. 自定义指令可以用来进行dom操作
  2. 一定要注意调用时间
  3. 样式设定不需要考虑时间 因为设定了就会存在	  el.style.color
  4. 解析到时如果元素还没渲染到页面，事件不会生效    el.focus
```

###  按键修饰符

```javascript
使用：v-on:keyup.enter
.enter .tab .delete (“删除”和“退格”键) .esc .space 等.
```

### axios

注：单独写篇博客介绍吧

### 组件创建/使用

```javascript
注册全局组件： 
1. Vue.component('组件名', 组件的构造函数)
  如： const com1 = Vue.extend({
            template: '<h1>创建全局组件----第一种方式</h1>'
       })
       Vue.component('com1', com1)
  	   console.dir(com1);    
  // 可以看出创建的com1为构造函数 而非组件   它上面的原型属性prototype指向Vue$3 {constructor: ƒ}

2. Vue.component('com2', {
  		template: '<h1>创建全局组件----第二种方式</h1>'
   })

3. <template id='tmpl-id'>
  		<h1>创建全局组件----第三种方式</h1>
   </template>
   Vue.component('com3', {
  		template: '#tmpl-id'
   })
注：
	1. 通过上面的Vue.extend可以看出组件是特殊Vue实例
	2. 使用template方式创建的全局组件 UI模板元素必须放在在注册组件前, 
	3. 模板元素不能放入Vue实例控制的app容器中，因为会被当成组件渲染到界面上
	4. 注册全局组件必须在Vue实例之前（通常指vm之前），是为了保证注册的全局组件在任意的实例中都可以使用
	
注册私有组件
let vm = new Vue({
    data: {},
    components: {
        'com4': {
            template: '<h1>创建私有组件----第一种方式</h1>'
        }
    }
})


定义组件的私有data methods
Vue.component('com2', {
	template: '<h1>定义组件的私有数据----{{ msg }}</h1>',
	data: function() {
      	return {
          	msg: '组件私有数据msg'
      	}
	}
})
注：
	1. 组件私有数据data必须是function类型，且必须return一个对象
	2. 每次引用组件data方法return出的对象都是一个新的对象，保证每次创建的组件数据私有化

使用component标签结合is属性切换多个组件（很实用哦）
	<div>
		<component :is="'组件名'"></component>
		// is属性用来控制要渲染的组件
	</div>
	<script>
		Vue.component('com1', {
  			template: '<h1>组件1</h1>'
        })

        Vue.component('com2', {
            template: '<h1>组件2</h1>'
        })

        Vue.component('com3', {
            template: '<h1>组件3</h1>'
        })
	</script>
```

### 使用hash值模拟路由实现

```javascript
// hash # 不会刷新页面以及发送HTTP请求，只是实现客户端页面的定位
// hash # 可以修改浏览器的历史纪录
window.onhashchange()  	// 监听页面的hash值的改变
location.hash()		    // 获取当前页面hash值
```

### 路由

```javascript
1. const login = {
	template: '<h1>路由----登录</h1>'
}

2. const router = new VueRouter({
  	routes: [ // 路由规则
  		{path: 'hash路径', component: '组件物理地址'},
  		{path: '/login', component: login}
  	]
})

3. new Vue({
  	data: {},
  	methods: {}
  	router: router,
})
4. 	<div>
		<router-link to='路由路径'></router-link>		// 负责路由跳转，类似于a链接
        <router-view></router-view>					   // 负责UI界面的呈现
	</div>
	
5. 设定默认的根路由
	{path: '/', redirect: '重定向到某路由'}

6. 路由传参
	a. 	query传参（即问号传参）
		<router-link to='/login?id=536'></router-link>
		this.$route.query		// 获取url问号后面传递的参数
		注：query传参（问号传参）不需要修改对应的路由规则   对比params传参
		
	b.	params传参
		<router-link to='/login/536/lien'></router-link>
		{path: '/login/:id/:name', component: login}
		this.$route.params
     区别： query相当于get请求，页面跳转的时候，可以在地址栏看到请求参数，而params相当于post请求，参数不会再地址栏中显示
		
	c.	props 获取参数
		{path: '/login/:id/:name', component: login, props: true}
		注：props: true这种获取参数的形式好处是 不需要在使用'this.$route.'的形式，直接
			在组件中使用 props: ['id', 'name']接收参数即可
7. 子路由
	{
		path: '/account', 
		component: account, 
		children: [
          	{path: 'login', component: login}
          	// 子路由规则path属性值前不需要再加 '/'
		]
	}
	
8. 	编程式导航--实现路由跳转   
	标签式 <router-link to=''>
	<button @click='goRouter'></button>
	goRouter() {
      	this.$router.push('/login/' + this.id)
	}
	this.$router.go(-1) 后退，用来实现面包屑导航
注：
	this.$route 是用来获取参数的
	this.$router 是用来实现编程是路由跳转的，不要搞混淆了
```

##### watch、computed属性使用

```javascript
UI界面：name = xing + ming
new Vue({
  	data: {
      	xing: '',
      	ming: ''
      	name: ''
  	},
  	watch: {
  		// watch 应用场景：监听虚拟数据如监听url的改变（单个数据）
      	'ming': function(newVal, oldVal) {
      		this.name = this.xing + newVal 
      	},
      	'$route.path': function(newVal, oldVal) {}
  	}
  	computed: {
      	'name': function() {
          	return: this.xing + this.ming
          	// 'name'即为计算属性
          	// 只要计算属性的function中，任何依赖的数据发生变化，就会触发重新求值
          	// 计算属性的值，如果未发生变化，则会被缓存供之后使用
      	}
  	}
})

// get set使用
	computed: {
      	'name': {
          	get() {
              	// 获取name的值触发
              	return: this.xing + this.ming
          	},
          	set(val) {
              	// name的值自身发生改变，触发
              	const arr = val.split('-')
              	this.xing = arr[0] || ''
              	this.ming = arr[1] || ''
          	}
      	}
	}
```

##### Vuex使用

```javascript
// 全局环境实现数据共享
1. 导入并注册 Vue.use(Vuex)
2. 创建
	store = new Vuex.Store({
  		state: {
          	// state用来存储数据
          	count: 0
  		},
  		mutations: {
  			// 专门用来修改state中的数据
  			add(state, arg) {
              	// mutations中的方法第一个参数永远都是 state
              	// 自定义参数只能有一个，多个参数可以使用数组或对象
              	state.count += arg
  			}
  		},
  		getters: {
          	// getter 类似于计算属性----包装数据类似过滤器----如添加前缀（不会修改原数据）
          	nameInfo(state) {
          		let xing = 'li', ming = 'en'
          		state.name = xing + ming
              	return '我的名字是：' + state.name
          	}
  		},
  		actions: {
          	// actions类似于mutations
          	// actions里进行异步操作、mutations里进行同步操作
          	// mutations中的异步操作，可以正常执行，但是vue-tools调试插件无法监听数据变化
          	newAdd(context) {
          	// actions中的方法，可以提交对应的mutations方法
          	// action中不能直接操作state，如果想要修改state，只能在actions中，提交对应的					mutations方法
              context.commit('add')
          	}
  		}
	})
	new Vue({
      	el: '#app',
      	store: store,	// 挂载到Vue实例上
      	methods: {
          	increment() {
          	    //直接使用this.$store.state修改数据
              	// this.$store.state.count++	强烈不推荐
              	
              	// this.store.commit可以调用mutations中的方法修改数据----推荐使用
              	let arg = 2
              	this.store.commit('add', arg)
          	}
      	}
	})
3. UI界面
	3.1	state----UI层使用方式
		3.1.1	<h1>数量：{{ $store.state.count}}</h1> ---- 较为麻烦
		3.1.2	mapState函数获取数据----返回值一个对象
                 computed: {// 定义计算属性区域
                     ...mapSate(['count'])
                 }
	3.2	mutations----UI层使用方式
		3.2.1	this.store.commit('add', '可选实参')
         3.2.2   mapMutations----映射mutations中的方法到methods中
                 methods: {// 定义计算属性区域
                     ...mapMutations(['add'])
                 }
	3.3 getters----UI层使用方式
		3.3.1	this.$store.getters.nameInfo
		3.3.2	mapGetters----映射到计算属性中
                 computed: {// 定义计算属性区域
                     ...mapGetters(['nameInfo'])
                 }
	3.4 actions----异步操作
		3.4.1	mapActions---映射mutations中的方法到methods中
                 methods: {// 定义计算属性区域
                     ...mapActions(['newAdd'])
                 }
		3.4.2	this.store.dispatch('newAdd')
```

