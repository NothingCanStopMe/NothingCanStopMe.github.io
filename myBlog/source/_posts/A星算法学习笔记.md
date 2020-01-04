---
layout: 笔记
title: A星算法学习笔记
date: 2020-01-04 22:18:12
tags: 笔记
---

1. 简化搜索区域, 即栅格化起点与终点所在的区域并使之对应为平面坐标系, 确定搜索最大区域
2. 遍历搜索区域, 建立open list(可走区域), closed list(不可走区域)
3. 建立open list时, 将每个区域的 
	f(n) 参考量: = g(n) + h(n)  
	g(n) 移动代价: 初始点移动到计算点的难易程度 比如上下左右移动为1 斜线运动为1.414
	h(n) 移动距离:一般采用曼哈顿距离计算方式, 即
	计算好                                                                                                                      
4. 开始搜索
	4.1 确定第一个计算点, 通常为初始点
	4.2 将计算点与计算点周围的点放入openlist内
	4.3 遍历计算点周围的点, 找出f(参考量)最小的点为下一个计算点
	4.4 将下一个计算点的父结点设为当前计算点
	4.5 将计算点从openlist内移除
	4.6 循环4.1 - 4.5 直到将终点纳入计算点或openlist表为空为止
5. 注意点
	5.1 当多个点的f值一样的时候需要引入策略判断
	5.2 当不能直接找到下一个计算点的时候, 重新从openList里面去点需要策略判断(一般来说取f最小的)
6. 整的来说A星算法给需要设计寻路、搜索算法启示。 通俗一点类似找到走出迷宫的路径, 
	首先需要简化搜索规则(比如不能穿墙, 不能翻墙, 不能破墙),
	然后确定搜索模型(比如能否将起点 路径 终点都对应到一个平面上)
	然后确定搜索策略(比如f(n) = g(n) + h(n))
	然后一步一步的确定每一个计算点(比如根据f值的大小取下一个计算点)
	然后循环搜索计算点的过程, 知道没有点可以搜索或者找到终点为止

{% codeblock %}
-- 本demo方法只适用于起点终点的坐标都位于直角坐标系上的情况
local AStarSearch = {}

-- sPoint:起点 ePoint:终点
-- barrierList: 障碍物表
function AStarSearch:ctor(sPoint, ePoint, barrierList)
	self.sPoint = sPoint
	self.ePoint = ePoint

	self.sPoint = self:newNode(sPoint, 0)
	self.ePoint = self:newNode(ePoint, 0)
	self.openList = {}
	self.closedList = {}
	for k,v in pairs(barrierList) do
		self.closedList[v[1] .. "_" .. v[2]] = {v[1], v[2]}
	end

	self.openList[self.sPoint.name] = self.sPoint -- 将起点放入openList
	self.resultNode = self:search(self.sPoint) -- 开始寻路, 并启用起点为第一个计算点
end

-- 通过坐标和G值新建结点
function AStarSearch:newNode(point, gValue)
	if type(point) ~= "table" then 
		error("table expected got other")
		return
	end

	local node = {point[1], point[2]}
	node.g = gValue or 0 -- 起点到当前结点的难易程度
	node.h = self:getManhatanDistance(node, self.ePoint) -- 当前结点到终点的曼哈顿距离
	node.f = node.g + node.h -- 当前结点的参考值
	node.name = string.format("%d_%d", point[1], point[2]) -- 当前结点的名字
	node.sh = self:getManhatanDistance(node, self.sPoint) -- 当前结点到起点的曼哈顿距离

	return node
end

-- 获取曼哈顿距离
function AStarSearch:getManhatanDistance(point1, point2)
	return 10 * (math.abs(point1[1] - point2[1]) + math.abs(point1[2] - point2[2]))
end

-- 找出计算点周围的下一个计算点
function AStarSearch:getMinFNode(parseNode)
	local minF = math.pow(2, 10) -- 最小的f值
	local minFNode = nil -- 最小f的结点
	local targetG = 0 -- 最小f值结点的g值
	for i = -1, 1 do
		for j = -1, 1 do
			-- 计算点和不可越过/不参与计算的点 不参与比较f值
			local nodeName = string.format("%d_%d", parseNode[1] + i, parseNode[2] + j)
			if (i ~= 0 or j ~= 0) and not self.closedList[nodeName] then
				-- print("getMinFNode ", nodeName)
				local tmpNode = self.openList[nodeName]
				local tmpF = 0
				if not tmpNode then
					tmpNode = self:newNode({parseNode[1] + i, parseNode[2] + j}, parseNode.g + 10)
					tmpNode.parent = parseNode
					targetG = tmpNode.g
					tmpF = tmpNode.f
				else 
					targetG = parseNode.g + 10
					tmpF = targetG + tmpNode.h
				end

				-- 确定下一个计算的策略
				-- f值最小 或 f值相等时h值最小 或 f值相等时sh最大
				if tmpF < minF then 
					minF = tmpF
					minFNode = tmpNode
				elseif tmpF == minF and (tmpNode.h < minFNode.h or tmpNode.sh > minFNode.sh) then 
					minF = tmpF
					minFNode = tmpNode
				end
			end 
		end
	end

	if minFNode then 
		minFNode.g = targetG
		minFNode.f = minF
	end

	return minFNode
end

-- 搜索算法
function AStarSearch:search(parseNode)
	-- 搜索模型原理为
	-- 将计算点周围x,y轴偏移量为1的结点纳入openList中
	-- 找出计算点周围的点中f值最小的点为下一计算
	-- 递归寻找下一个计算点, 直到找到终点或者openList里面没有点可计算为止

	local minFNode = self:getMinFNode(parseNode)
	-- print("search ", parseNode.name, minFNode and minFNode.name)
	-- 将当前计算点放入closedList里面
	self.openList[parseNode.name] = nil
	self.closedList[parseNode.name] = parseNode

	if minFNode then -- 找到下一个计算点
		minFNode.parent = parseNode
		if minFNode.name ~= self.ePoint.name then 
			return self:search(minFNode)
		else
			return minFNode
		end
	else -- 没找到下一个计算点, 我这里随便挑了一个点, 理论上需要引入策略
		local key,value = next(self.openList)
		if key then 
			return self:search(value)
		end
	end

	return false
end

-- 返回路线的链表
function AStarSearch:getResultNode()
	return self.resultNode
end

-- 从终点开始打印路线到起点来
function AStarSearch:printPathToEnd()
	local node = self.resultNode
	if not node then 
		print("并没有搜索到路线")
		return 
	end

	while(true) do
		local parent = node.parent
		print(node.name)
		if parent then 
			node = parent
		else
			break
		end
	end
end

-- 获取从起点到终点的路径
function AStarSearch:getPathPoints()
	local ret = {}
	local node = self.resultNode
	if not node then 
		print("并没有搜索到路线!")
		return nil
	end

	while(true) do
		local parent = node.parent
		table.insert(node, 1, node)
		if parent then 
			node = parent
		else
			break
		end
	end

	return ret
end

-- 开始测试
-- 障碍物
local barrierList = {
	{3, 0}, {3,1}, {3,2},
	{3, -1}, {3, -2},
}

local myAS = {}
setmetatable(myAS, {__index = AStarSearch})
myAS:ctor({0, 0}, {5, 0}, barrierList)
myAS:printPathToEnd()


{% endcodeblock %}

{% link "参考自这位同学的博客" https://blog.csdn.net/qq_36946274/article/details/81982691 %}
