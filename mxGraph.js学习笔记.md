# mxGraph.js 学习笔记



# 目录

* 介绍
* 起步
  * 安装



# 介绍

> mxGraph是一个强大的JavaScript图表库，可以快速创建交互式图形和图表应用程序。



# 起步

以下笔记都是结合Vue+Typescript的基础上记录

## 安装

使用yarn安装mxgraph

```
yarn install mxgraph
```

使用yarn安装mxgraph Typings

*mxgraph-typings*s是开源社区爱好者提供一个非官方包，作者为[lgleim](https://github.com/lgleim/mxgraph-typings)  

```
yarn add -D lgleim/mxgraph-typings
```

引入mxgraph

```typescript
// mxgraph/index.ts       
import mx from 'mxgraph'

const mxgraph = mx({
  // 相对于vue cli 生成的public下的mxgraph
  mxBasePath: '/mxgraph' 
})

export default mxgraph

```

放置mxgraph静态文件, 静态文件可以在mxgraph 仓库地址下载

```
├── public
│   ├── mxgraph              
│   |	├── css     
│   |	├── images     
│   |	├── resources     
```

在Vue component里面使用


```vue
<template>
	<section
         class="mj-editor-canvas"
         ref="editor"
         >
    </section>
</template>
```

```typescript
  // methods
  initGraph(): void {
    const container = this.$refs.editor
    const graph = new mx.mxGraph(container as Element)
    this.graph = graph
    // 设置图形节点直接的连接
    graph.setConnectable(true)
    // 开启图形框选
    // tslint:disable-next-line:no-unused-expression
    new mx.mxRubberband(graph)
    const parent = graph.getDefaultParent()
    // 事务确保图形插入完整
    graph.getModel().beginUpdate()
    try {
      const v1 = graph.insertVertex(parent, null, 'Hello,', 20, 20, 80, 30)
      const v2 = graph.insertVertex(parent, null, 'World!', 200, 150, 80, 30)
      const e1 = graph.insertEdge(parent, null, '', v1, v2)
    } finally {
      graph.getModel().endUpdate()
    }
  }
  
  mounted() {
    this.initGraph()
  }
```







