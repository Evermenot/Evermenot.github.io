---
title: Highcharts使用笔记
date: 2019-03-01 10:38:44
tags: highcharts
categories: [技术, 插件]
---

### 前言

​	本篇博客是个人日常工作中使用highcharts的笔记总结，纯粹是为了在之后工作中如果再次使用到时能快速上手提高开发效率（不适用于他人）。

`注：下面功能的大致思路都是改变图例的配置数据，最后通过highcharts提供的update内置函数来更新并重新渲染图例数据`

### 添加全选、反选功能

```javascript
events: {
    legendItemClick(event) {
        let series = this.chart.series
        if(this.name == '【反选】') {
            series.forEach((item, i) => {
                if(i !== series.length-1) {
                    if(!!series[i].visible) {
                        series[i].update({
                            selected: false,
                            visible: false
                        }, false);
                    }else {
                        series[i].update({
                            selected: true,
                            visible: true
                        }, false);
                    }
                }
            })
        }
        if(this.name == '【全选】') {
            series.forEach((item, i) => {
                if(!series[i].visible) {
                        series[i].update({
                        selected: true,
                        visible: true
                    }, false);
                }
            })
        }
    },
}
```

### tooltip数据排序

需求场景：数据较多时，tooltip中显示的数据没有规律很不明朗，数据分析师看着会很费劲，so产品小姐姐就提出了对tooltip数据进行排序的需求

`记得当时做这个排序费了半天劲＞﹏＜，没有思路无从下手`

```javascript
tooltip: {
    shared: true,
    xDateFormat: this.category, 
    pointFormatter: function() {
        // 该tip数据的排序关键点是 判断当前的第一个图例和最后一个图例
        let series = this.series.chart.series
        let nameArr = []
        series.forEach(val => {
            if(val.visible && val.yData[this.index] !== null && val.name !== '【全选】' && val.name !== '【反选】') {
                nameArr.push(val.name)
            }
        })
        // console.log(nameArr)
        if(this.series.name === nameArr[0]) {
            tipData = []
        }

        let valStr = this.options.y + picData.yUnit
        tipData.push({cate: [this.series.name, valStr], color: this.series.color})
        if(this.series.name === nameArr[nameArr.length - 1]) {
            let tipHtml = '', temp = ''
            for(let m = 0; m < tipData.length; m++) {
                for(let j = 0; j < tipData.length - m - 1; j++) {
                    if( Number(tipData[j].cate[1]) < Number(tipData[j + 1].cate[1]) ) {
                        temp = tipData[j];
                        tipData[j] = tipData[j + 1];
                        tipData[j + 1] = temp;
                    }
                }
            }
            tipData.forEach((val, i) => {
                tipHtml += `
                        <span style="color: ${val.color}">●</span>
                        <span style="color: ${val.color}">${val.cate[0]} : </span>
                        <b> ${val.cate[1]} </b>
                        <br/>
                    `
            })
            return tipHtml
        }
    }
},
```

### 坐标轴简单配置

```javascript
// picData实际的图表配置（形参）
xAxis: {
    categories: picData.xAxis,
    title: {
        enabled: false
    },
    labels: {
        formatter: function() {
            return this.value + picData.picUnit;
        }
    },
    // tickInterval: 10,
    // tickPixelInterval: 3,
},
// y轴配置与x轴类似
```

### x轴坐标显示（密度）自适应

需求场景：以折线为例，当x轴坐标的数组长度较大时，但其中某条线的数据坐标较少时（x轴坐标密集），而数据分析师在单独看这条线时为了更直观，所以需要使坐标点更分散点，就需要降低x轴坐标的密度（反之数据多时则需要加大密度）。

```javascript
let arr = [true, true, true] // 在函数（renderChart）作用域的最上层
events: {
    legendItemClick(event) {
        this.xAxis.tickInterval = 20
        // console.log(this)
        let series = this.chart.series
        let name = this.name
        series.forEach((val, i) => {
            if(val.name == name) {
                if(val.visible === true) {
                    arr[i] = false
                }
                if(val.visible === false) {
                    arr[i] = true
                }
            }
        })
        if(arr[0] && !arr[1] && !arr[2] ) {
            let len = series[0].xData.length
            this.xAxis.update({
                tickInterval: len > 10 ? 2 : 1
            }, true)
        }else {
            this.xAxis.update({
                tickInterval: picData.xAxis.length >= 200 ? parseInt(picData.xAxis.length * 0.1) : 5
            }, true)
        }
    }
},
```



### 图例常用配色

```javascript
// 纯粹个人喜欢
colors: [
    '#7CB5EC', '#2B908F', '#90ED7D', '#F15C80', '#8085E9', '#2F7ED8', '#91E8E1', '#E4D354', 
    '#F7A35C', '#9966cc', '#836FFF', '#6B8E23', '#7CCD7C', '#68228B', '#EE7621', '#A2CD5A',
    '#00B2EE', '#008B00', '#483D8B', '#00CDCD', '#008B8B', '#548B54', '#66CDAA', '#6B8E23',
    '#1C86EE', '#7D26CD', '#5F9EA0', '#8B636C', '#228B22', '#2E8B57', '#698B22', '#43CD80',
]
```

### 折线图完整配置

```javascript
// 少林功夫好&复制粘贴大法好ヾ(≧▽≦*)o
import Highcharts from "highcharts";
require('highcharts/js/modules/exporting.js')(Highcharts);
import '../../../../node_modules/highcharts/css/highcharts.css';
renderChart(dom, picData) {
    let chartJson =  {
            chart: {
                type: picData.type,
                zoomType: 'xy'
            },
            title: {
                text: picData.title,
                style: {
                    fontSize: '14px',
                    fontWeight: '700'
                }
            },
            xAxis: {
                categories: picData.xAxis,
                title: {
                    enabled: false
                },
            },
            yAxis: {
                min: 0,
                title: {
                    enabled: false,
                    // text: '曝光量'
                },
                labels: {
                    formatter: function() {
                        return this.value + picData.yUnit;
                    }
                },
            },
            exporting: {
                enabled: true,
                width: 690,
                height: 460,
            },
            tooltip: {
                shared: true,
                xDateFormat: this.category, 
                pointFormatter: function() {
                    return `
                        <span style="color: ${this.series.color}">●</span>
                        <span style="color: ${this.series.color}">${this.series.name} : </span>
                        <b> ${this.options.y + picData.yUnit} </b>
                        <br/>
                    `;
                }
            },
            plotOptions: {
                spline: {
                    dataLabels: {
                        enabled: false,
                        verticalAlign: 'top',
                        y: -24  // 数值上下偏移
                    },
                    events: {
                        legendItemClick(event) {
                            let series = this.chart.series
                            if(this.name == '【反选】') {
                                series.forEach((item, i) => {
                                    if(i !== series.length-1) {
                                        if(!!series[i].visible) {
                                            series[i].update({
                                                selected: false,
                                                visible: false
                                            }, false);
                                        }else {
                                            series[i].update({
                                                selected: true,
                                                visible: true
                                            }, false);
                                        }
                                    }
                                })
                            }
                            if(this.name == '【全选】') {
                                series.forEach((item, i) => {
                                    if(!series[i].visible) {
                                            series[i].update({
                                            selected: true,
                                            visible: true
                                        }, false);
                                    }
                                })
                            }
                        },
                    }
                },
            },
            series: picData.seriesArr,
            credits: {
                enabled: false
            },
            colors: [
                '#7CB5EC', '#2B908F', '#90ED7D', '#F15C80', '#8085E9' '#E4D354', 
                '#F7A35C', '#9966cc', '#836FFF', '#6B8E23', '#EE7621', '#A2CD5A',
            ]
        }
    return new Highcharts.chart(dom, chartJson);
}
```

### 性能提升模块

Highcharts 性能提升模块（[boost.js](https://github.com/highcharts/highcharts/blob/master/js/modules/boost.src.js)） 是官方发布的用于提升性能的模块，可以轻松的让 Highcharts 加载十万、百万级的数据