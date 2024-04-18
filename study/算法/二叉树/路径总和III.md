LT432 mid

## 简述
给定一个二叉树的根节点 `root` ，和一个整数 `targetSum` ，求该二叉树里节点值之和等于 `targetSum` 的 **路径** 的数目。

**路径** 不需要从根节点开始，也不需要在叶子节点结束，但是路径方向必须是向下的（只能从父节点到子节点）。
## 思路一
自己写的很捞的一个解法
把到当前节点的路径保存起来，依次遍历看是否有符合的
注意点是，每次都要创建一个新的数组传下去，不然会影响上一层的数组

``` go

func pathSum(root *TreeNode, targetSum int) int {
	if root == nil {
		return 0
	}
	res := 0
	if root.Val == targetSum {
		res++
	}
	arr := []int{targetSum - root.Val}
	res += dfs(root.Left, arr, targetSum)
	res += dfs(root.Right, arr, targetSum)
	return res
}

func dfs(root *TreeNode, arr []int, targetSum int) int {
	if root == nil {
		return 0
	}
	res := 0
	tempArr := make([]int, len(arr)+1)
	for i := range arr {
		if root.Val == arr[i] {
			res++
		}
		tempArr[i] = arr[i] - root.Val
	}
	if root.Val == targetSum {
		res++
	}
	tempArr[len(arr)] = targetSum - root.Val
	res += dfs(root.Left, tempArr, targetSum)
	res += dfs(root.Right, tempArr, targetSum)

	return res
}

```
优化：
``` go
func rootSum(root *TreeNode, targetSum int) (res int) {
    if root == nil {
        return
    }
    val := root.Val
    if val == targetSum {
        res++
    }
    res += rootSum(root.Left, targetSum-val)
    res += rootSum(root.Right, targetSum-val)
    return
}

func pathSum(root *TreeNode, targetSum int) int {
    if root == nil {
        return 0
    }
    res := rootSum(root, targetSum)
    res += pathSum(root.Left, targetSum)
    res += pathSum(root.Right, targetSum)
    return res
}

```

## 思路二
求前缀和
map记录根节点到当前节点的前缀和
怎么判断以当前节点为终点，是否有答案？看map中是否有前缀和-target的值，有的话取出来++
比如10,5,3  target是8，那么到3节点时，前缀和有10,15,18 ，18-8=10，说明把10这条路径的下一个节点，到当前节点的路径就是答案。
注意点：遍历完左右子树后，要把当前前缀和--

``` go
func pathSum(root *TreeNode, targetSum int) int {
    ans := 0
    m := map[int]int{} //m中key为前缀和，value存的是该前缀和出现的次数
    m[0] = 1 //当某结点的前缀和等于target时，表示从根结点到该结点的pathSum等于target，这是一条路径ans需要加一，所以m[prefixSum - targetSum]即m[0]得为1
    var dfs func(*TreeNode, int)
    dfs = func(root *TreeNode, prefixSum int) {
        if root == nil {
            return
        }
        prefixSum += root.Val
        // 遍历到当前结点时，以当前结点为终点，从root到当前结点的路径上有多少结点满足到当前结点的路径和为targetSum，累加到ans
        ans += m[prefixSum - targetSum]
        // 更新当前结点的前缀和到m中
        m[prefixSum] += 1
        dfs(root.Left, prefixSum)
        dfs(root.Right, prefixSum)
        // 消除状态。确保m中存的前缀和是从root一直往下走到该结点路径上的前缀和。其他不属于这个路径的前缀和就去掉。
        m[prefixSum] -= 1
    }
    dfs(root, 0)
    return ans
}

```