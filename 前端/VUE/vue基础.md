---
typora-copy-images-to: images
typora-root-url: ./
---

#### 

```html
<style>
    [v-cloak]{
        display:none;
    }
</style>

<!-- 使用v-cloak解决插值表达式闪烁问题 -->
<div v-cloak>{{msg}}</div>

<!-- 使用v-text没有闪烁问题还会覆盖原本内容 -->
<div v-text = "msg">============</div>

<!-- 使用v-html显示HTML内容 -->
<div v-html = "msg"></div>

<!-- v-bind绑定属性指令，设置内容 -->
<input v-bind:title="mytitle"></input>
<input type="button" value="按钮" :title="mytitle"></input>

<!-- v-on 事件触发 mathods:{shoe:function(){}}-->
<input type="button" value="按钮" v-on:click="show"> 
<input type="button" value="按钮" @click="show"> 
```

