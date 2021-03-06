# adaptive-dookay
H5端自适应解决方案

使用方便，设计图的1px对应0.01rem,设计图的字体大小24px对应0.24rem,就是如此简单！

本插件forked于finance-sh/adaptive，这里新增了minWidth参数项设置最小款度，用于解决pc端的适配问题。

[上海稻壳网络科技有限公司](http://www.dookay.com)


### 使用方法：

代码引用方法一：
    
    npm install adaptive-dookay --save
   
代码引用方法二：

    在页面head写入以下代码，实时更新html的fontsize:
    
    <script src="js/adaptive.js"></script> 

```javascript
<script>
    // 设计图宽度，如果是pc端请设置为设计稿居中内容的宽度，以下以移动端作为示例
    window['adaptive'].desinWidth = 640;
    // body 字体大小 会将body字体大小设置为 baseFont / 100 + 'rem'  750的设计图一般设置为28,640的设计图一般设置为24
    window['adaptive'].baseFont = 24;
    /*
    // 显示最大宽度 可选
    window['adaptive'].maxWidth = 480;
    window['adaptive'].minWidth = 320;
    // rem值改变后执行方法 可选
    window['adaptive'].setRemCallback = function () {
        alert(1)
    }
    // scaleType 1:iphone andriod下viewport均无缩放 2:iphone下viewport有缩放,andriod没有 3:iphone andriod下viewport均有缩放; 默认值为1
    window['adaptive'].scaleType = 1;
    */
    // 初始化
    window['adaptive'].init();
</script>
```
## 优先加载该JS，并执行


然后在css中设置相应样式即可： 设计图的1px对应0.01rem,设计图的24px对应0.24rem,就是如此简单！
```css
.main-info {
    height: 0.88rem;
    padding-bottom: 0.24rem;
}
.fund-info {
    position: relative;
    font-weight: normal;
    padding: 0.2rem 0;
    padding-right: 1.7rem;
    padding-left: 0.23rem;
    font-size: 0.32rem;
    line-height: 1;
}
```
字体也推荐使用rem
### 推荐 window['adaptive'].scaleType = 1, 对于window['adaptive'].scaleType = 3谨慎使用，因为安卓某些版本可能会遇到viewport缩放有bug,待解决
## 优化宽度问题
新增最大宽度，解决平板或手机横屏时体验不佳的问题，新增最小宽度，解决pc端部分适配问题
```javascript
window['adaptive'].maxWidth = 480; // 设置最大宽度，默认540px
window['adaptive'].minWidth = 320; // 设置最小宽度，默认320px
```

如果不需要居中，则不用添加任何代码，手机横屏时就会整个页面自适应且不会也没元素不会太大

如果需要居中，需要css配合使用，添加如下代码：

```css
body {
    max-width: 6.4rem; // 设计图宽度为640px时为6.4rem ,750时为7.5rem ，以此类推
    margin: 0 auto;
}
body * {
    max-width: 6.4rem; // 设计图宽度为640px时为6.4rem ,750时为7.5rem ，以此类推
}
```
## window['adaptive'].remToPx 方法，将rem值转换为px   
```javascript
// 将1rem转换为像素值
window['adaptive'].remToPx(1) 
```
### adaptivejs原理：

    最最理想的解决方案当然是设计图和手机屏幕的像素点一一对应，就像我们在PC端所做的一样。
    拿750px宽的设计图来说，如果手机屏幕的水平分辨率是750px，那么这样是最理想最完美的，对于水平分辨率不是750px的屏幕呢？
    这个时候我们把设计图进行缩放，使其宽度刚好与屏幕的分辨率相等，即是等比例缩放设计图，使其宽度刚好覆盖手机屏幕。
    如果我们使用<meta content="width=device-width,minimum-scale=1.0,maximum-scale=1.0,user-scalable=no,minimal-ui" name="viewport">这种常用标签，视觉同学经常会抱怨1px过粗的问题，这个时候我们需要缩放viewport来达到真正还原1px的效果。

    开发的便捷性也是理想解决方案的一部分，大部分rem解决方案，是把设计图分成100份，1rem等于10份，总宽度10rem。
    如果设计图是750px，1rem就等于75px，切图的时候需要将px转换为rem,这个时候就需要用设计图的px值除以75得到rem的值，得到的rem值基本都是带几位小数的，虽然我们可以用less，sass这类css预处理语言来统一处理，但是如果我们让1rem等于100px，在切图的时候就可以更加快速方便了。
    比如设计图宽度为100px，css则书写为1rem即可。

    要实现1rem等于设计图100px，页面的总的宽度就不是10rem了，而是随设计图的宽度而变化了。
    拿750px的设计图来说，总的宽度就是7.5rem。我们先根据设备像素比来缩放viewport，布局视口的宽度就调整好了，布局视口同样的宽度是7.5rem，我们就可以计算出html根元素的像素值，这样就大功告成了。
    如果是文字，我们也建议使用rem。  
    
    对于iphone的retina高清显示屏，基本版本(adaptive.js)我们做了缩放处理，以达到最佳显示效果。 对于快速开发版本(adaptive-version2.js),viewport的width等于设备宽度，不会缩放
    如果只是webview里，可以使用 adaptive-version3.js 在IOS和安卓下都会缩放，否则还是谨慎使用此版本，抱歉
    
### 注意：可能存在0.01rem将小于1px而不显示的问题,故如果设计图是1px,在css中仍然用1px显示。
 可用的全局变量：window.devicePixelRatioValue 当前页面设置的设备像素比

### 关于3个版本
```javascript
window['adaptive'].scaleType = 2  // iphone下缩放，retina显示屏下能精确还原1px
window['adaptive'].scaleType = 1 // 没有缩放，能快速开发的版本
window['adaptive'].scaleType = 3 // 无论iphone还是安卓手机，都能精确还原1px，做到高度还原视觉稿，如果只是在webview里使用，建议使用，否则请谨慎使用
