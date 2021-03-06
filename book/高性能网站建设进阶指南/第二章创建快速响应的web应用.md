# 第二章 创建快速响应的web应用

Web应用和传统桌面应用有一个共同目标：尽可能快的响应用户输入。我们必须确保响应这些输入的js能快速的执行。<br>
浏览器使用单线程处理队列中的事件和执行用户代码。（web workers除外）

## 2.1 怎样才算足够的快
Jakob Nielsen：<br>
<b>0.1秒：</b>用户直接操作UI中对象的感觉极限。<br>
<b>1秒：</b>用户随意地在计算机指令空间进行操作而无需过度等待的感觉极限。（超过1秒要提示用户计算机正在解决这个问题）<br>
<b>10秒：</b>用户专注于任务的极限。（超过10秒的任何操作都需要有一个百分比完成指示器）

## 2.2 测量延迟时间
手动代码检测（记录）和自动代码检测（性能分析）<br>
手动代码检测：打log <br>
自动代码检测：工具 <br>
firebug包含的js代码性能分析器

## 2.3 线程处理
多线程将不会很快在JS上实现。

## 2.4 确保响应速度
Google浏览器插件Gears实现WorkerPool API。允许浏览器主JS线程创建后台“Worker”，接收浏览器线程的一些简单信息。<br>
### 2.4.1 Web Workers
<b>主线程：</b>
	
	var w = new Worker("worker.js");
	w.postmessage(m);
	w.onMessage = function(e) {
		...
	}
	w.terminate()

<b>Worker:</b>

	onmessage = function(e) {
	    postMessage()
	}

### 2.4.2 Gears
Gears API 与Web Worker API有微小差别。

### 2.4.3 定时器
把运行时间很长的计算拆分成独立的区块，然后用js的定时器控制其执行。<br>
<b>该方法的问题：</b>

1. 编码的困难。
2. 浪费计算机多核的能力。
  

### 2.4.4 内存使用对相应时间的影响
自动内存管理的开销：在执行回收时，GC执行复杂的“stop-the-world”操作时，会冻结整个运行环境。

### 2.4.5 虚拟内存
内存分页是与内存相关的另一种风险。网页对内存需求足够大，迫使操作系统分页，内存页从物理内存转移到虚拟内存。

### 2.4.6 内存问题的疑难解答
当前没有好的JS内存排查工具，大多技术停留在观察浏览器进程中内存痕迹的水平。<br>
发现内存问题的解决方案：

1. 使用delete删除不需要的JS对象。
2. 从DOM中移除不需要的节点。

	var page = {test: "test"};
	...
	delete page;

	var Node = $('#redundant');
	delete Node.parent.removeChild(Node);

[Mozilla lab](http://labs.mozilla.com)
      