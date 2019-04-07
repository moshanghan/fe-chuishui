前端开发过程中，尺寸单位是我们必须用到的，下面我们对css中常见的几种尺寸单位px,em,rem,rpx进行逐一介绍  
在这之前，需要先对几个概念进行普及介绍  

## 基本概念
（以下概念读起来可能有些晦涩，如果看不懂也没关系）  
### 像素
它不是自然界的物理长度，指基本原色素及其灰度的基本编码。  
css中的像素只是一个抽象的单位，在不同的设备或不同的环境中，css中的1px所代表的设备物理像素是不同的。在为桌面浏览器设计的网页中，我们无需对这个津津计较，但在移动设备上，必须弄明白这点。在早先的移动设备中，屏幕像素密度都比较低，如iphone3，它的分辨率为320x480，在iphone3上，一个css像素确实是等于一个屏幕物理像素的。后来随着技术的发展，移动设备的屏幕像素密度越来越高，从iphone4开始，苹果公司便推出了所谓的Retina屏，分辨率提高了一倍，变成640x960，但屏幕尺寸却没变化，这就意味着同样大小的屏幕上，像素却多了一倍，这时，一个css像素是等于两个物理像素的。其他品牌的移动设备也是这个道理。  

### 物理像素
它是显示器（电脑、手机屏幕）最小的物理显示单位，物理像素指的是显示器上最小的点。物理像素的大小取决于屏幕。是一个无法改变的属性。  

### 设备独立像素
我上一张图，你就会理解什么是设备独立像素  
[![](http://www.qdtalk.com/wp-content/uploads/2019/03/dlxs-1.png)](http://www.qdtalk.com/wp-content/uploads/2019/03/dlxs-1.png)  
就是我们开发过程中使用的css中的px  

### 设备像素比（device pixel radio）
设备像素比 = 物理像素 / 设备独立像素，单位是dpr！（device pixel radio）  

### Retina屏幕

> 所谓“Retina”是一种显示标准，是把更多的像素点压缩至一块屏幕里，从而达到更高的分辨率并提高屏幕显示的细腻程度。也被称为视网膜显示屏  ——百度百科

因为Retina屏幕的出现，在pc端默认情况下，css中的1px等于1物理像素，但在移动端1px不一定等于1物理像素，比如说iPhone的设备独立像素是375 * 667，因为它使用了Retina屏幕，他的dpr是2，所以iPhone 6 的物理像素为 750 * 1334  

在不同的屏幕上(普通屏幕 vs retina屏幕)，css中1px所呈现的大小(物理尺寸)是一致的，不同的是1px所对应的物理像素个数是不一致的。  
在普通屏幕下，1px 对应 1个物理像素(1:1)。 在Retina屏幕下（dpr=2），1px对应 2x2个物理像素(1:4)。  
你会发现，在移动端开发中使用了图片（img标签），2倍图要比1倍图清晰，就是这个缘故  

## px
 默认情况下像素px是相对于屏幕分辨率而言，比如说我们的屏幕分辨率是1440 X 900，说的就是像素1440px  X  900px；  
 这里会遇到另一种情况  
 ### 浏览器缩放
 缩放是缩放CSS像素(缩放比例为1时,一个CSS像素等于一个屏幕像素)，就是在屏幕分辨率不变的情况下，用户对浏览进行了缩放  
 **强调一点**，用户的缩放行为是对浏览器进行的，缩放的是css像素，而非分辨率，分辨率是屏幕的分辨率，不论你怎么缩放当前页面，屏幕分辨率都不会改变  
 我们知道在移动端可以在一定程度上控制用户的缩放行为，也可以禁止用户缩放  
```html
 <meta name="viewport" content="width=device-width,initial-scale=1.0">
```
content属性值 :  

1. width:可视区域的宽度，值可为数字或关键词device-width
2. height:可视区域的高度，值可为数字或关键词device-height
3. intial-scale:页面首次被显示是可视区域的缩放级别，取值1.0则页面按实际尺寸显示，无任何缩放
4. maximum-scale=1.0, minimum-scale=1.0;可视区域的缩放级别，
5. maximum-scale用户可将页面放大的程序，1.0将禁止用户放大到实际尺寸之上。
6. user-scalable:是否可对页面进行缩放，no 禁止缩放

但是在pc端就麻烦了  
windows：

- 	ctrl + +/-
- 	ctrl + 滚轮
- 	浏览器菜单栏

mac：

- 	cammond + +/-
- 	浏览器菜单栏 

由于浏览器菜单栏属于系统软件权限，没发控制，我们可以通过js控制ctrl/cammond + +/- 或 Windows下ctrl + 滚轮 缩放页面的情况  
具体参考 [阻止pc端浏览器缩放js代码](https://www.cnblogs.com/snail222/p/6256229.html "阻止pc端浏览器缩放js代码")  

## em
本人在实际开发过程中并没有使用过em单位，但是后面要说的rem是基于em的，所以，对em进行简单介绍  
em 是相对长度单位。相对于当前对象内文本的字体尺寸。如当前对行内文本的字体尺寸未被人为设置，则相对于浏览器的默认字体尺寸。  

- em的值并不是固定的；
- em会继承父级元素的字体大小；
- 任意浏览器的默认字体高都是16px。所有未经调整的浏览器都符合: 1em=16px。body选择器中声明Font-size=62.5%<==>1em=10px。

看个栗子吧
```html
<body>
  <style>
    html {
      font-size: 50px;
    }

    .my-div {
      width: 100%;
      height: 500px;
      margin-top: 50px;
      background-color: gray;
      font-size: 40px;
    }

    .my-div .parent-font {
      font-size: 30px;
    }

    .my-div .parent-font .child-font {
      font-size: 0.5em;
    }
  </style>
  <div class="my-div">
    <p class="parent-font">
      我是父级文字
      <span class="child-font">我是子级文字</span>
    </p>
  </div>
</body>
```
html代码中，  
第一级，html的 font-size: 50px;  
第二级，my-div 的 font-size: 40px;  
第三级，parent-font 的 font-size:30px;  
第四级，child-font 的 font-size:  0.5em;  
[![](http://www.qdtalk.com/wp-content/uploads/2019/03/fonts1.png)](http://www.qdtalk.com/wp-content/uploads/2019/03/fonts1.png)  
我们通过浏览器查看，发现第四级的fong-size为15px；  

我们取消第三级parent-font 的字体大小  
[![](http://www.qdtalk.com/wp-content/uploads/2019/03/fonts2.png)](http://www.qdtalk.com/wp-content/uploads/2019/03/fonts2.png)  
我们通过浏览器查看，发现第四级的fong-size为20px；  
当我们取消第三级font-size后，第三级的字体大小为40px；  


**所以我们说em的字体大小不是固定的，em的大小取决于父级的字体大小**
当父级的字体大小为20px，子级的1em就是20px  
当父级的字体大小为30px，子级的1em就是30px  

那么说font-size存在着继承父级的特点  
我们在第一级html中设置font-size，第二级继承第一级，第三级继承第二级，第四级继承第三级，以此类推  
每一级都继承自它的父级，也就是说每一级的em所代表的px大小都不是固定的，因为他们的父级不是同一个，所以em的应用场景并不多。  
那么如果是em的都继承自同一个地方，是不是可以解决很多问题呢？  
这时候rem出现了  

## rem
rem 是CSS3的一个相对单位（root em，根em）  
使用rem为元素设定字体大小时，仍然是相对大小，但相对的只是HTML根元素  
只要html的font-size大小不变，1rem所代表的font-size大小就不会变，rem只取决于html的font-size  

### rem解决了哪些问题
移动设备的宽度是各种各样的，每个设备的dpr也不同，换句话说就是不同设备每一行的物理像素数不同，能显示的css的px数也不同，  

如果我们写一个div，宽度是375px，375px在这个屏幕（iPhone6）上是刚刚满屏，因为这个屏幕宽度刚刚是375px( 设备独立像素)，当我们换另一个宽度是414px的设备（iPhone6Plus）时，这个宽度375px的div就无法铺满这个屏幕，同样的当换一个iPhone5（320px），又会出现滚动条，安卓机的宽度更是五花八门，使用media媒体查询不靠谱，因为它不能覆盖所有的机型宽度  
我们之前说rem的大小是相对于html的font-size的，如果html的font-size根据不同设备的宽度做动态计算，问题就会得到解决  
我们写页面都是根据UI设计稿来做的，我们假设UI设计稿的宽度是750px（750px是常规宽度，当然也可以是640px或是其他宽度，但是整个项目，宽度必须统一），唯一不变就是就屏幕宽度，我们的html的font-size（rem）只取决于设备宽度  
于是  
```javascript
document.documentElement.style.fontSize = 100 * ( document.documentElement.clientWidth / 750) + 'px'
```
html的font-size：document.documentElement.style.fontSize  
设备的宽度：document.documentElement.clientWidth  
750：UI设计稿的宽度  
为了方便计算我们将font-size x 100，方便计算（乘100不是必须的，我接触过一些项目就不是乘以100，但是UI设计稿中使用了sketch做了动态计算，但我还是建议乘100，不然遇到psd的设计图就很麻烦了）  
对上面的js做些完善  
```javascript
const fontFun = function () {
  let docEl = document.documentElement
  let resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize'
  const recalc = function () {
    let clientWidth = docEl.clientWidth
    if (!clientWidth) return
    docEl.style.fontSize = 100 * (clientWidth / 750) + 'px'
  }
  if (!document.addEventListener) return
  window.addEventListener(resizeEvt, recalc, false)
  window.addEventListener('pageshow', recalc, false)
  document.addEventListener('DOMContentLoaded', recalc, false)
}
export {
  fontFun
}
```
对以上代码不做过多解释  
也可以这样写  
```javascript
(function(doc, win) {
  var docEl = doc.documentElement,
    resizeEvt = 'orientationchange' in window ? 'orientationchange' : 'resize',
    recalc = function() {
      var clientWidth = docEl.clientWidth
      if (!clientWidth) return
      docEl.style.fontSize = 100 * (clientWidth / 750) + 'px'
    }
  if (!doc.addEventListener) return
  win.addEventListener(resizeEvt, recalc, false)
  win.addEventListener('pageshow', recalc, false)
  doc.addEventListener('DOMContentLoaded', recalc, false)
})(document, window)
```
iPhone5(320px)下html的font-size:42.6667px,1rem=42.6667px  
iPhone6(375px)下html的font-size:50px,1rem=50px  
iPhone6Plus(414px)下html的font-size:55.2px,1rem=55.2px  
rem是继承自html的font-size，但是小程序中没有html，那怎么办呢？  

## rpx  
我不基于html的font-size了，我基于一个别的值就行了，你也不需要计算这个值，我给你计算了，这就是rpx。  
最终的效果就是，你开发时在iphon6的设计稿上量了多少px，就写多少rpx就行了，完美适配，perfect！  
[![](http://www.qdtalk.com/wp-content/uploads/2019/03/xcx.png)](http://www.qdtalk.com/wp-content/uploads/2019/03/xcx.png)



参考链接  
[【微信小程序】——rpx、px、rem等尺寸间关系浅析](http://www.bubuko.com/infodetail-1923012.html "【微信小程序】——rpx、px、rem等尺寸间关系浅析")   
[px、物理像素、rem、rpx的关系](https://www.jianshu.com/p/1b69f0df78f3?from=timeline "px、物理像素、rem、rpx的关系")  
[阻止pc端浏览器缩放js代码](https://www.cnblogs.com/snail222/p/6256229.html "阻止pc端浏览器缩放js代码")  
[viewport解释](https://blog.csdn.net/u012402190/article/details/70172371)  
[CSS中大小单位px，em，rem 以及微信小程序的rpx](https://www.cnblogs.com/Leesoo/p/6903148.html "CSS中大小单位px，em，rem 以及微信小程序的rpx")  