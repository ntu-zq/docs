lt230 mid
## 简述
给定一个二叉搜索树的根节点 `root` ，和一个整数 `k` ，请你设计一个算法查找其中第 `k` 个最小元素（从 1 开始计数）。
## 思路
中序遍历取第K个元素
这道题用到了函数式编程
```go
func kthSmallest(root *TreeNode, k int) int {
	ret := []int{}
	var dfs func(root *TreeNode)
	dfs = func(root *TreeNode) {
		if root == nil || len(ret) >= k{
			return
		}

		dfs(root.Left)
		ret = append(ret, root.Val)
		dfs(root.Right)
	}
	dfs(root)
	return ret[k-1]
}
```