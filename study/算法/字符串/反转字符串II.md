lt541 easy
## 简述
给定一个字符串 s 和一个整数 k，从字符串开头算起, 每计数至 2k 个字符，就反转这 2k 个字符中的前 k 个字符。

如果剩余字符少于 k 个，则将剩余字符全部反转。

如果剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符，其余字符保持原样。

示例:

输入: s = "abcdefg", k = 2  
输出: "bacdfeg"

## 思路
```go
func reverseStr(s string, k int) string {
	arr := []byte(s)
	n := len(arr)
	for index := 0; index < len(arr); {
		left, right := index, index+k-1
		if index+k > n {
			right = n-1
		}
		for ; left < right; left, right = left+1, right-1 {
			arr[left], arr[right] = arr[right], arr[left]
		}
		index = index + 2*k

	}
	return string(arr)
}
```

优化一下：
``` go
func reverseStr(s string, k int) string {
    ss := []byte(s)
    length := len(s)
    for i := 0; i < length; i += 2 * k {
     // 1. 每隔 2k 个字符的前 k 个字符进行反转
     // 2. 剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符
        if i + k <= length {
            reverse(ss[i:i+k])
        } else {
            reverse(ss[i:length])
        }
    }
    return string(ss)
}

func reverse(b []byte) {
    left := 0
    right := len(b) - 1
    for left < right {
        b[left], b[right] = b[right], b[left]
        left++
        right--
    }
}
```
