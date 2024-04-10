lt19 mid
## 简述
给你一个链表，删除链表的倒数第 n 个结点，并且返回链表的头结点。

进阶：你能尝试使用一趟扫描实现吗？

## 思路
### 方法一
map存储各个节点，遍历一遍后，删除对应节点

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
	m := make(map[int]*ListNode, 0)
	count := 0
	virtualHead := &ListNode{
		Next: head,
	}
	cur := virtualHead
	for cur != nil {
		m[count] = cur
		count++
		cur = cur.Next
	}
	if n >= count {
		return nil
	}
	m[count-n-1].Next = m[count-n+1]
	return virtualHead.Next
}
```

### 方法二、快慢指针
快指针先移动n步，此时快慢指针一起移动，当快指针指向末尾的时候，就开始删除慢指针所在的节点
题目写明了，n是小于链表长度的

```go
func removeNthFromEnd(head *ListNode, n int) *ListNode {
	virtualHead := &ListNode{Next: head}
	left,right := virtualHead,virtualHead
	for n > 0 {
		right = right.Next
		n--
	}
	for right.Next != nil{
		left = left.Next
		right = right.Next
	}
	left.Next = left.Next.Next
	return virtualHead.Next
}
```