lt347 mid

## 简述
给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

示例 1:

- 输入: nums = [1,1,1,2,2,3], k = 2
- 输出: [1,2]

示例 2:

- 输入: nums = [1], k = 1
- 输出: [1]

提示：

- 你可以假设给定的 k 总是合理的，且 1 ≤ k ≤ 数组中不相同的元素的个数。
- 你的算法的时间复杂度必须优于 $O(n \log n)$ , n 是数组的大小。
- 题目数据保证答案唯一，换句话说，数组中前 k 个高频元素的集合是唯一的。
- 你可以按任意顺序返回答案。

## 思路

### 方法一
没太看懂

1. 使用小顶堆。为什么？因为我们是维护最高频率的元素，每次遍历要淘汰掉最小的值

### 方法二
```go
func topKFrequent(nums []int, k int) []int {
	m := make(map[int]int)
	for _, v := range nums {
		m[v]++
	}
	res := make([]int, 0)
	for k, _ := range m {
		res = append(res, k)
	}
	sort.Slice(res, func(i, j int) bool {
		return m[res[i]] > m[res[j]]
	})
	return res[:k]
}
```


