文章目录

    Web动画形式
    应用场景
    素材准备
    实现方案
        一、GIF图
        二、CSS3帧动画
        三、JS帧动画
    主线程和排版线程
    方案总结
    注意事项
    总结
    

---

## Web动画形式

首先我们来了解一下Web有哪些动画形式

    1. CSS3动画
        Transform（变形）
        Transition（过渡）
        Animation（动画）
    2. JS动画（操作DOM、CSS）
    3. Canvas动画
    4. SVG动画
    5. 以Three.js为首的3D动画

以上各种动画形式都可以制作出一种类型的动画，那就是**帧动画**，也叫序列帧动画，定格动画，逐帧动画等，这里我们统一用帧动画来表述。

![这里写图片描述](https://img-blog.csdn.net/20180710110036414)

示例

## 应用场景

帧动画一般用来实现稍微复杂一点的动画效果，同时希望动画更细腻，设计师更自由的发挥。他可以定义到每一个时间刻度上的展现内容，我们一般用帧动画来做页面的Loading，小人物，小物体元素的简单动画。我们想象中的帧动画应该有以下几个特点：
1. 可以自由控制播放、暂停和停止
2. 可以控制播放次数，播放速度
3. 可以添加交互，在播放完成后添加事件
4. 浏览器兼容性好

## 素材准备

帧动画的素材一般是先由设计师在PS中的时间轴上设计好了，然后导出图片给前端人员，PS制作时间轴动画一般是用来制作稍微简单的动画，操作简单，方便。
或者是由设计师在AE的时间轴进行设计，以为AE内置了更丰富的动作效果，比如转换，翻转之类的，AE可以帮助我们实现更复杂的效果，然后再导出图片给前端人员。
这里帧动画素材的要求，每一帧的图片最好是偶数宽高，偶数张，最好周围能有一些留白。

## 实现方案

将目前用到的解决方案梳理如下图，同时我们将对每种方案进行详细介绍。
![这里写图片描述](https://img-blog.csdn.net/2018071011130196?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

### 一、GIF图

我们可以将上面制作的帧动画导出成GIF图，GIF图会连续播放，无法暂停，它往往用来实现小细节动画，成本较低、使用方便。但其缺点也是很明显的：

1. 画质上，gif 支持颜色少(最大256色)、Alpha 透明度支持差，图像锯齿毛边比较严重；
2. 交互上，不能直接控制播放、暂停、播放次数，灵活性差；
3. 性能上，gif 会引起页面周期性的 绘画 ，性能较差。

### 二、CSS3帧动画

CSS3帧动画是我们今天需要重点介绍的方案，最核心的是利用CSS3中Animation动画，确切的说是使用`animation-timing-function` 的阶梯函数 `steps(number_of_steps, direction)` 来实现逐帧动画的连续播放的。帧动画的实现原理是不断切换视觉内图片内容，利用视觉滞留生理来实现连续播放的动画效果，下面我们来介绍制作CSS3帧动画的几种方案。

**（1）连续切换动画图片地址src（不推荐）**
我们将图片放到元素的背景中（`background-image`），通过更改 background-image 的值实现帧的切换。但是这种方式会有以下几个缺点，所以该方案不推荐。

 - 多张图片会带来多个 HTTP 请求
 - 每张图片首次加载会造成图片切换时的闪烁
 - 不利于文件的管理

**（2）连续切换雪碧图位置（推荐）**
我们将所有的帧动画图片合并成一张雪碧图，通过改变 `background-position` 的值来实现动画帧切换。分两步进行：

**步骤一：** 将动画帧合并为雪碧图，雪碧图的要求可以看上面**素材准备**，比如下面这张帧动画雪碧图，共20帧。
![这里写图片描述](https://img-blog.csdn.net/20180709103614444?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

**步骤二：** 使用steps阶梯函数切换雪碧图位置

先看写法一：
```js
<div class="sprite"></div>

.sprite {
    width: 300px;
    height: 300px;
    background-repeat: no-repeat;
    background-image: url(frame.png);
    animation: frame 1s steps(1,end) both infinite;
}
@keyframes frame {
    0% {background-position: 0 0;}
    5% {background-position: -300px 0;}
    10% {background-position: -600px 0;}
    15% {background-position: -900px 0;}
    20% {background-position: -1200px 0;}
    25% {background-position: -1500px 0;}
    30% {background-position: -1800px 0;}
    35% {background-position: -2100px 0;}
    40% {background-position: -2400px 0;}
    45% {background-position: -2700px 0;}
    50% {background-position: -3000px 0;}
    55% {background-position: -3300px 0;}
    60% {background-position: -3600px 0;}
    65% {background-position: -3900px 0;}
    70% {background-position: -4200px 0;}
    75% {background-position: -4500px 0;}
    80% {background-position: -4800px 0;}
    85% {background-position: -5100px 0;}
    90% {background-position: -5400px 0;}
    95% {background-position: -5700px 0;}
    100% {background-position: -6000px 0;}
}
```

针对以上动画有疑问？

问题一：既然都详细定义关键帧了，是不是可以不用steps函数了，直接定义linear变化不就好了吗？

`animation: frame 10s linear both infinite;`

如果我们定义成这样，动画是不会阶梯状，一步一步执行的，而是会连续的变化背景图位置，是移动的效果，而不是切换的效果，如下图：

![这里写图片描述](https://img-blog.csdn.net/20180709160742872)

问题二：不是应该设置为20步吗，怎么变成了1？

这里我们先来了解下`animation-timing-function`属性。

CSS `animation-timing-function`属性定义CSS动画在每一动画周期中执行的节奏。对于关键帧动画来说，timing function作用于一个关键帧周期而非整个动画周期，即从关键帧开始开始，到关键帧结束结束。（timing-function 作用于每两个关键帧之间，而不是整个动画）

接着我们来了解下steps() 函数：

 - steps 函数指定了一个阶跃函数，它接受两个参数。
 - 第一个参数接受一个整数值，表示两个关键帧之间分几步完成。
 - 第二个参数有两个值< start > or < end >。默认值为< end > 。
 - step-start 等同于 step(1, start)。step-end   等同于 step(1, end)。

综上我们可以知道，因为我们详细定义了一个关键帧周期，从开始到结束，每两个关键帧之间分 **1** 步展示完，也就是说0% ~ 5%之间变化一次，5% ~ 10%变化一次，所以我们这样写才能达到想要的效果。

----------

再看写法二：

```js
<div class="sprite"></div>

.sprite {
    width: 300px;
    height: 300px;
    background-repeat: no-repeat;
    background-image: url(frame.png);
    animation: frame 1s steps(20) both infinite;
}
@keyframes frame {
    0% {background-position: 0 0;}//可省略
    100% {background-position: -6000px 0;}
}
```
这里我们定义了关键帧的开始和结束，也就是定义了一个关键帧周期，但因为我们没有详细的定义每一帧的展示，所以我们要将0%~100%这个区间分成20步来阶段性展示。

也可以换成关键字的写法，还可以只定义最后一帧，因为默认第一帧就是初始位置。

```css
@keyframes frame {
    from {background-position: 0 0;}//可省略
    to {background-position: -6000px 0;}
}
```

**（3）连续移动雪碧图位置（移动端推荐）**
跟第二种基本一致，只是切换雪碧图的位置过程换成了`transform:translate3d()`来实现，不过要加多一层`overflow: hidden;`的容器包裹。这里我们以只定义初始和结束帧为例。

```js
<div class="sprite-wp">
   <div class="sprite"></div>
</div>

.sprite-wp {
    width: 300px;
    height: 300px;
    overflow: hidden;
}
.sprite {
    width: 6000px;
    height: 300px;
    will-change: transform;
    background: url(frame.png) no-repeat center;
    animation: frame 1s steps(20) both infinite;
}
@keyframes frame {
	0% {transform: translate3d(0,0,0);}
    100% {transform: translate3d(-6000px,0,0);}
}
```
用**transform**可以有效解决移动端抖动的问题。
### 三、JS帧动画
**（1）通过JS来控制img的src属性切换（不推荐）**

和上面CSS3帧动画里面切换元素`background-image`属性一样，会存在多个请求等问题，所以该方案我们不推荐，但是这是一种解决思路。

**（2）通过JS来控制Canvas图像绘制**

通过Canvas制作帧动画的原理是用drawImage方法将图片绘制到Canvas上，不断擦除和重绘就能得到我们想要的效果。

```js
<canvas id="canvas" width="300" height="300"></canvas>

(function () {
    var timer = null,
        canvas = document.getElementById("canvas"),
        context = canvas.getContext('2d'),
        img = new Image(),
        width = 300,
        height = 300,
        k = 20,
        i = 0;
    img.src = "frame.png";

    function drawImg() {
        context.clearRect(0, 0, width, height);
        i++;
        if (i == k) {
            i = 0;
        }
        context.drawImage(img, i * width, 0, width, height, 0, 0, width, height);
    }
    img.onload = function () {
        timer = setInterval(drawImg, 50);
    }
})();
```
上面是通过改变裁剪图像的X坐标位置来实现动画效果的，也可以通过改变画布上放置图像的坐标位置实现，如下：
`context.drawImage(img, 0, 0, width*k, height,-i*width,0,width*k,height);`。

**（3）通过JS来控制CSS属性值变化**

这种方式和前面CSS3帧动画一样，有三种方式，一种是通过JS切换元素背景图片地址`background-image`，一种是通过JS切换元素背景图片定位`background-position`，最后一种是通过JS移动元素`transform:translate3d()`，第一种不做介绍，因为同样会存在多个请求等问题，不推荐使用，这里实现后面两种。

- 切换元素背景`background-position`
```js
.sprite {
    width: 300px;
    height: 300px;
    background: url(frame.png) no-repeat 0 0;
}

<div class="sprite" id="sprite"></div>

(function(){
    var sprite = document.getElementById("sprite"),
	    picWidth = 300,
	    k = 20,
	    i = 0,
	    timer = null;
    // 重置背景图片位置
    sprite.style = "background-position: 0 0";
    // 改变背景图位置
    function changePosition(){
        sprite.style = "background-position: "+(-picWidth*i)+"px 0";
        i++;
        if(i == k){
            i = 0;
        }
    }
    timer = setInterval(changePosition, 50);
})();
```
- 移动元素位置`transform:translate3d()`

```js
.sprite-wp {
   width: 300px;
    height: 300px;
    overflow: hidden;
}
.sprite {
    width: 6000px;
    height: 300px;
    will-change: transform;
    background: url(frame.png) no-repeat center;
}

<div class="sprite-wp">
	 <div class="sprite" id="sprite"></div>
</div>

(function () {
    var sprite = document.getElementById("sprite"),
        picWidth = 300,
        k = 20,
        i = 0,
        timer = null;
    // 重置背景图片位置
    sprite.style = "transform: translate3d(0,0,0)";
    // 改变背景图移动
    function changePosition() {
        sprite.style = "transform: translate3d(" + (-picWidth * i) + "px,0,0)";
        i++;
        if (i == k) {
            i = 0;
        }
    }
    timer = setInterval(changePosition, 50);
})();
```


---


## 主线程和排版线程

在现代浏览器中，渲染页面所要负责的线程主要有两个：主线程和排版线程。

### 主线程
- 运行 JS
- 计算 HTML 元素的 CSS 样式
- 布局页面
- 把页面元素绘制成一个或多个位图
- 把这些位图移交给排版线程

在浏览器开始渲染页面，或者长时间执行某个 JS时，主线程会一直在忙碌状态，此时对于用户的任何输入或是操作都不会有所响应。

### 排版线程

- 通过 GPU 渲染位图，并显示在屏幕上
- 向主线程请求更新位图的可见部分或即将可见的部分
- 判断出当前页面处于可见的部分
- 判断出即将通过页面滚动而可见的部分
- 随着用户滚动页面来移动这些部分

排版线程对于用户的操作保持快速的响应，普遍的帧率是每秒 60 帧的速度去渲染刷新，显示器是会以一定的频率来刷新显示器，频率是赫兹（Hz）。

### Transtion
下面我们在网页中实现一个元素的高度变化的动画，鼠标悬浮在元素上动画启动，直至完成，我们来了解一下浏览器的两个线程是如何协同工作的：

```css
<style>
#foo {
  height: 100px;
  width: 100px;
  background: red;
  transition: height 1s linear;
}
#foo:hover {
  height: 200px;
}
</style>
```

> 图中橘黄色部分代表操作相对较慢，消耗较大；蓝色部分代表操作相对较快，消耗较小

![image](https://segmentfault.com/img/remote/1460000010254854)

从上图我们可以看到，浏览器的两个线程在来回地切换工作，而且橘黄色出现次数较多，这意味着浏览器需要处理相当多的工作。

对于浏览器而言，由于元素的高度一直在变化，因此这个动画的每一帧中，都需要重新布局 ——> 绘制页面 ——> 将新的位图加载到 GPU 中 ——> 显示。而其中加载到 GPU 是一个相对缓慢的操作。

## Transform

经过上面的实验，我们对 transition 属性有了比较好的了解；同时我们对上述动画性能也有一个了解。接着我需要在网页中实现一个元素的大小变化动画，鼠标悬浮在元素上动画启动，直至完成：

```css
<style>
#bar {
  height: 100px;
  width: 100px;
  background: red;
  transition: transform 1s linear;
}
#bar:hover {
  transform: scale(2);
}
</style>
```
![image](https://segmentfault.com/img/remote/1460000010254855)

由此我们可以看到，两个线程来回切换的情况并不多，橘黄色部分出现的次数也较少，蓝色部分居绝大部分，这意味着这个动画效果相较于上面的要流畅很多。

在定义中，transform 是不会使浏览器产生重新排版的，因此 transform 不会影响原本的布局，以及周围的元素。它会将定义的元素作为一个整体进行缩放、移动或旋转等。

基于 transform 这类的特性，浏览器在渲染页面时可以节省很多不必要的开支，例如重新布局和将位图传给 GPU 等工作，这样就使得动画更有效率。

## 方案总结

总结以上几种方案，我们可以看到GIF图有一定的优点也有缺点，所以这种方式是看情况选择使用的，选择符合实际场景的方案就是就好的方案。同时我们最常用的是CSS3帧动画，因为通过CSS就可以实现，效果也很好；如果希望添加更灵活丰富的交互就可以采用JS帧动画的解决方案了。下面我们来比较一下各种动画形式的性能。

![这里写图片描述](https://img-blog.csdn.net/20180711095447996?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![这里写图片描述](https://img-blog.csdn.net/20180711095455827?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这两个FPS meter截图是来自CSS3帧动画，可以看到他们都能达到60FPS的流畅动画效果，同时后者是改变背景图位移`transform:translate3d()`，具有GPU加速效果。

![这里写图片描述](https://img-blog.csdn.net/20180711095513774?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![这里写图片描述](https://img-blog.csdn.net/20180711095527279?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)![这里写图片描述](https://img-blog.csdn.net/20180711095534873?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM3Nzg5MDU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

这三个FPS meter截图是来自JS帧动画，分别是Canvas绘制，改变`background-position`，改变`transform:translate3d()`，可以看到JS帧动画的FPS都只有20左右，这个数值的FPS会给人感觉一定的卡顿和不舒适感，同时也看到，他们的波线或多或少有一定的不稳定性，这同样会给人卡顿的感觉，而且不难看出，使用了transform 3D属性具有GPU加速效果，在设备上表现相对会好一点。

综上我们可以对各方案动画性能简单排序：

**GPU 硬件加速CSS3动画 > 非硬件加速CSS3动画 > GPU 硬件加速Javascript 动画 > 非硬件加速Javascript 动画**

**tips**：使用 will-change 可以在元素属性真正发生变化之前提前做好对应准备

## 注意事项
**素材**：动画图片宽高最好是偶数，总帧数最好是偶数，图片拼接处最好有一定的留白。

**适配**：移动端适配最好不用rem，因为rem的计算会造成小数四舍五入，造成一定的抖动效果，建议直接用px作为单位，同时辅助以scale（zoom）媒体查询进行适配。如果使用rem适配，试试使用**translate**的方案，抖动问题可以得到优化解决。

对于帧与帧之间的**盈亏互补**现象导致动画抖动，想要了解更多，可以阅读[《CSS技巧：逐帧动画抖动解决方案》](https://aotu.io/notes/2017/08/14/fix-sprite-anim/index.html)。

## 总结
本文我们主要梳理了目前实现帧动画的几种方案，同时对各种方案进行效果实现，优劣讨论，性能对比，同时简单介绍了帧动画实现过程的注意事项，最后我们得出结论，从实现简单及性能上来看，我们优先选择CSS3帧动画，同时可以根据页面在设备端的适配方案，优先选择 **translate** 的方案。

参考来源：

1. [《CSS3动画之逐帧动画》](https://aotu.io/notes/2016/05/17/css3-animation-frame/index.html)
2. 《指尖上行》
3. [《实用的 CSS — 动画性能对比》](https://segmentfault.com/a/1190000010254851)