# 闲言碎语
想要在走前记录下移动端布局的，每次都会忘记  在记录之前还是很感慨这几年在这里的学习很成长
以至于连离职的时候，我都不知道要说什么 千万个舍不得又觉得像是在逞强
以后不打算再做个嘴硬心软的人 要做个心硬嘴软的
刚好在听林宥嘉的我没有说谎
觉得好搞笑我为什么还要在这个全球同性最佳交友平台上诉说我要离职的心情，有点幼稚 只是恍然
我一直在从一个圈子 跳到另外一个圈子 从小到大好像都是这样 然后我又离开了 一个人往前走
我相信我会越来越好的  因为太感慨
像又毕业了一次 
想起那段勤劳 可爱的日子
在前面 写上这样一段话 祝福自己 祝福身边的人越来越好 一切顺利


# 移动端布局方案
日常css布局无非就这四种计算
- em   em少用 后期有用到补上
- rem
- px
- 百分比+媒体查询

## 1、em
介绍概念
他是一种相对于父元素的字体大小的单位 少用 不做详细介绍


## 2、px

<code>px + fiexible_css.js ：</code>
我们在项目中，经常是pc 手机端一起做 手机端因为大多数游戏图为主 会用到淘宝的一个封装js插件
设计稿以750为主,会在开头初引入这样一段

引用fiex.js
设置meta标签的width =750
其他还是按照切图出来的像素来进行css布局
github上做的手机端的项目基本上都是用这个做的 之后有不懂得可以在其他项目里的手机mobile目录查看

```javascript
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"/>
    <meta name="viewport" content="target-densitydpi=device-dpi,width=750,user-scalable=no">
    <script src="js/flexible_css.js"></script>
  <!-- HTML5 Shim and Respond.js IE8 support of HTML5 elements and media queries -->
    <!-- WARNING: Respond.js doesn't work if you view the page via file:// -->
    <!--[if lt IE 9]>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.3/html5shiv.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/respond.js/1.4.2/respond.min.js"></script>
    <![endif]-->
</head>
```

### ps
加个ps是要说下这个方法虽然从效果上看是最完美的<br>
但不是最流行的 因为在不同的浏览器上 有不确定性 <br>
图片没事 但是遇到产品界面 例如app界面 移动端有很多浏览器 在不同的浏览器或者微信上 高度都是不同的<br>
所以可能会造成间距上 例如margin padding position定位上的问题 导致整个页面在维护上会加重负担<br>
所以最好还是我们用js去获取当前设备的宽度 window.screen.width <br>

## 3、rem  
rem应该是最为推崇的方式了 因为百搭又高效  可以配合scss预处理器来开发<br>
我们先介绍下rem的概念<br>
rem是一种相对于根元素的字体大小的单位 他可以很好的实现等比例的适配所有屏幕<br>

这里附上文件里的代码 

```javascript 
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
    <title>Title</title>
    <style>
        @import "css/base.css";
    </style>
    <script>
        !function (n) {
            var e = n.document,
                    t = e.documentElement,
                    i = 750,
                    d = i / 100,
                    o = "orientationchange" in n ? "orientationchange" : "resize",
                    a = function () {
                        var n = t.clientWidth || 320;
                        n > 750 && (n = 750);
                        t.style.fontSize = n / d + "px"
                    };
            e.addEventListener && (n.addEventListener(o, a, !1), e.addEventListener("DOMContentLoaded", a, !1))
        }(window);
    </script>
</head>
<body>
<div class="box">
    <div class="test">
        <p class="tro">这是一个小图 测试用</p>
        <img src="http://www.rni-l.com/mobile/images/pic.jpg">
    </div>
</div>
</body>
</html>
```


```javascript
@function pr($px, $base-font-size: 50px) {
  @if (unitless($px)) {
    //@warn "Assuming #{$px} to be in pixels, attempting to convert it into pixels for you";
    @return pr($px + 0px); // That may fail.
  } @else if (unit($px) == rem) {
    @return $px;
  }
  @return ($px / $base-font-size) * 1rem;
}

@mixin wh($w) {
  width: $w;
  height: $w;
}

.box {
  overflow: hidden;
  margin: 0 auto;
  @include wh(pr(350));
  border: 1px solid black;
}

.test {
  @include wh(pr(135));
  border: 1px solid wheat;
  p.tro{
    font-size: pr(12);
  }
  img {
    @include wh(pr(80));
  }
}
```

我们可以看到随着切换 font-size会重新计算<br> 
使用rem布局结合在html上根据不同分辨率设置不同font-size有很多不好解决的麻烦，<br> 
font-size通过js计算出来的，所以当分辨率发生变化时，html的font-size就会变，<br> 
不过这得在我们调整分辨率后，才能看得到效果 <br> 
看代码就知道为啥font-size是直接写到html的style上面的了（js设置的原因）：<br> 

![截图](https://github.com/JoPure/rem/blob/master/img/remGifImg.gif)


在这个计算的过程中 我们可以看设计稿是640 or 750 <br> 
一般用750<br> 
为了计算方便，取一个100px的font-size为参照，那么body元素的宽度就可以设置为width: 7.5rem，<br> 
于是html的font-size=deviceWidth / 7.5。<br> 
这个deviceWidth就是viewport设置中的那个deviceWidth。<br> 
根据这个计算规则，可得出本部分开始的四张截图中html的font-size大小如下：<br> 
那么body的元素就可以设置width:7.5rem <br> 


       deviceWidth = 320，font-size = 320 / 7.5 = 42.666px
       deviceWidth = 375，font-size = 375 / 7.5 = 50px
       deviceWidth = 414，font-size = 414 / 7.5 = 55.2px
       deviceWidth = 500，font-size = 500 / 7.5 = 66.6667px

当device-width大于750 or 1080 后 物理分辨率则大于1280  这个时候我们可以访问pc网站
之前做项目都是用到 <code> uaredirect.js </code> 


```javascript
    <script src="js/uaredirect.js"></script>
    <script>
        uaredirect('./mobile/index.html');
    </script>
 
```

### 百分比加媒体查询
高度自适应占位
刚还没接触rem的时候我就是疯狂用百分比的人 疯狂到我连pc端都在用百分比  背景图的布局会用padding top 撑开<br> 
padding-top的计算方法是用图片的高除以宽得到的 page2-bg假设是750*786 <br> 
则padding-top的值是786 / 750 = 1.048  百分比则为104.8 如果要高度要小一点可以适当在这个范围减小百分比<br> 
这个我好像只会用 大概实现的原理就是给容器添加一个伪元素，在父元素的容器里撑开这个内容高度跟宽度
找到了一篇更详细的文章 可参考

[百分比适配](https://segmentfault.com/a/1190000004231995)  
```javascript 

.index-downLoadDiv {
    position: relative;
    background-size: 100% auto;
    background-repeat: no-repeat;
    background-image: url(../img/page2-bg.jpg);
}

.index-downLoadDiv::before {
    display: block;
    content: "";
    margin: 0 auto;
    padding-top: 94.8%;
}


```
![截图](https://github.com/JoPure/rem/blob/master/img/percent.png)

这个例子可以在cos文件夹vote投票的页面中找到

### 4、结尾
还是会根据业务来看要用哪种方案布局  一开始的我只会用px 死都用px <br> 
后来用百分比 疯狂用百分比跟媒体查询  再到后来会用rem <br> 
并巧妙的学着pc端也用rem配合媒体查询<br> 
这都是一个学习的过程 真的都想回到那个时间段 闷热的小房子 每天加班 滴滴回家的我 喜欢吃无穷鸡翅的我<br> 
感谢小伙伴 感谢我的师傅 感谢这里的一切<br> 

