## 简述
题意： 给定一个链表，返回链表开始入环的第一个节点。 如果链表无环，则返回 null。

为了表示给定链表中的环，使用整数 pos 来表示链表尾连接到链表中的位置（索引从 0 开始）。 如果 pos 是 -1，则在该链表中没有环。

**说明**：不允许修改给定的链表。

## 思路
## 方法一
哈希表
```go
func detectCycle(head *ListNode) *ListNode {
	m := make(map[*ListNode]bool)
	cur := head

	for cur != nil {
		if m[cur] {
			return cur
		}
		m[cur] = true
		cur = cur.Next
	}
	return nil
}
```

## 方法二
**快慢指针判断是否有环**

快指针每次两个节点，慢指针每次一个节点

#### 为什么有环的情况下，快慢指针一定相遇？
因为快指针比慢指针多一个节点，相对速度大1。相当于每次移动，快指针都会靠近慢指针一个节点，所以必会相遇。
1. 快慢必会相遇
2. 必定是慢指针进入环内第一圈相遇
3. 相遇之后，如何找到入口节点？

```go
func detectCycle(head *ListNode) *ListNode {
	if head == nil || head.Next == nil {
		return nil
	}
	slow, fast := head, head
	for fast != nil && fast.Next != nil {
		slow = slow.Next
		fast = fast.Next.Next
		if slow == fast {
			index1 := slow
			index2 := head
			for index1 != index2 {
				index1 = index1.Next
				index2 = index2.Next
			}
			return index1
		}
	}
	return nil
}
```