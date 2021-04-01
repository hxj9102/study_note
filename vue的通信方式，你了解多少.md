# vue的通信方式，你了解多少

## 1 父子组件通信

### props/$emit

父组件可以通过v-bind向子组件传递数据。

子组件可以通过props接收父组件的数据，并且通过$emit向父组件触发事件。

```javascript
// 父组件
var vm = new Vue({
    template: `<chid :word="word"></child>`,
    data () {
        return {
            word: 'hello word'
        }
    }
    components: {
        Child
    }
})

// 子组件
var Child = {
	prop: ['word']
}
```



### provide / inject

父组件可以通过provide 向子组件或孙组件传递数据。

子组件可以通过inject接收父组件的数据。

```javascript
// 父组件
var vm = new Vue({
	provide: {
        word: 'hello word'
    },
    components: {
        Child
    }
})

// 子组件
var Child = {
    inject: ['word'],
    components: {
        GrandChild
    }
}

// 孙组件
var GrandChild = {
    inject: ['word'],
    created () {
        console.log(this.word) // hello word
    }
}
```

官方：`provide` 和 `inject` 主要在开发高阶插件/组件库时使用。并不推荐用于普通应用程序代码中。

在一些组件库的源码中可以看到使用provide和inject，例如vant。



### $parent/$children

通过vm.$parent获取父实例

通过vm.$children获取子实例列表

``` javascript
// 父组件
var vm = new Vue({
	mounted () {
        window.vm = this
        console.log(this.$children) // [Child1, Child2] 子组件实例列表
    },
    components: {
        Child1,
        Child2
    }
})

// 子组件1
var Child1 = {
    mounted () {
        console.log(this.$parent === window.vm) // true
    }
}
// 子组件2
var Child2 = {
    mounted () {
        console.log(this.$parent === window.vm) // true
    }
}
```



### ref/$refs

父组件为子组件定义ref，并且可以通过$refs获取子组件实例

``` javascript
// 父组件
var vm = new Vue({
    template: `<chid ref="child"></child>`,
    components: {
        Child
    },
    mounted () {
        this.$refs['child'].showMe() // see me
    }
})

// 子组件
var Child = {
    methods () {
        showMe () {
            console.log('see me')
        }
    }
}
```



### $attrs/$listeners

包含了父作用域中不作为 prop 被识别 (且获取) 的 attribute 绑定 (`class` 和 `style` 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (`class` 和 `style` 除外)，并且可以通过 `v-bind="$attrs"` 传入内部组件——在创建高级别的组件时非常有用。

```javascript
// 父组件
var vm = new Vue({
    template: `<chid @alertMe="alertMe"></child>`,
    data () {
        return {
            word: 'hello word',
            message: 'hello message'
        }  
    },
    components: {
        Child
    },
    methods: {
        alertMe (msg) {
            alert(msg)
        }
    }
})

// 子组件
var Child = {
    template: `<grand-chid v-bind="$attrs" v-on="$listeners"></grand-chid>`,
    props: ['word'],
    components: {
        GrandChild
    }
}

// 孙组件
var GrandChild = {
    template: `<button @click="alertMe"></button>`,
    props: ['message', 'word'],
    created () {
        console.log(this.message) // hello message
        // 由于子组件props拦截了word，word不会再往下传
        console.log(this.word) // undefinded
    },
    methods: {
        // 点击按钮后将事件传递给vm的alertMe
        alertMe () {
            this.$emit('alertMe', 'hi, see you')
        }
    }
}

```



###  sync/update

 (2.3.0+) 语法糖，会扩展成一个更新父组件绑定值的 `v-on` 侦听器。

```javascript
// 父组件
var vm = new Vue({
    template: `<chid :word.sync="word"></child>`,
    data () {
        return {
            word: 'hello word'
        }  
    },
    components: {
        Child
    }
})

// 子组件
var Child = {
    create () {
        this.$emit('update:word', 'hello message') // 触发之后父组件的word变为hello message
    }
}

```



## 2 非父子组件通信

### vuex



### eventBus

```javascript
// 父组件
var eventBus = new Vue()

var ChildA = {
    created () {
        eventBus.$on('showMe', msg => {
            console.log(msg)
        }
    }
}
    
var ChildB = {
    mounted () {
        eventBus.$emit('showMe') // 将触发ChildA监听的showMe
    }
}
```