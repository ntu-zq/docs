
lt59 mid
## 简述
给定一个正整数 n，生成一个包含 1 到 n^2 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。

示例:

输入: 3 输出: [ [ 1, 2, 3 ], [ 8, 9, 4 ], [ 7, 6, 5 ] ]

## 思路
和二分法一样，有个循环**不变量**，每次循环的边界是一样的。
左闭右开或者左闭右闭，这题适合用左闭右开。
如果n是奇数的话，中心位置要单独复制

```go
func generateMatrix(n int) [][]int {
	result := make([][]int, n)
	for i := 0; i < n; i++ {
		result[i] = make([]int, n)
	}

	offset := 1
	count := 1
	for i := 0; i < n/2; i++ {
		startX, startY := offset-1, offset-1
		x, y := startX, startY
		for ; y < n-offset; y++ {
			result[x][y] = count
			count++
		}
		for ; x < n-offset; x++ {
			result[x][y] = count
			count++
		}
		for ; y > offset-1; y-- {
			result[x][y] = count
			count++
		}
		for ; x > offset-1; x-- {
			result[x][y] = count
			count++
		}
		offset++
	}
	if n%2 != 0 {
		mid := n / 2
		result[mid][mid] = count
	}
	return result
}
```


