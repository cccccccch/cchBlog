---
title: 资源
date: 2020-02-10 22:07:08
type: "resources"
comments: false
---
## uni-app 上传图

``` bash
<template>
  <view 
    class="mk-upload" 
    :class="{'reverse': reverse}"
  >
    <!--进行flex占位-->
    <block v-if="imgList.length >= column && reverse">
      <block v-if="imgItemAppendLength === 0">
        <view
          class="mk-upload_item"
          :class="uploadItemColumn"
          v-for="(item, index) in (imgItemAppendLength + (column -1))"
        />
      </block>
      <block v-if="imgItemAppendLength === 1">
        <block v-if="column == 3">
          <view
            class="mk-upload_item"
            :class="uploadItemColumn"
            v-for="(item, index) in imgItemAppendLength"
          />
        </block>
        <block v-if="column == 4">
          <view
            class="mk-upload_item"
            :class="uploadItemColumn"
            v-for="(item, index) in (imgItemAppendLength + 1)"
          />
        </block>
        <block v-if="column == 5">
          <view
            class="mk-upload_item"
            :class="uploadItemColumn"
            v-for="(item, index) in (imgItemAppendLength + 2)"
          />
        </block>
      </block>
      <block v-if="imgItemAppendLength === 2">
        <block v-if="column == 4">
          <view
            class="mk-upload_item"
            :class="uploadItemColumn"
            v-for="(item, index) in (imgItemAppendLength - 1)"
          />
        </block>
        <block v-if="column == 5">
          <view
            class="mk-upload_item"
            :class="uploadItemColumn"
            v-for="(item, index) in imgItemAppendLength"
          />
        </block>
      </block>
      <block v-if="imgItemAppendLength === 3">
        <block v-if="column == 5">
          <view class="mk-upload_item" :class="uploadItemColumn"/>
        </block>
      </block>
    </block>
    <!--进行flex占位-->
    
    <view 
      class="mk-upload_item"
      :class="uploadItemColumn"
      v-for="(item, index) in imgList"
      :key="index"
    >
      <view 
        class="mk-upload_remove" 
        :class="deletePosition"
        @tap="onDeleteThis(index)"
        v-if="deleteShow"
      >
        <text>✕</text>
      </view>
      <image
        :src="item"
        class="mk-upload_item_img" 
        @tap="onPreviewImage(index)" 
        mode="aspectFill"
      />
    </view>
    <!-- 列表end -->
    
    
    <view
      class="mk-upload_item"
      :class="uploadItemColumn"
      v-if="controlShow && imgList.length < maxCount"
    >
      <view 
        class="mk-upload_add"
        @tap="onChooseImage"
        :style="{'background-color': bgcolor}"
        style="width: 60px;height: 90px; border: 2px solid #e8e8e8;"
      >+</view>
    </view>
    <!-- 上传按钮 -->
    
      
  </view>
</template>

<script>
  export default {
    //组件传参
    props: {
      addW:{
        type:Number,
      },
      addH:{
        type:Number,
      },
      //背景颜色
      bgcolor:{
        type:String,
        default:'#f1f1f1'
      },
      //图片数组
      imgList: {
        type: Array,
        default: function() {
          return []
        }
      },
      //行数量
      column: {
        type: [Number, String],
        default: 3
      },
      reverse: {
        type: Boolean,
        default: false
      },
      //是否显示上传按钮
      controlShow: {
        type: Boolean,
        default: true
      },
      //是否显示删除按钮
      deleteShow: {
        type: Boolean,
        default: true
      },
      //删除按钮位置
      deletePosition: {
        type: String,
        default: 'topRight'
      },
      //album 从相册选图，camera 使用相机
      sourceType: {
        type: Array,
        default: function() {
          return ['camera', 'album']
        }
      },
      //original 原图，compressed 压缩图
      sizeType: {
        type: Array,
        default: function() {
          return ['original', 'compressed']
        }
      },
      //最多可以选择的图片张
      maxChooseCount: {
        type: Number,
        default: 5
      },
      //最大条数
      maxCount: {
        type: Number,
        default: 5
      }
    },
    data() {
      return {
      }
    },
    //计算属性
    computed: {
      uploadItemColumn(){
        return `mk-upload_item_column${this.column}`
      },
      imgItemAppendLength(){
        return this.imgList.length % this.column;
      }
    },
    //检测属性
    watch: {
    },
    //组件加载后
    created() {
    },
    methods: {
      //删除指定图片
      onDeleteThis(index){
        if(this.$listeners.onDeleteTake){
          this.$emit('onDeleteTake', index);
          return;
        }
        uni.showModal({
          title: '提示',
          content: '您确定删除吗？',
          success: (res)=> {
            if(res.confirm) {
              this.$emit('onDelete', index);
            }
          }
        });
      },
      //预览图片
      onPreviewImage(index) {
        if(this.$listeners.onPreviewTake){
          this.$emit('onPreviewTake', index);
          return;
        }
        const imgList = this.imgList;
        const isReverse = this.reverse;
        const currentIndex = isReverse ? (imgList.length - index) - 1 : index
        const imgLists = isReverse ? imgList.reverse() : imgList;
        uni.previewImage({
          current: currentIndex,
          urls: imgLists
        });
      },
      //选择图片
      onChooseImage(){
        uni.chooseImage({
          sizeType: this.sizeType,
          sourceType: this.sourceType,
          count: this.maxChooseCount,
          success: (res) => {
            this.$emit('onChoose', res);
          }
        });
      }
    }
  }
</script> 

<style lang="less" scoped>
  .mk-upload{
    display: flex;
    flex-wrap: wrap;
    box-sizing: border-box;
    // padding-left: var(--mk-upload_item_margin, 15px);
    padding-bottom: var(--mk-upload_item_margin, 15px);
    flex-direction: row;
    // justify-content: space-between;
    &.reverse{
      flex-direction: row-reverse;
      flex-wrap: wrap-reverse;
      justify-content: flex-end;
    }
    
    .mk-upload_item{
      position: relative;
      margin-top: var(--mk-upload_item_margin, 15px);
      // margin-right: var(--mk-upload_item_margin, 15px);
      margin-right: 20rpx;
      flex: 0 calc(33.3% - var(--mk-upload_item_margin, 15px));
      .mk-upload_remove{
        background-color: var(--mk-upload_remove_color, #D1372C);
        color: #fff;
        width: var(--mk-upload_remove_size, 24px);
        height: var(--mk-upload_remove_size, 24px);
        display: flex;
        justify-content: center;
        align-items: flex-start;
        opacity: 0.8;
        position: absolute;
        z-index: 2;
        cursor: pointer;
        box-sizing: border-box;
        text{
          transform: scale(0.8);
          position: absolute;
          top: -2upx;
          line-height: var(--mk-upload_remove_size, 24px);
        }
        &:active{
          opacity: 0.6;
        }
        &.topLeft{
          top: 0;
          left: 0;
          border-radius: 0 0 100% 0;
          text{
            left: 8upx;
          }
        }
        &.topRight{
          top: 0;
          right: 0;
          border-radius: 0 0 0 100%;
          text{
            left: 18upx;
          }
        }
        &.bottomLeft{
          bottom: 0;
          left: 0;
          border-radius: 0 100% 0 0;
          text{
            left: 8upx;
            top: 6upx;
          }
        }
        &.bottomRight{
          bottom: 0;
          right: 0;
          border-radius: 100% 0 0 0;
          text{
            top: 6upx;
            left: 16upx;
          }
        }
      }
      .mk-upload_add{
        display: flex;
        justify-content: center;
        align-items: center;
        background-color: var(--mk-upload_add_bgcolor, #f1f1f1);
        
        font-size: var(--mk-upload_add_size, 30px);
        height: var(--mk-upload_img_height, 100px);
        // width: 100%;
      }
      .mk-upload_item_img{
        display: block;
        // width: 100%;
        width: 100rpx;
        height: 100rpx;
        // height: var(--mk-upload_img_height, 100px);
      }
      &.mk-upload_item_column2{
        flex: 0 calc(50% - var(--mk-upload_item_margin, 15px));
      }
      &.mk-upload_item_column3{
        flex: 0 calc(33.3% - var(--mk-upload_item_margin, 15px));
      }
      &.mk-upload_item_column4{
        flex: 0 calc(25% - var(--mk-upload_item_margin, 15px));
        .mk-upload_item_img,
        .mk-upload_add{
          // height: var(--mk-upload_img_height, 70px);
          font-size: var(--mk-upload_add_size, 25px);
          width: 120rpx;
          height: 120rpx;
        }
      }
      &.mk-upload_item_column5{
        flex: 0 calc(20% - var(--mk-upload_item_margin, 15px));
        .mk-upload_item_img,
        .mk-upload_add{
          height: var(--mk-upload_img_height, 55px);
          font-size: var(--mk-upload_add_size, 25px);
        }
      }
      &.mk-upload_item_column4,
      &.mk-upload_item_column5{
        .mk-upload_remove{
          width: var(--mk-upload_remove_size, 20px);
          height: var(--mk-upload_remove_size, 20px);
          text{
            transform: scale(0.6);
          }
          &.topLeft{
            text{
              left: 6upx;
              top: -6upx;
            }
          }
          &.topRight{
            text{
              left: 10upx;
              top: -6upx;
            }
          }
          &.bottomLeft{
            text{
              left: 4upx;
              top: 0;
            }
          }
          &.bottomRight{
            text{
              left: 10upx;
              top: 0;
            }
          }
        }
      }
      
    }
  }
  .mk-upload_add {
    
  }
</style>

```
## bug调试
###  debugger

只要在代码中添加**debugger**，chrome在运行的时候会自动停在那里。还可以用条件语句把它包裹起来，这样就可以在需要的时候才执行它。

```JavaScript
if (thisThing) {
  debugger;
}
```

###  console功能

调试时console.log是最常用的命令之一，此外还有一些其他的使用的功能2.1 console.log在console.log()中，可以用%s设置字符串，%i设置数字，%c设置自定义样式，console.log()接受的两个参数，前者是描述性的语言，而第二个参数是与第一个参数位置对应的字符

```JavaScript
console.log(' %c %s %s %s', 'color: yellow; background-color: black;', '–', '测试信息', '–');

```

![](https://secure2.wostatic.cn/static/wdFTGvs6eKgXv3jHq7mGjX/image.png?auth_key=1680851093-vgEDDMDR2hP5JLh2ykPCbb-0-c872a773029e4c6b50e7b0c305621a89)

###  [console.table](https://www.zhihu.com/search?q=console.table&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":2603739492})()

很多的时候，你可能会有一堆对象需要查看。可以用**console.**[**log**](https://www.zhihu.com/search?q=log&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":2603739492})**把每一个对象都输出出来，也可以用**console.table语句直接把所有的对象都直接输出成为一个表格

![](https://secure2.wostatic.cn/static/43steyqp4u5tC9RHwJ1WDT/image.png?auth_key=1680851093-3FbXGVkFkxG5t6Z5gmpTRc-0-3d20973a7e3bd27fb5142f1a5203c1f8)

###  [console.time](https://www.zhihu.com/search?q=console.time&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":2603739492})()和console.timeEnd()

当想知道某些代码的执行时间的时候这个工具将会非常有用，特别是当你定位很耗时的循环的时候。

![](https://secure2.wostatic.cn/static/9JUYNCFi2Bh43LTmaRmZ7/image.png?auth_key=1680851093-auyhW4jkQkEZLYUDKPKbrw-0-3fc83a418c1ef3b45e05995b58f04e8a)

###  console.trace()

**console.trace()**会显示函数调用的完整的堆栈轨迹信息

```JavaScript
getTopN2(arr, n) {
  // sort参数返回值大于1，就交换位置
  console.trace('getTopN2');
  return [...arr].sort((a, b) => b - a).slice(0, n)
}
```

![](https://secure2.wostatic.cn/static/mbJbGoTCiH6tr6p2mVkuWC/image.png?auth_key=1680851093-dYRZi1viP6dLfasNZ8FPmz-0-14ba53f363d717823dfec58cffa3dcfd)

###  DOM节点变化时中断代码运行

在调试DOM节点时，可以在Chrome的Elements界面，右键点击某个元素，选择**Break on**选项，可以在子节点变化时中断、在元素属性变化时中断或者在节点被移除时中断代码运行

![](https://secure2.wostatic.cn/static/wJ2MdGwpKtRREPqj3CDFXu/image.png?auth_key=1680851093-fjbeSpjYdvH2ZwKPnrk5vx-0-2275fbaca2d88ab3ebe961a803696b52)

###  在控制台设置断点

###  快捷键

- **F8:** 跳转至下个断点
- **F10:** step over, 单步执行, 不进入函数
- **F11:** step into, 单步执行, 进入函数
- **shift + F11**: step out, 跳出函数

###  在代码上设置断点

对于每个已添加的断点都有两种状态：激活和禁用。刚添加的断点都是激活状态，禁用状态就是保留断点但临时取消该断点功能。

在Breakpoints列表中每个断点前面都有一个复选框，取消选中就将禁用该断点。断点位置的右键菜单中也可以禁用断点。也可以在右侧功能区上面Chrome断点设置钮临时禁用所有已添加的断点，再点一下恢复原状态。

###  逐步执行

![](https://secure2.wostatic.cn/static/ce4H72pygRe1JcWYSWWhmJ/image.png?auth_key=1680851093-j6nQ8k36heKkWwzt2fTfym-0-14af5114d6a41b0cbed1146aa1301382)

每点击一次，JS语句往后执行依据，F11

###  逐过程执行

![](https://secure2.wostatic.cn/static/uYxi2C5dqmxbkzwJ5gNDBJ/image.png?auth_key=1680851093-9gQEUbFR2TMXcVhGgKW7M2-0-f45eaf095b7f98ff97b437a72f0c1aeb)

和“逐语句执行”按钮不同，“逐过程执行”按钮常用在一个方法调用多个JS文件时，涉及到的JS代码比较长，则会使用到这个按钮。

在复杂的JS文件中，F11会进入到jQuery等工具库文件的内容，点击逐过程执行的按钮就可以跳过框架的JS脚本了

###  条件断点

已有断点位置的右键菜单中选择“Add Breakpoint…”或者空白位置右键出现的菜单选择“Add Conditional Breakpoint”，可以设置触发断点的条件，就是写一个表达式，表达式为true时才触发断点。

可以用条件断点代替在代码中的**console.log**

###  调用栈（Call Stack）

在断点停下来时，右侧调试区的**Call Stack**会显示当前断点所处的方法调用栈，比如有一个函数g()其中又调用了函数**f()**，而我在**f()**中设置了一个断点，那么执行函数g()的时候会触发断点，其调用栈显示如下

![](https://secure2.wostatic.cn/static/n2EmPxXUU5c2qz9b8SWrbx/image.png?auth_key=1680851093-qo7Q2Crxcr5ofnpeTic8zm-0-8631dfe33f2f566c368255e7ce1e904b)

最上面是f()，然后是g()。[调用栈](https://www.zhihu.com/search?q=调用栈&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":2603739492})中的每一层叫做一个frame，点击每个frame可以跳到该 frame 的调用点上。

此外，还可以在frame上用右键菜单重新开始执行当前frame，也就是从该frame的开始处执行。比如在函数**f()**的frame上Restart Frame， 断点就会跳到**f()**的开头重新执行，context中的[变量值](https://www.zhihu.com/search?q=变量值&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":2603739492})也会还原。

这样结合变量修改和编辑代码等功能，就可以在当前frame中反复进行调试，而不用刷新页面重新触发断点了。

###  查看变量

Call Stack列表的下方是Scope Variables列表，在这里可以查看此时局部变量和全局变量的值。

###  临时修改 JavaScript 代码

Chrome中可以临时修改JS文件中的内容，保存（Ctrl+S）就可以立即生效，结合Console等功能就可以立即重新调试了。但注意这个修改是临时的，刷新页面修改就没了。

###  在页面操作时触发断点

在Source面板，右下角Event Listener Breakpoints菜单，选择不同的类型的DOM事件，可以在页面上发生对应的事件时，触发断点

###  Copy As Fetch复制为 Fetch

在Network标签下的所有的请求，都可以复制为一个完整的Fetch请求的代码。

操作：

- 在**Network**标签页中，选中一个请求
- 右击，选择**Copy --> Copy as fetch**

![](https://secure2.wostatic.cn/static/a4TpRkdsnuDSkdb1QVPjtp/image.png?auth_key=1680851093-eaYzZue6vGDohYiBdtSM82-0-b08d0479de683777e9ef654848a28286)

###  截屏

在新版本的Chrome中，提供了一个截图的API，你可以将整个页面截图或者截取部分页面元素，且截取的图片尺寸跟浏览器当前视图中要截取的内容所占尺寸一致。截图输出的是png格式的图片，会自动通过浏览器下载到默认的目录下。现在有三种截取的方式：截取整个页面、部分元素 或 当前视图。

- **CMD + SHIFT + P**(windows中用CTRL + SHIFT + P) 打开命令菜单
- 在**Elements**标签页，选中要截取的页面元素
- 选择**Capture node screenshot**（或者**Capture full size screenshot**或者**Capture screenshot**）

### 复制变量

不管是在Network面板还是在Sources面板获得的变量，如果想要拷贝出来都可这样做：

（1）右键点击这个变量，选择**Store as global varible**，如果之前没有进行过这样的操作，那么想要拷贝的变量就会被复制到**[temp1](https://www.zhihu.com/search?q=temp1&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra={"sourceType":"answer","sourceId":2603739492})**这个全局变量中

（2）在控制台输入**copy(temp1)**，这时候这个变量就被复制到了剪贴板

（3）在需要的地方粘贴即可

## CSS技巧
### 创建防篡改对象

`Object.freeze()` 方法。冻结对象变为只读，属性和方法都不能被修改、添加、删除或者重新定义

```JavaScript
const Obj = {
  name:'zs',
  age:18
}
Object.freeze(Obj)
Obj.name = 'ls'
console.log(Obj);
//重新赋值会报错
```

![](https://secure2.wostatic.cn/static/27tFSSbZw4okQV4yzQcB6b/image.png?auth_key=1680852092-2z9HhG24pZR7doDcbnnGsV-0-d855e70ebd5e8be26bdbf3062f2dcb3b)

### .响应式文档样式布局

```JavaScript
.parent{
  display: grid;
  grid-template-columns: minmax(150px, 25%) 1fr;
}

```

![](https://secure2.wostatic.cn/static/91CvVFh18iiuFW6Ryd4JEH/image.png?auth_key=1680852092-rtEEwwVaDWEKFZqLNb8eqd-0-9bf26f4cdaa29973caa29be7991f11be)

### 自定义光标

```CSS
html{
  cursor:url('no.png'), auto;
}
```

![](https://picx.zhimg.com/v2-ba9a7e4949da0934094cbdfa6c9db4d3_b.webp)

### **用图像填充文本**

```JavaScript
h1{
  background-image: url('images/flower.jpg');
  background-clip: text;
  color: transparent;
  background-color: white;
}
```

![](https://secure2.wostatic.cn/static/sQNFnU5qmmRaVoJwwUx9dX/image.png?auth_key=1680852092-tcmgr3ncWqP2wHyDu9JWHF-0-e0dc3d02594e82afaafe652c572abaa3)

注意：使用此技术时，请始终指定background-color，因为如果由于某种原因图像未加载，可以将其用作回退值。

### **为文本添加描边效果**

使用`text-stroke`属性可以使文本更清晰可见，因为会向文本添加描边笔触或轮廓

```CSS
h1 {
  -webkit-text-stroke: 5px crimson;
  text-stroke: 5px crimson;
}
```

![](https://secure2.wostatic.cn/static/4TzR5mAQZLQDvnKp99MTqn/image.png?auth_key=1680852092-5PdAhWvBRBK8BgcSD4a39B-0-d18a04900037ee4fa6456cbb07763843)

### `:paused` **伪类**

使用`:paused`选择器在暂停状态下设置媒体元素的样式，与`:paused`类似的还有`:playing`。

```CSS
/* 目前只支持 Safari 浏览器 */

video:paused {
  opacity: 0.6;
}
```

### **强调文本**

使用`text-emphasis`属性将强调标记应用于文本元素。你可以指定任意字符串（包括表情符号）作为值。

```CSS
h1 {
  text-emphasis: "⏰";
}
```

![](https://secure2.wostatic.cn/static/8BQEdZrwe6tmMbKuAtaW5U/image.png?auth_key=1680852092-b4KRxE7F9nbugwJ3bkga1R-0-aafcc86ba1148c6425a93ac966989a97)

### **首字母下沉**

避免不必要的`span`，改用伪元素来设置内容的样式，同样，与`:first-letter`伪元素类似的还有`:first-line`伪元素。

```CSS
h1::first-letter{
  font-size: 2rem;
  color:#ff8A00;
}
```

![](https://secure2.wostatic.cn/static/feHBMv2Dmmr1DC6CR6bhbT/image.png?auth_key=1680852092-jhAqVmH76FWERZu9mJsZFC-0-88ae91c1c4d256a1ea01efe60c4a0495)

### **更改书写模式**

你可以使用书写模式属性来指定文本在网站上的布局方式，垂直或水平布局。

```CSS
<h1>Cakes & Bakes</h1>

h1 {
  writing-mode: sideways-lr;
}
```

![](https://secure2.wostatic.cn/static/2dxLgLKPGCKFpcEiZkkUgc/image.png?auth_key=1680852092-5Cr9E1XgCprwcL5Rs9ibtv-0-989c99b5abe79937e5b905623dbc3187)

### **彩虹动画**

为元素创建连续循环的颜色动画以吸引用户的注意力

```CSS
button{
  animation: rainbow-animation 200ms linear infinite;
}

@keyframes rainbow-animation {
  to{
    filter: hue-rotate(0deg);
  }
 from{
    filter: hue-rotate(360deg);
  }
}

```

### **悬停缩放**

```CSS
/* 定义图片容器的高度和宽度，以及设置图元溢出时隐藏 */
.img-container {
  height: 250px; width: 250px; overflow: hidden;
 }

/* 让图片填充整个容器 */

.img-container img {
  height: 100%; width: 100%; object-fit: cover; 
  transition: transform 200m ease-in;
 }

 img:hover{
  transform: scale(1.2);
 }

```





### **属性选择器**

使用属性选择器根据属性选择HTML元素。

```CSS
<a href="">HTML</a>
<a>CSS</a>
<a href="">JavaScript</a>

/* 为每个带href的a元素设置颜色 */

a[href] {
  color: crimson;
}
```

![](https://secure2.wostatic.cn/static/nP5RWZAv4iiemTYAyvYKJH/image.png?auth_key=1680852092-gA5TTiF5rb5yBd9yRdfA9X-0-4e6a77bc0052175fedb38f4ee5553a62)

### **剪切元素**

使用`clip-path`属性创建有趣的视觉效果，例如将元素剪裁为自定义的三角形或六边形形状。

```CSS
div {
  height: 150px;
  width: 150px;
  background-color: crimson;
  clip-path: polygon(50% 0%, 0% 100%, 100% 100%);
}
```

![](https://secure2.wostatic.cn/static/6LvaoAQAnNJVd3YfX2Xjro/image.png?auth_key=1680852092-6j4ubb5AU7fGfZn8yeYMDi-0-503ba53bd4bfed27051b6f2673afb41a)

### **检测属性支持**

使用CSS `@support rule`直接在CSS中检测对CSS功能的支持。

```CSS
@supports (accent-color: #74992e) {
/* 如果浏览器支持，则以下代码可以运行 */
  blockquote {
    color: crimson;
  }

}
```
![](https://secure2.wostatic.cn/static/wWzMCtJqcWoQ4QwgamSdFo/image.png?auth_key=1680852092-2DNv3pchA2RaEXUgv52pCT-0-5b1f738c5cb8e4f0bf4fa85184ec2bb0)
###  **clamp函数**

`clamp()`函数可用于响应式和流畅的排版。

```JavaScript
/* Syntax: clamp(minimum, preferred, maximum) */
h1{
  font-size: clamp(2.25rem,6vw,4rem);
}
```
###  **设置可选字段的样式**

你可以使用`:optional`伪类设置表单字段的样式，例如输入框、下拉框和文本框，这些字段上没有必要属性。

```CSS
*:optional{
  background-color: green;
}
```
###  **字间距属性**

使用`word-spacing`属性指定两个单词之间的空格长度。

```
p {
  word-spacing: 1.245rem;
}
```
###  **创建渐变阴影**

创建渐变阴影以提供独特的用户体验。
```c
:root{
  --gradient: linear-gradient(to bottom right, crimson, coral);
}

div {
  height: 200px;
  width: 200px;
  background-image: var(--gradient);
  border-radius: 1rem;
  position: relative;
}

div::after {
  content: "";
  position: absolute;
  inset: 0;
  background-image: var(--gradient);
  border-radius: inherit;
  filter: blur(25px) brightness(1.5);
  transform: translateY(15%) scale(0.95);
  z-index: -1;
}
```
![](https://secure2.wostatic.cn/static/mUpTZCyaVzkyw86HEiaqsY/image.png?auth_key=1680852092-h3JqxVHgENBTXFXeqqZpQN-0-d07b89858495aa8367a066ec9a23b801)
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
##  VUE组件全局自动注册 及 VUEx仓库module自动导入
### 组件全局注册 页面无需引入直接做标签使用
####   #读取需要全局自动注册的文件 例如：在src的components wholeSituation文件夹 
#### test和test2为全局注册组件

![](https://secure2.wostatic.cn/static/p6KH9jUhHuWbWcRCuPUa7j/image.png?auth_key=1680855319-fqjL2eGqLtPoC3E7xFDeQ6-0-8d296f5a6f518bf97cb200df665b5104)
#### wholeSituation根目录新建index.js文件

```c
export default {
  install(Vue) {
//自动读取文件夹下的文件require.context()
//require.context('需要读取的文件路径','是否读取子文件夹 boolean值','需要读取的文件-正则匹配')
    const req = require.context('./', true, /.vue$/)
//函数内keys方法，keys方法调用后返回所有读取文件的路径
    req.keys().forEach((item) => {
//req(读取的文件路径)可以相当于导入对应的文件     返回值.default的内容就是导入的文件
      const com = req(item).default
//此处的name名注意！！！！使用是的标签名称就是文件中的name名
      Vue.component(com.name, com)
    })
  }
}
```
![](https://secure2.wostatic.cn/static/2SaRj8BrUDmBTSxB3Ew7Am/image.png?auth_key=1680855423-vyNaNEiTHdKGm8P3nwaiqR-0-5c53fd33d0a51e2dee61cd95dc0406bc)

#### 在main.js中做导入

```c
import test from '@/components/wholeSituation/index.js'
Vue.use(test)
```

### Store自动注册modules
#### 同上查找文件
```c
const modulesFiles = require.context('./modules',true,/\.js$/)
const modules = modulesFiles.keys().reduce((modules,modulePath)=>{
const moduleName = modulePath.replace(/^\.\/(.*)\.\w+$/,'$1')
const value = modulesFiles(modulePath)
modules[moduleName] = value.default
return modules
},{})
//最后导入
const store = new Vuex.Store({
modules,
})
```
