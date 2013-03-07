---
layout: post
title: "mxgraph笔记1【简介】"
date: 2013-03-04 14:53
comments: true
categories: 
---
## 简介
mxGraph客户端是一个图形组件，并提供和网页集成的接口。。客户端需要一个Web服务器提供所需的文件，也可以在本地文件系统上运行。后台可用于集成到现有存在的服务器所支持的语言中
与后台配合后， 该组件可完成以下功能：

*   创建类似visio的图库

*   存储加载图库

*   创建一个graph对象

*   与其他客户共享图库

提交
<!--more -->

上述几种方式可以结合起来应用，如发送更改配置的XML文件到后台，或自动保存图形以免数据丢失。并且客户端可以本地化操作。

## 例子 Hello, World!
### 引入库文件

网页头部包含javascript代码和依赖关系。用以下代码来加载库文件。mxBasePath变量用来定义库资源的目录。这个变量必须在加载库前就定义好。

	<script type="text/javascript">
	  mxBasePath = 'javascript/src/';
	</script>
	<script type="text/javascript" src="javascript/src/js/mxClient.js"></script>
mxClient.js包含全部所需代码。注意：资源代码仅仅商业发行。在评估版本中这个文件是一个来自服务器的URL链接。不可以本地化源代码。

### 检查浏览器

下一个script标签包含hello world的代码。代码的第一部分是检查浏览器是否支持mxgraph。 建议在编码前做这步，如果浏览器不支持就能在此显示错误信息。一般来说, js脚本代码应该和html代码分开，但这个例子中没这样做。

对于主函数function main（）{}没有什么特殊的规定。function引用头部加载的文件，并且可以有任何名称包含任何参数。在这个例子中参数是body中的dom节点。注意： 以下代码和dom节点的id无关

	<script type="text/javascript";>
	function main(container)
	{
	  // Checks if the browser is supported
	//检查浏览器是否支持
	  if (!mxClient.isBrowserSupported())
	  {
	    // Displays an error message if the browser is not supported.
	//如果浏览器不支持，则显示错误信息
	    mxUtils.error('Browser is not supported!', 200, false);
	  }
	  ...

## Container 容器

页面用一个dom节点将graph与javascript结合。它可以使用document.getElementById在body中取得（如这个例子）或者直接动态创建（如createElement， 译者注）。dom节点传递到主函数中用于建立以下graph例子。

如果你想让容器中有滚动条，那么将容器样式的属性 overflow 设为auto。

## Graph 图

代码创建了一个空的graph图模型并通过容器和空的模型来构建具体的图。在这个例子中，所有默认事件处理在最后一行将失效。（mxgraph使用典型的MVC模式， 熟悉MVC模式的读者更容易上手。 译者注）

	var model = new mxGraphModel();
	var graph = new mxGraph(container, model);
	如果你希望graph图只读，可用 graph.setEnabed(false).

## Vertices （节点）and Edges（连线）

程序需要在beginUpdate和endUpdate中来插入节点和连线（更新图形）。endUpdate应放在finally-block中以确保endUpdate一直执行。但beginUpdate不能在try-block中，这样如果beginUpdate失败那么endupdate永远不会执行。

块内的部分为图形创建节点和连线。默认的父节点是在用graph时无需参数自动创建在图中根节点的第一个子节点。

	// Gets the default parent for inserting new cells. This
	// is normally the first child of the root (ie. layer 0).
	//为插入节点获得默认的父节点。



	  //这通常是根节点的第一个子节点
	var parent = graph.getDefaultParent();
		
	// Adds cells to the model in a single step
	//在单独的一步中添加cell
	model.beginUpdate();
	try
	{
	  var v1 = graph.insertVertex(parent, null, 'Hello,', 20, 20, 80, 30);
	  var v2 = graph.insertVertex(parent, null, 'World!', 200, 150, 80, 30);
	  var e1 = graph.insertEdge(parent, null, '', v1, v2);
	}
	finally
	{
	  // Updates the display
	//更新显示
	  model.endUpdate();
	}
beginUpdate&endUpdate不仅提供了显示功能，而且它能够当做undo/redo标记的边界（也就是说，beginUpdate和endUpdate之间操作会作为Undo、redo的原子操作，要么同时被redo或undo， 相当于数据库中的事务）

## Graphs 图

将dom节点实例化为一个graph图：

	var node = document.getElementById('id-of-graph-container');
	var graph = new mxGraph(node);

## Model 编程模型

mxcell在mxGraphModel中实现了定义图模型的元素。
图模型有以下属性（包含关系）：
根节点包含各个层，各个层的父节点都是根节点。
层中可包含graph图模型的元素：节点、连线组。
组中可递归的包含graph图模型的元素。
graph图的结构和信息都存储在cell和用户对象中。（又名业务对象）

用一个根节点和默认的层来创建一个新的graph模型：

	var root = new mxCell();
	root.insert(new mxCell());
	var model = new mxGraphModel(root);
## Stylesheet 样式表

cell的样式由样式表（mxStylesheet的实例）来决定。样式表规定了样式名称到样式之间的映射关系。一个样式是一个键的数组。那些键对应所用cell的值。值在mxConstants中定义，可以是字符串和数字、javascript对象、函数等 。 修改节点和连线的默认样式：

	var vertexStyle = graph.getStylesheet().getDefaultVertexStyle();
	vertexStyle[mxConstants.ROUNDED] = true;
	 
	var edgeStyle = graph.getStylesheet().getDefaultEdgeStyle();
	edgeStyle[mxConstants.STYLE_EDGE] = mxEdgeStyle.TopToBottom;
## Styles 样式

cell的样式在属性style中（cell.style）。样式是cell状态的一部分，它可以通mxGraphModel.setStyle来改变。style是form[stylename;|key=value;]中的一段字符串。默认样式可覆盖此cell的制定键值。例如：你用 rounded 样式，它可以覆盖 stroke和fillColor，样式被这样定义：

	[stylename;|key=value;]
## Appearance 显示外观

在具体项目中你也许需要自定义cell的那些动态特性（就是外观），例如 图形、指针形状、颜色等。你可以分别用以下方法： getImage, getIndicatorShape, getIndicatorImage, getIndicatorColor ，getIndicatorGradientColor...。注意：这些方法作为一个参数，它指向一个cell的style"解决"（即数组）版本的cell状态。因此，对getImage默认实现如下所示：

	mxGraph.prototype.getImage = function(state)
	{
	  if (state != null && state.style != null)
	  {
	    return state.style[mxConstants.STYLE_IMAGE];
	  }
	  return null;
	}
这个方法可把cell的图形改成任何你需要的。通常，图像被定义为state.cell指向cell关联的状态或state.cell.value指向用户对象。
为了使改变可以显示出来，你需要调用view.invalidate(cell)和view.validate
## Editors 编辑器
程序通过初始化mxEditor来创建编辑器。这是编辑器包的核心类。其他都是辅助类。可通过配置文件名称创建并配置一个编辑器。


请看下面的例子：

	var config = mxUtils.load('editors/config/keyhandler-commons.xml').getDocumentElement();
	var editor = new mxEditor(config);
xml格式的配置文件会传递给mxCodec，mxCodec使用mxEditorCodec和其他编码将xml文件读入编辑器对象层次结构中。通常在编辑器开始构件时进行，graph, model, toolbar, popupmenus等用 I/O subsystem

## CSS

Css样式表单包含多种用户接口元素（选择框，编辑器，弹出菜单等）的样式的定义。它也包含允许应用XML配置文件来支持IE的指令，因此页面中有大量的这种表单。

可以通过mxClient.link('stylesheet', filename)或者表单标签来配置编辑器。例：

	<mxEditor>
	  <ui>
	    <stylesheet name="examples/editors/css/process.css"/>
	    ...
## Templates 模板

按如下，通过在配置文件中的节点定义新的cell类型模板

	<add as="symbol">
	  <Symbol label="Symbol" customAttribute="whatever">
	    <mxCell vertex="1" connectable="1" style="symbol;image=images/event.png">    
	      <mxGeometry as="geometry" width="32" height="32"/>
	    </mxCell>
	    <CustomChild customAttribute="whatever"/>
	  </Symbol>
	</add>
 

add元素的as属性包含模板上一次成功应用的名称，Symbol子节点元素是一个ie或者火狐的客户端元素，可以有任何名称和任意多的子节点以及客户属性。标签属性是对图形单元的文字表述。mxCell元素是另一个特殊的子节点包含cell的图像化信息，并命名其为 cell-type, -style, -size ， -position.等

## Toolbar

按如下，必须通过在配置文件中配置mxDefaultToolbar节(mxEditor /mxDefaultToolbar[as=toolbar]) 来应用Toolbar模板。

	<add as="symbolTool" template="symbol"
	  style="symbol;image=wf/images/bpmn/special_event.png"
	  icon="wf/images/bpmn/small_event.gif"/>
as属性指定工具标签在工具栏中显示，template属性指定最先被加载的模板，style属性是可选的，用于重写默认的style。icon属性指定了toolbar自身的图形样式。
注：在这个symbolTool的例子中，as属性被规定为语言资源的键值。如果资源在mxResource中没有定义，那么属性值将被作为标签。

以上尽简单介绍，会在后续中详细讨论。

