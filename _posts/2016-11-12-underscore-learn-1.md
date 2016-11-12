---
title:  "underscore源码解读-1"
date:   2016-11-12 10:18:00
---

![food](/assets/images/food.jpg)



## Default Value

初始值是状态中的value。如果要取数据，可直接使用 var inputValue = this.state.value。

```js
 render: function() {
    return <input type="text" defaultValue={this.state.value}/>;
 }
```

## bind复用

```js
handleChange:function(name,event){
    ......
}
onChagne={this.handleChange.bind(this,'input')}
```