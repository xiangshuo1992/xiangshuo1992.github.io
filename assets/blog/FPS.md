> 作者: [前端向朔](https://xiangshuo.blog.csdn.net/) from 迅雷前端
>
> 原文地址：[帧动画的多种实现方式与性能对比](https://xiangshuo.blog.csdn.net/article/details/80773003)


本文目录

    Web动画形式
    应用场景
    素材准备
    实现方案
        一、GIF图
        二、CSS3帧动画
        三、JS帧动画
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
    2. JS动画（操作DOM、修改CSS属性值）
    3. Canvas动画
    4. SVG动画
    5. 以Three.js为首的3D动画

以上各种动画形式都可以制作出一种类型的动画，那就是**帧动画**，也叫序列帧动画，定格动画，逐帧动画等，这里我们统一用帧动画来表述。

![这里写图片描述](https://user-gold-cdn.xitu.io/2019/3/3/16943ad82dcaf584?w=333&h=305&f=gif&s=459655)


## 应用场景

帧动画一般用来实现稍微复杂一点的动画效果，同时希望动画更细腻，设计师更自由的发挥。他可以定义到每一个时间刻度上的展现内容，我们一般用帧动画来做页面的Loading，小人物，小物体元素的简单动画。我们想象中的帧动画应该有以下几个特点：
1. 可以自由控制播放、暂停和停止
2. 可以控制播放次数，播放速度
3. 可以添加交互，在播放完成后添加事件
4. 浏览器兼容性好

## 素材准备

帧动画的素材一般是先由设计师在PS中的时间轴上设计好了，然后导出图片给前端人员，PS制作时间轴动画一般是用来制作稍微简单的动画，操作简单，方便。

或者是由设计师在AE的时间轴进行设计，因为AE内置了更丰富的动作效果，比如转换，翻转之类的，AE可以帮助我们实现更复杂的效果，然后再导出图片给前端人员。

这里帧动画素材的要求，每一帧的图片最好是偶数宽高，偶数张，最好周围能有一些留白。

## 实现方案

将目前想到的解决方案梳理如下图，同时我们将对每种方案进行详细介绍。
![这里写图片描述](https://user-gold-cdn.xitu.io/2019/3/3/16943ad82d575d70?w=717&h=599&f=png&s=24785)

### 一、GIF图

我们可以将上面制作的帧动画导出成GIF图，GIF图会连续播放，无法暂停，它往往用来实现小细节动画，成本较低、使用方便。但其缺点也是很明显的：

1. 画质上，gif 支持颜色少(最大256色)、Alpha 透明度支持差，图像锯齿毛边比较严重；
2. 交互上，不能直接控制播放、暂停、播放次数，灵活性差；
3. 性能上，gif 会引起页面周期性的**绘画**，性能较差。

### 二、CSS3帧动画

CSS3帧动画是我们今天需要重点介绍的方案，最核心的是利用CSS3中**Animation动画**，确切的说是使用`animation-timing-function` 的阶梯函数 `steps(number_of_steps, direction)` 来实现逐帧动画的连续播放。

帧动画的实现原理是不断切换视觉内图片内容，利用视觉滞留生理现象来实现连续播放的动画效果，下面我们来介绍制作CSS3帧动画的几种方案。

**（1）连续切换动画图片地址src（不推荐）**

我们将图片放到元素的背景中（`background-image`），通过更改 `background-image` 的值实现帧的切换。但是这种方式会有以下几个缺点，所以该方案不推荐。

 - 多张图片会带来多个 HTTP 请求
 - 每张图片首次加载会造成图片切换时的闪烁
 - 不利于文件的管理

**（2）连续切换雪碧图位置（推荐）**

我们将所有的帧动画图片合并成一张雪碧图，通过改变 `background-position` 的值来实现动画帧切换。分两步进行：

**步骤一：** 将动画帧合并为雪碧图，雪碧图的要求可以看上面**素材准备**，比如下面这张帧动画雪碧图，共20帧。
![这里写图片描述](https://user-gold-cdn.xitu.io/2019/3/3/16943ad82b00b280?w=6000&h=300&f=png&s=66440)

**步骤二：** 使用steps阶梯函数切换雪碧图位置

先看写法一：

```js
<div class="sprite"></div>

.sprite {
    width: 300px;
    height: 300px;
    background-repeat: no-repeat;
    background-image: url(frame.png);
    animation: frame 333ms steps(1,end) both infinite;
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

![这里写图片描述](https://user-gold-cdn.xitu.io/2019/3/3/16943ad82d6f8f5d?w=333&h=305&f=gif&s=446114)

问题二：不是应该设置为20步吗，怎么变成了1？

这里我们先来了解下`animation-timing-function`属性。

CSS `animation-timing-function`属性定义CSS动画在每一动画周期中执行的节奏。对于关键帧动画来说，timing function作用于一个关键帧周期而非整个动画周期，即从关键帧开始开始，到关键帧结束结束。

**timing-function 作用于每两个关键帧之间，而不是整个动画。**

接着我们来了解下steps() 函数：

 - steps 函数指定了一个阶跃函数，它接受两个参数。
 - 第一个参数接受一个整数值，表示两个关键帧之间分几步完成。
 - 第二个参数有两个值< start > or < end >。默认值为< end > 。
 - step-start 等同于 step(1, start)。step-end   等同于 step(1, end)。

综上我们可以知道，因为我们详细定义了一个关键帧周期，从开始到结束，每两个关键帧之间分 **1** 步展示完，也就是说0% ~ 5%之间变化一次，5% ~ 10%变化一次，所以我们这样写才能达到想要的效果。


再看写法二：

```js
<div class="sprite"></div>

.sprite {
    width: 300px;
    height: 300px;
    background-repeat: no-repeat;
    background-image: url(frame.png);
    animation: frame 333ms steps(20) both infinite;
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

跟第二种基本一致，只是切换雪碧图的位置过程换成了`transform:translate3d()`来实现，不过要加多一层`overflow: hidden;`的容器包裹，这里我们以只定义初始和结束帧为例，使用**transform**可以开启GPU加速，提高机器渲染效果，还能有效解决移动端帧动画抖动的问题。

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
    animation: frame 333ms steps(20) both infinite;
}
@keyframes frame {
	0% {transform: translate3d(0,0,0);}
    100% {transform: translate3d(-6000px,0,0);}
}
```

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
        window.requestAnimationFrame(drawImg);
    }
    img.onload = function () {
        window.requestAnimationFrame(drawImg);
    }
})();
```
上面是通过改变裁剪图像的X坐标位置来实现动画效果的，也可以通过改变画布上放置图像的坐标位置实现，如下：
`context.drawImage(img, 0, 0, width*k, height,-i*width,0,width*k,height);`。

**（3）通过JS来控制CSS属性值变化**

这种方式和前面CSS3帧动画一样，有三种方式，一种是通过JS切换元素背景图片地址`background-image`，一种是通过JS切换元素背景图片定位`background-position`，最后一种是通过JS移动元素`transform:translate3d()`，第一种不做介绍，因为同样会存在多个请求等问题，不推荐使用，这里实现后面两种。

- 切换元素背景图片位置 `background-position`
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
        window.requestAnimationFrame(changePosition);
    }
    window.requestAnimationFrame(changePosition);
})();
```
- 移动元素背景图片位置 `transform:translate3d()`

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
        window.requestAnimationFrame(changePosition);
    }
    window.requestAnimationFrame(changePosition);
})();
```

## 方案总结

总结以上几种方案，我们可以看到GIF图有一定的优点同时缺点和局限性也比较明显，所以这种方案看情况选择使用。

其他实现方案的性能如何呢，我们来比较一下，如果测试结果出现偏差，可能与测试环境变化有关。

测试环境：

    系统：Windows 10 专业版
    处理器：Intel(R) Core(TM) i7-6700 CPU @ 3.40GHz 3.41GHz
    RAM: 8.00GB
    浏览器：Chrome 72.0
    

![CSS `transform:translate3d()` 方案性能数据](https://user-gold-cdn.xitu.io/2019/3/5/1694df01001cb754?w=902&h=784&f=png&s=72287)

CSS `transform:translate3d()` 方案性能数据

如上图，我们通过Chrome浏览器的各种工具，查看了每种方案的 FPS、CPU占用率、GPU占用、Scripting、Rendering、Painting、内存的使用情况，得到以下数据：

性能-方案 |css`background-position`| css`transform:translate3d()`| JS Canvas | JS`background-position` | JS`transform:translate3d()`|
---|---|---|---|---|---
FPS|60 | 51 | 60 | 60 | 60
CPU| 5%-6.2% | 0.3%-1% | 7%-8% | 6%-8% | 6%-8%
GPU| 3.8MB | 4-10MB | 0 | 3.8MB | 4-11MB
Scripting| 0 | 0 | 2.51% | 2.61% | 3.18%
Rendering| 1.17% | 0.141% | 0.84% | 1.65% | 2.71%
Painting| 1.58% | 0.01%  | 1.63% | 1.75% | 1.05%
内存| 20112K | 21120K | 21588K | 20756K | 21576K

通过分析以上数据我们可以得出以下几点：
1. 除了css `transform:translate3d()` 方案，其他方案的FPS都能达到60FPS的流畅程度，但该方案的FPS也不是很低。
2. CPU占用率最低的方案是 css `transform:translate3d()` 方案。
3. GPU占用最低的方案是 JS Canvas 绘制方案。
4. CSS 方案没有脚本开销
5. Rendering 最少的是 css `transform:translate3d()` 方案。
6. Painting 最少的是 css `transform:translate3d()` 方案。
7. 各方案内存占用区别不大。

**结论**：我们看到，在7个指标中，css `transform:translate3d()` 方案将其中的4个指标做到了最低，从这点看，我们完全有理由选择这种方案来实现CSS帧动画。

至于其他方案的绝对比较暂时没法给出结论，看具体情况来选择，也看开发者对哪个性能指标的追求。

延伸来看我们的Web动画，每种形式的动画都有其各自的有点，比如大量的粒子效果用Canvas绘制方案肯定要比DOM+CSS实现要好的，大量的CSS属性值变换，使用 `transform` 实现性能是要更好的。

## 注意事项
**素材**：动画图片宽高最好是偶数，总帧数最好是偶数，图片拼接处最好有一定的留白。

**适配**：移动端适配最好不用rem，因为rem的计算会造成小数四舍五入，造成一定的抖动效果，建议直接用px作为单位，同时辅助以scale（zoom）媒体查询进行适配。如果使用rem适配，试试使用**transform**的方案，抖动问题可以得到优化解决。

对于帧与帧之间的**盈亏互补**现象导致动画抖动，想要了解更多，可以阅读[《CSS技巧：逐帧动画抖动解决方案》](https://aotu.io/notes/2017/08/14/fix-sprite-anim/index.html)。

**tips**：使用 `will-change` 可以在元素属性真正发生变化之前提前做好对应准备。

## 总结
本文我们主要梳理了目前实现帧动画的几种方案，同时对各种方案进行效果实现，优劣讨论，性能对比，同时简单介绍了帧动画实现过程的注意事项，最后我们得出结论，css `transform:translate3d()` 方案在实现和性能上都明显优于其他方案。

参考来源：

1. [《CSS3动画之逐帧动画》](https://aotu.io/notes/2016/05/17/css3-animation-frame/index.html)
2. [《指尖上行》](https://item.jd.com/25564288988.html)

## 扫一扫关注迅雷前端公众号

![](https://user-gold-cdn.xitu.io/2017/9/18/a61c018adbf0a3e865643c51e91251bb?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
