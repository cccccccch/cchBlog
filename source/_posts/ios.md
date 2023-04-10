---
layout:
  - layout
title: ios
date: 2023-04-07 17:14:33
tags:
---
##  JavaScript复制文本至剪切板 兼容ios
###   input等  输入框复制
```c
  <div >
        <input id="text" value:'复制内容测试' />
        <button @click="copyToClipboard">复制</button>
    </div>copyToClipboard(){
        const input = document.querySelector('#text');
        input.select();
        // navigator.clipboard.writeText(val.target.innerText)
        document.execCommand('copy')?console.log('ok'):console.log('复制失败')
        document.execCommand("unselect", "false", null)
    },

```
###   其它地方复制

```c
    <div >
        <span id="text">复制内容测试</span>
        <button @click="copyToClipboard">复制</button>
    </div>

```
####  我们可能需要从`<div>`中复制内容我们可能需要从一个 <div> 中复制内容execCommand() 方法的定义中提到，它只能操作可编辑区域所以没有的话，我们 可以拥有这个是带注释 不需要就复制第二个
```c
   copyToClipboard(){
        //获取复制目标标签
        const txt = document.querySelector('#text')
        //由于目标标签可能非input 则创建一个input并加入当前body
        const input = document.createElement('input');
        //设置input框只读 防止ios输入框弹起
        input.setAttribute('readonly', 'readonly');
        document.body.appendChild(input);
        // 文本内容
        input.setAttribute('value', txt.innerText);
        // 解决ios选中区域缺失 若length依然未获取到全部内容 则setSelectionRange(0, 9999)
        input.setSelectionRange(0, input.value.length);
         // 选中区域
        input.select();
        // navigator.clipboard.writeText()此方法存在安全域问题
        // 某些浏览器禁用了非安全域的 navigator.clipboard 对象，这时候navigator.clipboard不可用，就需要我们使用已弃用的document.execCommand(‘copy’)
        // navigator.clipboard.writeText(val.target.innerText)
        document.execCommand('copy')?console.log('ok'):console.log('复制失败')
        //不喜欢三元表达可以使用if判断
        // if(document.execCommand('copy')){
        //     // document.execCommand('copy')
        //     
        // }else{
        //     
        // }
        //清除当前选中区的选中状态
        document.execCommand("unselect", "false", null)
        // 清除创建的input
        document.body.removeChild(input)
    },
```

```c
copyToClipboard(){
        //获取复制目标标签
        const txt = document.querySelector('#text')
        //由于目标标签可能非input
        const input = document.createElement('input');
        document.body.appendChild(input);
        input.setAttribute('value', txt.innerText);
        input.setSelectionRange(0, input.value.length);
        input.select();
        // navigator.clipboard.writeText(val.target.innerText)
        document.execCommand('copy')?console.log('ok'):console.log('复制失败')
        document.execCommand("unselect", "false", null)
        document.body.removeChild(input)
    },
```