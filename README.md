# Vue源码

## mustache

### 模板引擎

纯DOM法-->数组join法-->ES6的反引号法-->模板引擎

```html
<!-- 纯DOM -->
<script>
  var arr = [
    { "name": "jack", "age": 12, "sex": "男" }
  ]
  var list = document.getElementById('list')
  for(let i = 0; i < arr.length; i++) {
    let oLi = document.createElement('li');
    let hdDiv = document.createElement('div');
    hdDiv.className = 'hd';
    hdDiv.innerText = arr[i].name + '的基本信息';
    // ......
    oLi.appendChild(hdDiv);
    list.appendChild(oLi);
    // ......
  }
</script>
```

```html
<!-- 数组join法 -->
<script>
  for(let i = 0; i < arr.length; i++) {
    list.innerHTML += [
      '<li>',
      '	<div class="hd">' + arr[i].name + '的信息</div>',
      '	<div class="bd">',
      '		<p>姓名：' + arr[i].name + '</p>',
      '		<p>年龄：' + arr[i].age + '</p>',
      '		<p>性别：' + arr[i].sex + '</p>',
      '	</div>',
      '</li>'
    ].join('')
  }
</script>
```

```html
<!-- ES6的反引号法 -->
<script>
  for(let i = 0; i < arr.length; i++) {
    list.innerHTML += `
      <li>
      	<div class="hd">${arr[i].name}的信息</div>
				<div class="bd">
      		<p>姓名：${arr[i].name}</p>
      		<p>年龄：${arr[i].age}</p>
      		<p>性别：${arr[i].sex}</p>
				</div>
      </li>`
  }
</script>
```

```html
<!-- mustache -->
<script src="./mustache.js"></script>
<script>
    var data = {
        arr: [
            { "name": "jack", "age": 12, "sex": "男" }
        ]
    };
    var templateStr = `
    <ul>
      {{#arr}}
        <li>
          <div class="hd">{{name}}的基本信息</div>
          <div class="bd">
            <p>姓名：{{name}}</p>
            <p>年龄：{{age}}</p>
            <p>性别：{{sex}}</p>
          </div>
        </li>
      {{/arr}}
    </ul>`;
    var domStr = Mustache.render(templateStr, data)
    var container = document.getElementById('container');
  	container.innerHTML = domStr;
</script>
```

### 正则表达式

```js
var templateStr = '<h1>我买了一个{{thing}},价值{{price}}</h1>'
var data = {
  thing: "PC",
  price: 3000
}
function render(templateStr, data) {
  return templateStr..replace(/\{\{(\w+)\}\}/g, function(findStr, $1) {
    return data[$1];
  });
}
var result = render(templateStr, data)
```

### mustache库

<img src="README.assets/image-20210706155736416.png" alt="image-20210706155736416" style="zoom: 25%;" />

## 虚拟DOM和diff算法

1. 虚拟DOM被渲染函数（h函数）产生
2. diff算法原理
3. 虚拟DOM通过diff编程真正的DOM

### snabbdom

```js
// 虚拟DOM的属性
{
  children: undefined // 子节点，undefined表示没有
  data: { // 属性样式
    props: {
      href: "http://www.baidu.com"
    }
  }
  elm: undefined // 该元素对应的真正的DOM节点，undefined表示还没有上树
  key: undefined // 节点唯一标识
  sel: "a" // selector选择器，节点类型
  text: "百度一下" // 文字
}
```

### diff

**只有是同一个虚拟节点**（选择器相同且key相同才是同一个），才进行精细化比较，否则就是暴力删除旧的、插入新的。

**只进行同层比较**，不会进行跨层比较。

<img src="README.assets/image-20210708111345484.png" alt="image-20210708111345484" style="zoom:33%;" />

**patch函数的比较过程**

<img src="README.assets/image-20210708154902206.png" alt="image-20210708154902206" style="zoom:50%;" />

**diff算法的比较顺序**

1. 新前、旧前
2. 新后、旧后
3. 新后、旧前
4. 新前、旧后
5. 如果都未命中，循环查找