---
layout: post
title: 讲讲 React Flow
---
React Flow (RF) 的[官方文档]写得非常细，给出了很多可以直接在 CodeSandbox 上面打开的例子。这个分享涵盖的内容仅为 RF Docs 的一个**子集**，当中混进了一些我个人的理解，因此感兴趣的小伙伴可以结合官方文档深度学习！

### 什么是 React Flow
React Flow 是一个前端的组件库，官方给出的定义是 “a highly **customizable** React component for building **node-based** editors and **interactive diagrams**”。个人觉得这个定义非常精妙，精准地概括出了 RF 的优点和特点：可定制化、基于节点、交互流程图。
以下是官网上的一些效果图。

![](../assets/blog-react-flow/img.png)

![](../assets/blog-react-flow/img_1.png)

![](../assets/blog-react-flow/img_2.png)

和 RF 功能相似的其它组件库主要有 react-diagrams， beautiful-react-diagrams，butterfly 等，不过他们的 Github Star 都没有 RF 多。
这说明在一定程度上 **RF 是一个受更多开发者青睐的组件库**。

![](../assets/blog-react-flow/img_3.png)

有 11.2k 的人关注了这个库，最近的一次 commit 来自于 14 小时前，他们的 contributors 会在 issues 里面回答各式各样的问题，这些都是非常不错的加分项 👍

👇 树也长得很好看
![](../assets/blog-react-flow/img_5.png)

### 如何使用 React Flow
1. 节点与连线 —— 你所需要知道的最少知识
React Flow 的所有图形都是由节点（和连线）组成的。
   一个最简单的节点在代码里长这样 👇
```jsx
{
  id: "1", // unique id
  data: { label: "节点上面的文字" }, // 这里还可以加一些我们自己的参数进去
  position: { x: 0, y: 0 }, // 节点的位置
}
```
一个最简单的连线在代码里长这样 👇
```jsx
{
  id: "e1-2", // unique id
  source: "1", // 出发节点的 id
  target: "2", // 目标节点的 id
}
```
以上这些属性都是节点和**连线**必须拥有的属性。
有了最基本的节点（和连线），我们就可以把他们以**数组**的形式传给 `ReactFlow` 这个组件，然后我们就能拥有一个最简单的图了。

提问：下面这段代码运行出来会是一个什么样的图形？
```jsx
import ReactFlow from 'react-flow-renderer';

const initialNodes = [{
  id: "1",
  data: { label: "我是唯一一个节点" },
  position: { x: 0, y: 0 },
}];
const initialEdges = [];

const Flow = () => {
  const [nodes, setNodes] = useState(initialNodes);
  const [edges, setEdges] = useState(initialEdges);

  return <ReactFlow nodes={nodes} edges={edges} />;
};

export default Flow;

```

2. 状态管理 —— 酷炫的 helper hooks
默认情况下 RF 不会更新内部 nodes 和 edges 的状态。如果要实现像“选中”、“拖拽”、“移除”这样的功能，我们需要通过写 `onNodesChange` 和 `onEdgesChange` 来告诉 RF 当这些变化发生时需要做些什么。

*“By default React Flow doesn't do any internal state updates besides handling the viewport when you set up a controlled flow. As with an `<input />` component you need to pass handlers to apply the changes that are triggered by React Flow to your nodes and edges. In order to select, drag and remove nodes and edges you need to implement an onNodesChange and an onEdgesChange handler.”*

`onNodesChange` 和 `onEdgesChange` 的实现本质上还是借助 RF 为我们提供的 Util Functions：`applyNodeChanges` 和 `applyEdgeChanges`。
```jsx
  const onNodesChange = useCallback(
    changes => setNodes((ns) => applyNodeChanges(changes, ns)),
    []
  );
  const onEdgesChange = useCallback(
    changes => setEdges((es) => applyEdgeChanges(changes, es)),
    []
  );
```
为了方便开发者们偷懒，RF 的作者们专门为我们提供了 hooks，直接一步到位。
```jsx
const [nodes, setNodes, onNodesChange] = useNodesState(initialNodes);
const [edges, setEdges, onEdgesChange] = useEdgesState(initialEdges);

// 不需要自己写 onNodesChange 亦或是 onEdgesChange 啦
...

return <ReactFlow 
  nodes={nodes}
  edges={edges}
  onNodesChange={onNodesChange}
  onEdgesChange={onEdgesChange}
/>;

...
```

React Flow 为我们提供了一些非常有用的辅助函数：
- 判断：`isNode`, `isEdge`
- 维护状态：`applyNodeChanges`, `applyEdgeChanges`
- 添加/更新连线：`addEdge`, `updateEdge`
- 获得和节点相关的连线：`getOutgoers`, `getIncomers`, `getConnectedEdges`

现在节点和连线的增删改查都变得简单起来了~
```jsx
// 添加连线 (onConnect 需要作为参数传给 ReactFlow)
const onConnect = connection => setElements((es) => addEdge(connection, es));

// 删除连线
onEdgesChange([{ id: edgeToDeleteId, type: "remove" }]);

// 添加节点
setNodes(nodes.concat(newNode));

// 删除节点 (需要把和节点相关的连线一起删除）
const edgesToDelete = getConnectedEdges(nodeToDeleteId);
onNodesChange([{ id: nodeToDeleteId, type: "remove" }]);
onEdgesChange(edgesToDelete.map(e => ({ id: e.id, type: "remove" })));
```

3. 自定义的魅力

![](../assets/blog-react-flow/img_6.png)
![](../assets/blog-react-flow/img_7.png)

在我们实际写代码的时候，经常会遇到一些需要 customize 的场景，这时候就需要用到 [Custom Nodes] 和 [Custom Edges] 了。

👇 举个例子，写一个自定义节点的流程大概是这样的
```jsx
const myInputNode = ({ data }) => {
  ...
  
  return <div>...</div>;
};

const nodeTypes = {
  myInput: myInputNode,
  myDeault: myDefaultNode,
  myOutout: myOutputNode,
};

const getInputNode = data => ({
  data,
  id: "some id here",
  type: "myInput",
  position: { x: 0, y: 0 },
 });

const Flow = () => {
  ...
  const onAddNode = () => setNodes(nodes.concat(getInputNode({ someData: "一些想传给 CustomNode 的数据" })));
  ...
  return <ReactFow ... nodeTypes={nodeTypes} />;
};

```

自定义节点的时候，我们可以进一步设置 Handle 的属性 
- type: "source" / "target"
- position: "top" / "bottom" / "left" / "right"

我还有很多想说的，但是这里空白太小了，写不下。（完）

[官方文档]: https://reactflow.dev/
[Custom Nodes]: https://reactflow.dev/docs/api/nodes/custom-nodes/
[Custom Edges]: https://reactflow.dev/docs/api/edges/custom-edges/
