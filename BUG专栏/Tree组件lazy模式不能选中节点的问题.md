# 解决element-ui Tree组件lazy模式不能选中节点的问题

### 需求

小伙伴他们公司是做区块链产品的，近日在公司的项目中有一个需求在实现过程中出现一些了bug，向我求助。具体需求如下： 

* 使用element-ui 组件库的tree实现区块链上下级的关系展示

* 要求tree组件可以进行懒加载，这样可以不用把所有数据一次性查出来

* 可以通过区块链地址或其他参数查询到区块链数里面的某个节点并**选中展开**

需求总结: 使用tree组件实现可以懒加载，可以查询某个节点并选中展开查询到的节点

通过需求的分析实现如下demo

![demm](https://github.com/lemontree2000/blog-note-image/blob/master/images/2019-12-11-bug%E4%B8%93%E6%A0%8F-element-ui%20tree.png?raw=true)


### 背景

element-ui组件库tree组件已经是一个很完善的控件，用它来在展示基本上没有上面太大问题，而且通过lazy模式可以很方便的实现懒加载，但是问题就在于要实现查询选中展开与懒加载同时存在。可能也有人说选中和展开组件内部不是已经暴露了api吗，直接使用就可以呀，而实际情况正是因为使用了这些api还是有问题。

tree的第一层数据是在页面初始化的时候就请求到位了，我们通过输入查询的key点击查询这时我们会访问后台的查询接口，后台返回的数据如下
```json
{
  "address": "f6179d96f7d0f05e4955ea98ceeb06a2170ca701",
  "parentAddressList": "2483a965053e24c54f3716c8e2a6b890f2128a34,d69cbe3e0a8066d961fe9e6cef377a6677da5bff,d5eccfa2d9070fed8fbdf3afa7106b085c88783c",
  "perantList": [
    [
      {
        "address": "f6179d96f7d0f05e4955ea98ceeb06a2170ca701",
        "nickname": "北北哥",
        "name": "北北哥"
      },
      ...
    ],
    [
      {
        "address": "2483a965053e24c54f3716c8e2a6b890f2128a34",
        "nickname": "痞子会长",
        "name": "痞子会长"
      },
	  ...
    ],
    [
      {
        "address": "d69cbe3e0a8066d961fe9e6cef377a6677da5bff",
        "nickname": "冉冉升起6",
        "name": "冉冉升起6"
      },
      ...
    ]
  ],
  "retCode": "1",
  "retMsg": "成功"
}

```

假设搜索的为北北哥的信息，则currentkey为`f6179d96f7d0f05e4955ea98ceeb06a2170ca701`

`address `是查询到区块的key即北北哥的key: `f6179d96f7d0f05e4955ea98ceeb06a2170ca701`

`parentAddressList`是当前查询的区块的父子关系链key的映射，从一级父级到最上级，如：痞子会长->冉冉升起6->根节点

`perantList`是每一级的兄弟元素的数组

### 实现

#### Part1

通过数据一般人很快就会有解决方案，而我也是如此，我马上就想出了一个常规方案，而往往常规方案能解决问题的话，就这篇文章了 😂

常规方案的思路

1. 将数据循环组装成完整树的结构，放在相应的节点上面,最后赋值tree的data属性
2. 通过tree组件setCurrentKey进行选中和赋值default-expanded-keys进行展开
实现以下代码

```vue
<template>
  <div style="text-align:center; padding:20px;border:1px solid #ccc;min-height:620px;">
    <el-form :inline="true" :model="formInline" class="demo-form-inline" ref="formInline">
      <el-form-item label="currentKey">
        <el-input v-model="currentKey" placeholder="currentKey"></el-input>
      </el-form-item>
      <el-form-item label="用户名">
        <el-input v-model="formInline.user" placeholder="用户名"></el-input>
      </el-form-item>
      <el-form-item label="钱包地址">
        <el-input v-model="formInline.address" placeholder="钱包地址"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">查询</el-button>
      </el-form-item>
    </el-form>
    <el-row>
      <el-col :span="8">
        <el-tree
          :data="treeList"
          ref="tree"
          style="width:100%;"
          :props="props"
          :load="loadNode"
          lazy
          node-key="address"
          empty-text="数据为空"
          :default-expanded-keys="defaultexpendphoto"
          :expand-on-click-node="false"
          :highlight-current="true"
          :default-expand-all="showAll"
          @node-click="handleNodeClick"
        ></el-tree>
      </el-col>
      <el-col :span="16">
        <div>展示内容区域</div>
        <div>{{defaultexpendphoto}}</div>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "HelloWorld",
  props: {
    msg: String
  },
  data() {
    const _this = this;
    return {
      treeList: [],
      showAll: null,
      currentKey: 2,
      formInline: {
        user: "",
        number: "",
        address: ""
      },
      rootNode: [],
      props: {
        label: "name",
        children: "children",
        isLeaf: "leaf"
      },
      defaultexpendphoto: []
    };
  },
  methods: {
    getNextDirectoriesData(node, resolve) {
      return resolve([
        {
          id: 12,
          name: "一级经销商-lazy",
          level: 2,
          islevel: true,
          isquery: true
        }
      ]);
    },
    loadNode(node, resolve) {
      if (node.level === 0) {
        this.rootNode = node;
        return resolve(this.treeList);
      } else {
        setTimeout(() => {
          this.getNextDirectoriesData(node, resolve);
        }, 500);
      }
    },
    async onSubmit() {
      const res = await axios.get(
        "https://easy-mock.bookset.io/mock/5def6b2fe9b44969dc0734a0/example/treeList"
      );
      const { parentAddressList, perantList } = res.data.data;
      const parentArr = parentAddressList.split(",");
      let treeArr = [];
      perantList.map((item, index) => {
        if (index === 0) {
          treeArr = item;
        }
        item.map(v => {
          v.name = v.nickname;
          if (v.address === parentArr[index - 1]) {
            v.children = treeArr;
            treeArr = item;
          }
        });
      });

      const newArr = this.treeList.map((item, i) => {
        if (item.address === parentArr[parentArr.length - 1]) {
          return { ...item, children: treeArr };
        } else {
          return item;
        }
      });
	  this.treeList = newArr
      this.$nextTick(() => {
        this.defaultexpendphoto = [this.currentKey];
        this.$refs.tree.setCurrentKey(this.currentKey);
      });
    }
  }
};
</script>

```

问题： 通过此种方法将组装后的treeList 重新赋值，但是组件并没有按照设定的样子进行展开和选中，说明这种方法是有缺陷的。

#### 问题排查

因为我对treeList进行重新赋值了，所以首先我对数据进行了跟踪，使用Vue devTools查看了组件内容的数据状态，发现treeList已经是我们组装后的数据，tree组件内部也已经有了最新数据。

紧接着查看tree组件内部的currentKey发现是null，说明我们对currentKey的设置是没有成功的，这时候就有整个疑问了“内部已经有完整的数据,currentKey为什么没有设置成功”,接下来我对setCurrentKey源码进行了翻看，发现了一些细节。setCurrentKey是通过调用实列化内部一个store的setCurrentNodeKey的方法

![bug](https://github.com/lemontree2000/blog-note-image/blob/master/images/2019-12-12-bug%E4%B8%93%E6%A0%8F-element-ui%20tree2.png?raw=true)

![img](https://github.com/lemontree2000/blog-note-image/blob/master/images/2019-12-12-bug%E4%B8%93%E6%A0%8F-element-ui%20tree3.png?raw=true)

而setCurrentNodeKey内部会先使用key去查找当前要设置的节点，所有节点都通过node类进行实列化，getNode回去nodesMap里面去找所有节点key的映射，所有进行实列化的节点都存了一份节点和nodekey在这里

![img](https://github.com/lemontree2000/blog-note-image/blob/master/images/2019-12-12-bug%E4%B8%93%E6%A0%8F-element-ui%20tree4.png?raw=true)

问题就在于tree组件内容并没有为我们搜索的数据进行node实列化，nodesMap里面就没有这个节点，所以无法设置当前选中节点，后面我把tree的数据写死进行验证，发现所有lazy模式下都只会对第一层的数据进行node实例化。所以这种常规思路解决不了这个问题的。

#### Part2

要想在lazy模式下实现里面层级的选中展开就必须让里面的层级进行主动渲染，那有什么方向进行主动渲染呢。我查看了一下树组件的api列表在其中找到了`updateKeyChildren`这个api，通过指定key来设置子元素，我觉的用这个api可能可以实现，所以我就去看`updateKeyChildren`内部实现，是通过调用store里面的updateChildren，这个方法会将要更新子元素的节点原来的children进行删除，在内部append方法添加新的节点，append会对每个节点进行实例化。 那说明这个方法理论上是可以实现主动渲染的。

![](https://github.com/lemontree2000/blog-note-image/blob/master/images/2019-12-12-bug%E4%B8%93%E6%A0%8F-element-ui%20tree5.png?raw=true)

接下来就开始用这个方法实现了， 因为后台已经返回了每个父元素的key和子元素的集合，所以只要往后遍历数据设置children后，再设置currentKey 和default-expanded-keys。所以我只需要改变一下搜索方法就可以了

```js
    async onSubmit() {
      const res = await axios.get(
        "https://easy-mock.bookset.io/mock/5def6b2fe9b44969dc0734a0/example/treeList"
      );
      const { parentAddressList, perantList } = res.data.data;
      const parentArr = parentAddressList.split(",");
      let treeArr = [];
      for (let i = parentArr.length - 1; i > -1; i--) {
        console.log("key", parentArr[i]);
        console.log("children", perantList[i]);
        this.$refs.tree.updateKeyChildren(parentArr[i], perantList[i]);
      }
      this.$nextTick(() => {
        this.defaultexpendphoto = [this.currentKey];
        this.$refs.tree.setCurrentKey(this.currentKey);
      });
    },
```

测试了一下果然实现需求，搜索后可以执行展开和选中的key，如下图

![](https://github.com/lemontree2000/blog-note-image/blob/master/images/2019-12-12-bug%E4%B8%93%E6%A0%8F-element-ui%20tree6.gif?raw=true)

至此，这个问题就彻底搞定了，在线demo地址：[点击](<https://codepen.io/lemontree2000/pen/VwYeavR>)

