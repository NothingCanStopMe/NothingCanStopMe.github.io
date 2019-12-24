---
title: 捕鱼项目减少cpu占用的优化方案总结
date: 2019-06-21 09:09:42
tags: 笔记
---

最近在做捕鱼项目的时候, 总为房间内cpu占用率过高而烦恼, cpu占用率甚至达到了60%, 着手优化这部分内容重中之重
为了优化代码 减少cpu占用率 采用以下流程检查代码那部分导致cpu占用率增加
1. 在 config.lua 文件内将 CC_SHOW_FPS 设置为 true , 显示整个游戏的GL verts(当前页面顶点数), GL call(每帧调用OpenGl次数), FPS(帧率)
	1.1 MAC模拟器上GL verts 8000+, GL call 200+, FPS 58+
	1.2 理论上FPS 58+不影响游戏的流畅度, 但是GL verts 和 GL call 太高了, 需要优化
2. 经检查代码发现: 
 	2.1 为了实现鱼的可点击, 每条鱼身上都添加了一张白图作为点击对象
 	2.2 为了实现光影效果, 每条鱼身上添加了一个 scheduler 每0.2秒渲染一个阴影到鱼的底下, {% link "(如果给鱼添加简单阴影)" https://nothingcanstopme.github.io/2019/06/21/如果给鱼添加简单阴影/ %}
3. 分析上面两个方案的弊端
	3.1 添加了白图作为点击对象, GL verts 增加了2000+
	3.2 添加了光影效果, GL verts 增加了2000+ GL call 增加了100+
4. 对这两方案进行优化
	4.1 由于鱼的碰撞采用的是cocos2dx 的物理世界(physicsWorld)的oncontactBegin方法, 那鱼的点击, 可以采用以下物理世界里面的getShape方法实现, 具体代码:
	{% codeblock %}
	function xxx:onTouchEnded(touch, event)
		local physicsWorld = cc.Director:getInstance():getRunningScene():getPhysicsWorld()
		local shape = physicsWorld:getShape(touch:getLocation())
		if shape then 
			local node = shape:getBody():getNode()
			-- 这里获取到了当前点击的Node, 自行判断是否为鱼即可
		end
	end
	{% endcodeblock %}
	getShape方法优先获取 最上层的shape(为了碰撞设置的多边形) 无需自行判断那条鱼在最上面. 
	如果需要知道当前点击位置附近有多少条鱼, 建议采用getShapes(touch:getLocation())方法
	4.2 光影效果直接删掉或者给个按钮用户选择是否出现即可(浪费了我研究这么久)
5. 做完以上优化之后发现 Gl verts 还要3000+, 检查代码发现, 捕鱼的房间直接add到大厅上面的, 还需要将大厅setVisible(false)掉
	因为cocos2dx很多layer(layout)叠在一起时, 只要可见都会进行渲染, 凭空浪费cpu

<font size = 3, color = red > 结论: </font>
做cpu优化时, 打开fps显示, 检查代码的哪一部分会导致 GL verts, GL call 升高, 并找替代方法, 可以达成目标
