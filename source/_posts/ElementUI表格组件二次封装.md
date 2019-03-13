---
title: ElementUI表格组件二次封装
date: 2019-03-01 14:11:03
tags: [element-ui, vue]
categories: [技术, vue]
---
### 前言

​	在做技术栈为vue + ElementUI的项目（多为后台管理平台）时，经常涉及到数据表格的开发，当然ElementUI中的表格组件已经封装的非常完美，但是在使用el-table时常常还是需要每一列每一列的手动配置，很机械很不想做ne要呕吐🤮＞﹏＜，所以为了解决这一痛点，最后对表格组件进行了二次封装，尽量达到所有列的可配置化。

### 表格组件

#### 代码

```javascript
<template>
    <div>
        <slot name="link"></slot>
        <el-table :data="tData" size="small" border>
            <template v-for="(val, idx) in tCols">
                <!-- 序号列 -->
                <el-table-column v-if="val.type" :key="idx" :type="val.type" :label="val.label" :fixed="val.fixed" align="center"></el-table-column>

                <!-- 自定义列插槽 -->
                <slot v-else-if="val.slot" :name="val.slot"></slot>

                <el-table-column v-else :key="idx" :label="val.label" :fixed="val.fixed" :width="val.width" align="center">
                    <template slot-scope="scope">
                        <!-- 链接 -->
                        <a :class="alignFnc(val.align)" v-if="val.href" :href="val.href(scope.row)" target="_blank" style="color:#409EFF">{{scope.row[val.prop]}}</a>

                        <!-- 数组 -->
                        <el-tooltip v-else-if="Array.isArray(scope.row[val.prop])" class="item" effect="dark" placement="top">
                            <div slot="content">
                                <p style="margin-bottom: 4px" v-for="(item, index) in scope.row[val.prop]" :key="index">{{ item }}</p>
                            </div>
                            <span class="tip-sty">{{ scope.row[val.prop].length }}</span>
                        </el-tooltip>

                        <span :class="alignFnc(val.align)" v-else > {{ scope.row[val.prop] | filterVal}} </span>
                    </template>
                </el-table-column>
            </template>
        </el-table>
    </div>
</template>

<script>
    // 表格组件
    export default {
        props: {
            tData: Array,
            tCols: Array
        },
        data() {
            return {}
        },
        methods: {
            alignFnc(align) {
                let className = ''
                switch(align) {
                    case 'left': className = 'left-sty'; break;
                    case 'right': className = 'right-sty'; break;
                }
                return className;
            }
        },
        filters: {
            filterVal(val) {
                if(val === null || val === '' || val === undefined) {
                    return '-'
                }else {
                    return val
                }
            }
        },
    }
</script>

<style scoped lang="less">
.tip-sty {
    cursor: pointer;
    color:#409EFF;
}
.left-sty {
    display: inline-block;
    text-align: left;
    width: 100%;
}
.right-sty {
    display: inline-block;
    text-align: right;
    width: 100%;
}
.qASty {
    color: #409eff;
    cursor: pointer;
}
</style>
```

#### 使用

```javascript
<table-com v-loading="tloading" :tData='tData' :tCols='tCols'>
    <template slot="media_name">
        <el-table-column label="媒体名称" fixed align="center">
            <template slot-scope="scope">
                <a class="left-sty" :href="'http://kuaibao.qq.com/s/MEDIANEWSLIST?chlid='+scope.row.media_id" target="_blank" style="color:#409EFF">{{ scope.row.media_name }}</a>
            </template>
        </el-table-column>
    </template>
</table-com>
```

#### 配置

```javascript
this.tCols = [
    {label: '序号', type: 'index', fixed: true},

    {label: '媒体名称', prop: 'media_name', slot: 'media_name', fixed: true},
    
    {label: '媒体ID', prop: 'media_id', fixed: true},
    {label: '原始媒体分', prop: 'init_media_level'},
    {label: '主低质类型', prop: 'low_type'},
    {label: '总低质文章数', prop: 'low_news_num'},
]
```

#### 效果

![img](/images/elementui/elementui.png)