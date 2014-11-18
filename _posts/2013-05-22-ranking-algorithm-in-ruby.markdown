---
layout: post
title: "Ruby 实现排序算法"
category: Technology
tags: Ruby ranking algorithm
---

最近在复习算法，做下笔记。以前看到的一般都是用 C 语言实现，下面用 Ruby 作为伪语言分别实现冒泡排序和快速排序。

### 冒泡排序

算法原理：

1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
2. 对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
3. 针对所有的元素重复以上的步骤，除了最后一个。
4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

代码实现如下：

    def bubble_sort(list) 
      list = list.dup
	    for i in 0...list.length 
	      for j in 0..(list.length - i - 2) 
	        list[j], list[j + 1] = list[j + 1], list[j]  if list[j + 1] < list[j] 
	      end 
	    end 
	    return list 
	  end

### 快速排序

算法原理：

1. 从数组中随机选出一个元素作为基数(pivot)。
2. 将数组重新排列，比基数小的排在基数前面，比基数大的排在基数后面（跟基数相同的数可以放在任一边）。在这个分割结束后，这个基数就处于数列的中间位置，这个称为分割（partition）操作。
3. 递归地把小于基数的元素子数列和大于基数的元素子数列排序。
4. 当所有分割数组的大小是0或者1的时候，排序结束。


		def quicksort(list)
		  return list if list.size <= 1
		  pivot = list.sample
		  left, right = list.partition { |e| e < pivot }
		  quicksort(left) + quicksort(right)
		end

### 睡眠排序

这个算法的原理是针对数组里面的不同数开多个线程，每个线程根据数的大小睡眠，越大的数“睡”得越久，挺有意思。这个算法来源于[这个帖子](http://dis.4chan.org/read/prog/1295544154)

		ARGV.each { |e| fork { sleep(e.to_f/1000); puts e } }


#### 参考：

* [冒泡排序 - 维基百科](http://en.wikipedia.org/wiki/Bubble_sort)
* [快速排序 - 维基百科](http://en.wikipedia.org/wiki/Quicksort)
* [ShareJs](http://www.sharejs.com/codes/ruby/)
