---
title: canvas绘制心形
date: 2019-01-11 14:11:03
tags: [canvas, 折腾]
categories: [技术, canvas]
---

### 前言

- **期望效果：**

  在鼠标点击位置绘制图形--心形

- **错误做法：**（引以为戒...）

  使用canvas API中的translate，分别以鼠标点击位置的offsetX、offsetY为画布坐标的水平和竖直方向的偏移量，绘制图形，但实际效果和预期完全不太一样（大哭~~~）

- **错误原因：**

  translate是以上个坐标原点为基础，进行平移             （原谅我的无知\~~~）

- **解决错误：**

  在开始绘制前，将当前状态使用save()保存下来，并在每一次绘制结束之后，使用restore()将状态重置，以免影响下次点击（理论上用鼠标点击位置减去画布上次的原点位置应该也可以，但是比较繁琐）

- **最后吐槽下：**

  有很多文章在介绍相同效果的绘制，但是真的有自测过吗。。。

记得去年自己还有用canvas画过挺炫酷的饼状图来着，( ⊙ o ⊙ )果然作为一枚渣渣还得脚踏实地的板砖啊\~~

[效果演示](https://jsfiddle.net/Evermenot/y3mz8opn/)

**极坐标方程：**

![img](/images/canvas/heart.png)

### 代码

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>heart--equation</title>
    <style>
        * {
            margin: 0;
            padding: 0;
        }
    </style>
</head>
<body>
　　　 <!-- 默认宽高300*150 -->
　　　 <!-- style方式设置canvas宽高会在默认宽高的基础上进行拉伸，导致变形 -->
    <canvas></canvas>
</body>
<script>
    (function(win) {
        let Heart = function(ctx, x, y, size) {
            this.ctx = ctx;
            this.x= x;
            this.y = y;
            this.size = size;
            this.vertices = getVector();
            
            function getVector() {
                let vertices = [];

                // console.log(this == win) //true　
                for(let i = 0; i < 50; i++) {
                    let step = i/50 * (Math.PI * 2);
                    let vector = {
                        x : size * (16 * Math.pow(Math.sin(step), 3)),
                        y : size * (13 * Math.cos(step) - 5 * Math.cos(2 * step) - 2 * Math.cos(3 * step) - Math.cos(4 * step))
                    }
                    vertices.push(vector);
                }

                return vertices;
            };
        }

        Heart.prototype.draw = function() {
            this.ctx.save();
            this.ctx.beginPath();
            this.ctx.translate(this.x, this.y);
            this.ctx.rotate(Math.PI);

            let vertices = this.vertices
            for(let i = 0; i < vertices.length; i++) {
               let vector = vertices[i];
                this.ctx.lineTo(vector.x, vector.y);
            }

            // this.ctx.fillStyle = 'hotpink';
            // this.ctx.fill();

            this.ctx.strokeStyle = "hotpink";
            this.ctx.stroke();
            this.ctx.closePath();

            this.ctx.restore();
        };
        win.Heart = Heart;

    })(window)
</script>
<script>
    let canvas = document.querySelector('canvas');
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    let ctx = canvas.getContext('2d');
    canvas.onclick = function(e) {
        let size = 6;
        let x = e.offsetX;
        let y = e.offsetY;
        let heart = new Heart(ctx, x, y, size);
        heart.draw();
    }
</script>
</html>
```

