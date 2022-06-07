---
title: vue2最佳实践
date: 2022-06-06 00:00:00
tags:
- vue
---


监听的是同源

[https://cn.vuejs.org/v2/api/#vm-watch](https://cn.vuejs.org/v2/api/#vm-watch)

注意：在变异 (不是替换) 对象或数组时，旧值将与新值相同，因为它们的引用指向同一个对象/数组。Vue 不会保留变异之前值的副本。

观察 Vue 实例变化的一个表达式或计算属性函数。回调函数得到的参数为新值和旧值。表达式只接受监督的键路径。对于更复杂的表达式，用一个函数取代

```

data:{
    haoroomsObj:{
        haoroomstestinner:{
            a: '我是haorooms资源库',
            b: '我是haorooms博客'
        }
    }
},
watch: {
  haoroomsObj: {
    handler: (val, olVal) => {
      console.log('我变化了', val, olVal)
    },
    deep: true
  }
},
//函数包装后监听
data:{
    haoroomsObj:{
        haoroomstestinner:{
            a: '我是haorooms资源库',
            b: '我是haorooms博客'
        }
    }
},
computed: {
  newHaorooms() {
    return this.haoroomsObj
  }
watch: {
  newHaorooms: {
    handler: (val, olVal) => {
      console.log('我变化了', val, olVal)
    },
    deep: true
  }
},

```