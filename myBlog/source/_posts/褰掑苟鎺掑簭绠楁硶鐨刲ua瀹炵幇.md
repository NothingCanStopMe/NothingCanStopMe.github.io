---
title: 归并排序算法的lua实现
date: 2019-06-24 11:10:17
tags: 笔记
---

{% codeblock %}

-- 归并排序算法学习
--[[
	1. 设置临时table, 用来存放排序之后的数组
	2. 设定两个值i,j, 初始值分别为两个待排序数组的第一个key
	3. 比较key为i,j对应的值, 较小的放入合并空间, 并移动到下一个值
	4. 重复步骤3到i,j其中一个超出数组最大值
	5. 将另一个数组的剩下的元素复制到临时table中
]]
function merge(sourTab, tmpTab, startIndex, midIndex, endIndex)
	local i = startIndex
	local j = midIndex + 1
	local k = startIndex
	while(i <= midIndex and j <= endIndex) do
		if sourTab[i] > sourTab[j] then 
			tmpTab[k] = sourTab[j]
			j = j + 1
		else
			tmpTab[k] = sourTab[i]
			i = i + 1
		end
		k = k + 1
	end

	-- 将剩下的拼到临时table的末尾 理论上i, j只有一个剩下
	while (i <= midIndex) do
		tmpTab[k] = sourTab[i]
		k = k + 1
		i = i + 1
	end

	-- 将剩下的拼到临时table的末尾
	while (j <= endIndex) do
		tmpTab[k] = sourTab[j]
		k = k + 1
		j = j + 1
	end

	-- 将排序好的数组赋值回原table, 这一步不能省略
	for i = startIndex, endIndex do
		sourTab[i] = tmpTab[i]
	end
end

function mergeSort(sourTab, tmpTab, startIndex, endIndex)
	if startIndex < endIndex then 
		local midIndex = startIndex + math.floor((endIndex - startIndex) / 2)
		mergeSort(sourTab, tmpTab, startIndex, midIndex)
		mergeSort(sourTab, tmpTab, midIndex + 1, endIndex)
		merge(sourTab, tmpTab, startIndex, midIndex, endIndex)
	end
end

local tab = {50, 10, 20, 30, 70, 40, 80, 60}
local tab1 = {}
mergeSort(tab, tab1, 1, #tab)
print(table.concat(tab, ","))

{% endcodeblock %}

{% link "参考百度百科" https://baike.baidu.com/item/归并排序 %}