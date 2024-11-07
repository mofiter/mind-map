# src/index.js
- 入口文件
- 整个思维导图的画布是一个 svg 标签，思维导图的容器是 svg 标签里的一个 group，class 为 smm-container
- 思维导图的容器里包含以下几个部分：
  - 节点连线容器 group，class 为 smm-line-container，节点连线都在这个容器里
  - 节点容器 group，class 为 smm-node-container，节点都在这个容器里
  - 关联线容器 group，class 为 smm-associative-line-container，关联线都在这个容器里
  - 其他内容容器 group，class 为 smm-other-container，其他内容都在这个容器里
  - 外框容器 group，class 为 smm-outer-frame-container，外框都在这个容器里

# src/core/render/node/MindMapNode.js
- 节点类
- 说明：节点内容都放在一个 group 里，class 为 smm-node，这个 group 放在节点容器 smm-node-container 里
- 属性
  - expand: 节点是否展开，布尔值
  - isActive: 节点是否激活，布尔值
  - children: 子节点，数组
  - shape: 节点形状，字符串
  - layerIndex: 节点层级，数字类型，根节点为 0，每增加一级节点，层级加 1
- 事件
  - mouseenter: 鼠标移入节点，显示展开收起按钮
  - mouseleave: 鼠标移出节点，隐藏展开收起按钮
- 连线
  - _lines: 连线 path 的数组，数组每一项是一个 path 标签
  - renderer.layout.renderLine: 渲染连线

# src/core/render/node/Shape.js
- 节点形状类
- 说明：形状节点是一个 path 或 polygon，class 为 smm-shape，放在节点内容 smm-node 里

# src/core/render/node/nodeExpandBtnPlaceholderRect.js
- 节点展开收起按钮的隐藏占位元素
- 说明：隐藏占位元素是一个矩形 Rect，放在节点内容 smm-node 里
- 使用 renderer.layout.renderExpandBtnRect 方法渲染
- 根节点或没有子节点不需要渲染
- 只是起个占位作用，实际的展开收起按钮不渲染在这个 Rect 里面
- 这个占位元素的作用到底是什么？没有会怎么样？

# src/core/render/node/nodeExpandBtn.js
- 节点展开收起按钮
- 说明：展开收起按钮是一个 group，class 为 smm-expand-btn，放在节点内容 smm-node 里
- 如果是收起状态，一般会显示子孙节点的数量，需要通过节点的 children 递归计算
- 使用 renderer.layout.renderExpandBtn 方法渲染

# src/core/render/node/nodeGeneralization.js
- 概要的相关处理
- 说明：概要是一个 node，class 为 smm-node 和 generalization_nodeId
- renderGeneralization: 渲染概要节点
  - updateGeneralizationData 更新节点概要数据
  - createGeneralizationNode 创建概要节点，会修改 _generalizationList 数组，数组每一项是一个对象，对象包含 node、range、generalizationLine、generalizationNode。创建了 MindMapNode 实例，赋值给 generalizationNode。
  - 使用 renderer.layout.renderGeneralization 方法渲染
- 

# src/plugins/OuterFrame.js
- 外框插件
- 说明：所有的外框有一个容器 group，class 为 smm-outer-frame-container，放在思维导图容器 smm-container 里，每个外框是一个矩形 Rect，放在容器 group 中
- 外框的渲染：当思维导图树渲染完毕，或者数据变化时，会重新渲染外框，调用 renderOuterFrames 方法
- 外框的创建：在外框渲染时，通过遍历节点，对于每个有外框的节点，都调用 createOuterFrameEl 方法创建外框，得到一个 rect，给这个 rect 添加一个点击事件，点击时，激活外框

# src/core/render/Render.js
- 渲染类
- 说明：渲染类是思维导图的核心类，负责思维导图的渲染和更新
- 构造函数
  - 设置布局，根据 options 中的 layout 设置布局，默认使用 LogicalStructure 布局。每个布局是一个类，在 src/layouts 目录下。
  - 绑定事件，绑定画布点击事件、右键事件、双击事件等
  - 注册命令，注册全选、回退、前进、插入同级节点、插入多个同级节点、插入子节点、插入多个子节点、插入父节点、上移节点、下移节点、删除节点、剪切节点、粘贴节点、展开所有节点、收起所有节点、设置节点数据、添加概要、删除概要等命令
  - 注册快捷键，如 Tab、Insert、Enter、Shift+Tab、Ctrl+g、/、Del、Backspace、Control+a、Control+l、Control+Up、Control+Down、Control+c、Control+x、Control+v 等。快捷键的触发是执行对应的命令，execCommand
- 方法
  - render: 渲染思维导图
    - 计算布局，调用布局类的 doLayout 方法，计算每个节点的位置、大小等信息，然后调用 node.render 方法渲染节点
  - addGeneralization: 添加节点概要
    - 从选择的节点中，找出要添加概要的节点，然后添加概要数据，再重新渲染
  - removeGeneralization: 删除节点概要