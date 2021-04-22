---
title: canvas绘制星空
date: 2019-01-19 17:01:17
tags: [canvas, 折腾]
categories: [技术, canvas]
---

### 前言

​	没事的时候在看些canvas方面的文章，并写了一两个小demo。。下面的代码就是其中之一。感兴趣的先去看小姐姐的原文吧，从canvas基础讲起且非常详细，真的很不错o.o　小姐姐好像是饿了么的大神来着。。。仰望~~[尊重原创](https://juejin.im/post/5986d6e1f265da3e241e8cdb) ；如果你只想要看代码，个人感觉自己写的要比小姐姐的原码清晰简洁点（最下面）。

### 效果图

说那麽多废话干嘛。。。有图就别哔哔哔

![img](/images/canvas/sky.webp)

**总结：**（写在前面吧）

- 在使用缓存Canvas优化Star移动时，我的做法是在cache方法中创建缓存画布绘制完star将画布对象return出来，初衷是为了降低代码的复杂度，将代码模块化；但是好像动画更卡了（应该是和每次都创建画布有关...\~\~\~\~(>\_<)\~\~\~\~），最后还是和原代码一样在全局上创建缓存画布（这样写动画效果很流畅，唉之后再想想如何简化代码写法吧^_^）。。

**一点建议：**（对自己说的）

- 在借鉴和学习别人的案例时，最好将整体效果或功能,划分成各个小块功能去逐一实现；对于某一小块功能的实现最好先自己思考如何实现（反正也不是让你一下想好实现全部功能，感觉原作者自己写的时候也不是一蹴而就的吧），功能实现后再和原代码相互印证（不同思想的碰撞bulabula。。。）学习大多数知识时也多是如此吧。


- 刚开始敲代码的时候，总是想着如何快速实现全部效果（干巴巴的，麻麻赖赖的，一点都不圆圆润，盘他~~~^_^），没有用自己掌握的canvas基础知识去想如何实现功能，反而在很多地方卡壳了（不是很关键的地方纠结）花费的时间更长了；有疑问的地方，可以先把疑问找个地方记下来（比如：' 小本本 '。。。），等功能全部实现再回过头来解决掉'小本本'上记得内容。疑问： clientX、layerX、offsetX、pageX、screenX、x 的区别？。。。


- 之前一直觉得只要把产品方提的需求做出来就大吉大利今晚吃鸡了，即使是之前没做过的或者不懂的（不要紧，可以搬前辈大神们的嘛...），实际工作中遇到问题多是如此，这样功能或效果同样达到了要求（也仅仅是达到要求），但自己并没有规划如何开发，导致所写的代码真的是不忍直视（很za乱~~），因为多数有点东平西凑的嫌疑，感觉不是自己在驭使代码，更多的是个被驾驭的角色...希望自己在以后能多花点时间去规划如何去做，戒骄戒躁a le。。。


### 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>canvas--again</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            overflow: hidden;
        }
        html,body {
            width: 100%;
            height: 100%;
            /* background: linear-gradient(to top, palevioletred 0%, #fff 100%); */
            background: no-repeat linear-gradient(to bottom, #dcdcdc 0%, #000 100%);
        }
    </style>
</head>
<body>
    <canvas id="canvas"></canvas>
</body>
<script>
    function init(win, doc) {
        let maxW = win.innerWidth;
        let maxH = win.innerHeight;
        const maxSize = 5;
        const minSize = 1;
        let isMoving = false;
        let timer = null;
        let animateID = null

        let canvas = doc.getElementById('canvas');
        canvas.width = maxW;
        canvas.height = maxH;
        let ctx = canvas.getContext('2d');
        let stars = [];      // 存放作为背景使用的星星
        let move_stars = []; // 存放鼠标移动时绘制的星星

        function CanvasStar(num) {
            this.num = num;
        };

        CanvasStar.prototype = {
            render() {
                for (let i = 0; i < this.num; i++) {
                    let alpha = Math.random() + 0.1;
                    stars[i] = new Star(i, getOneRandom(maxW), getOneRandom(maxH), getOneRandom(maxSize, minSize), true, alpha);
                }
                animate();
            }
        };

        function Star(id, x, y, r, isCache, dot_alpha) {
            this.id = id;
            this.x = x;
            this.y = y;
            this.r = r;
            this.dot_alpha = dot_alpha;
            this.show = .5;    // 作用：控制 鼠标绘制点的隐藏
            this.direct = getOneRandom(180) + 180;
            this.isCache = isCache;
            this.cacheCanvas = doc.createElement('canvas');
            this.ctx_cache = this.cacheCanvas.getContext('2d');
            if(isCache) {
                this.cache();
            }
        };

        Star.prototype = {
            draw() {
                // 绘制一个Star
                if(!this.isCache) {
                    let alpha = Math.random() + 0.1;
                    ctx.save();
                    ctx.beginPath();
                    ctx.arc(this.x, this.y, this.r, 0, 2 * Math.PI, false);
                    ctx.closePath();
                    
                    ctx.shadowColor = '#fff';
                    ctx.shadowBlur = 2 * this.r;
                    ctx.fillStyle = `rgba(255, 255, 255, ${ this.dot_alpha })`;
                    ctx.fill();
                    ctx.restore();
                }else {
                    ctx.drawImage(this.cacheCanvas, this.x - 3 * this.r, this.y - 3 * this.r);
                }
            },

            move() {
                this.y -= 0.25;
                if(this.y < -10) {
                    this.y = maxH + 10;
                }
                this.draw();
            },

            // 使鼠标绘制的点抖动起来
            shake(arr) {
                this.show -= 0.01;
                if(this.show < 0) {
                    return;
                }
                let speed = .5;
                this.x += Math.cos(this.direct * Math.PI / 180) * speed;
                this.y += Math.sin(this.direct * Math.PI / 180) * speed;
                this.draw();
                this.link();
            },

            link() {
                if(!this.id) return;
                let len = move_stars.length;
                // 关键思想：取当前id，之前的4个点，每绘制一次就向前取4个点，以此类推
                // 而不是move_stars最后的四个点，否则的话只有最后几个点会被连接起来
                let arr = move_stars.slice(this.id - 3, this.id);
                let endIdx = arr.length - 1;

                ctx.save();
                for(let i = endIdx; i >= 0; i--) {
                    if(i == endIdx && !!arr[endIdx]) {
                        ctx.moveTo(arr[endIdx].x, arr[endIdx].y);
                        ctx.beginPath();
                        ctx.lineTo(this.x, this.y);
                    }
                    if(i != endIdx && !!arr[i] && arr[i].show > 0) ctx.lineTo(arr[i].x, arr[i].y);
                }
                ctx.closePath();

                ctx.strokeStyle = 'rgba(255, 255, 255, 0.125)';
                ctx.stroke();
                ctx.restore();
            },

            cache() {
                this.cacheCanvas.width = 6 * this.r;
                this.cacheCanvas.height = 6 * this.r;

                this.ctx_cache.save();
                this.ctx_cache.beginPath();
                this.ctx_cache.arc(this.r * 3, this.r * 3, this.r, 0, 2 * Math.PI, false);
                this.ctx_cache.closePath();

                this.ctx_cache.shadowColor = '#fff';
                this.ctx_cache.shadowBlur = 2 * this.r;
                this.ctx_cache.fillStyle = `rgba(255, 255, 255, ${this.dot_alpha})`;
                this.ctx_cache.fill();
                this.ctx_cache.restore();
            }
        };

        // 动画
        function animate() {
            ctx.clearRect(0, 0, maxW, maxH);
            cancelAnimationFrame(animateID);
            let len = stars.length;
            for(let i = 0; i < len; i++) {
                stars[i].move();
            }

            let len2 = move_stars.length;
            if(isMoving) {
                for(let i = 0; i < len2; i++) {
                    if(move_stars[i]) move_stars[i].shake(move_stars);
                }
            }else {
                move_stars = []
            }
            animateID = requestAnimationFrame(animate);
        };

        // 获取区间内的随机数
        function getOneRandom(max, min = 0) {
            return Math.floor(Math.random() * (max - min) + min);
        };

        // 获取正负号
        function getSign() {
            return Math.random() > .5 ? -1 : 1;
        };

        // 鼠标移动事件
        doc.addEventListener('mousemove', function(e) {
            let x = e.clientX, y = e.clientY;
            // 控制绘制密度 以及点之间的距离    两个重要的点
            // 密度是控制绘制的数量    dis_x = Math.abs(x - pre_star.x);
            // 距离是在已绘制的点基础上、控制点的间距

            // 控制绘制密度 和 控制点之间的距离  不是一个功能哦（需要实际操作去体会， 文字很难表述~.~）
            // 没有控制距离的话 绘制的图形，太规则了，不够分散    x = x + getSign() * getOneRandom(50)

            let len = move_stars.length;
            let dis_x, dis_y;
            if(!len) {
                move_stars.push( new Star(len, x, y, getOneRandom(maxSize, 3), true, 1) );
            }

            if(move_stars[len - 1]) {
                // 当前的星还没有push到move_stars里，所以上个星是move_stars[len - 1]
                let pre_star = move_stars[len - 1];
                if(pre_star) {
                    dis_x = Math.abs(x - pre_star.x);
                    dis_y = Math.abs(y - pre_star.y);
                }
                x = x + getSign() * getOneRandom(50);
                y = y + getSign() * getOneRandom(50);
                if(dis_x > 5 && dis_y > 5) move_stars.push( new Star(len, x, y, getOneRandom(maxSize, minSize), true, 1) );
            }

            isMoving = true;
            clearInterval(timer);   // 清除上一次的定时器（此时还没触发）
            timer = setInterval(function() {
                isMoving = false;
                clearInterval(timer);    // 鼠标停止再清除下定时器
            }, 500)

        }, false);

        win.CanvasStar = CanvasStar;
    };
</script>
<script>
    init(window, document)
    let canvasStar = new CanvasStar(200);
    canvasStar.render();
    window.addEventListener('resize', function(e) {
        for(let k in canvasStar) {
            delete canvasStar[k]
        }
        init(window, document)
        let canvasStar2 = new CanvasStar(200);
        canvasStar2.render();
    }, false)
</script>


<script>
/**小本本
 * 疑问：
 *   1. clientX、layerX、offsetX、pageX、screenX、x 的区别？
*/
</script>
</html>
```

