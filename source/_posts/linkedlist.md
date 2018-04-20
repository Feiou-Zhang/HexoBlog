---
title: 链表
date: 2018-04-16 00:45:20
tags: [LinkedList]
categories: [Algorithm]
---
#### 206. Reverse Linked List
```java
public class ReverseLinkedList0206 {
    /** 比如 A -> B -> C -> D
     * 思路一：普通迭代
     * 定义最后的返回节点prev 为null，
     * 循环里面，新建一个next节点，记录head.next节点，然后head指针 指向 prev
     * 然后prev自己指向head，最后head自己指向next
     * 思路二：递归
     * 首先定义head.next 为next，然后 head 指针 指向null，
     * 这时候，递归翻转next，并且把返回的新结果保存在 newHead,
     * 此时，我们假设，链表已经翻转结束，那么，这时候，只需要把next的指针 指回 head就可以
     * 最后返回newHead
     * 思路三，start，end，翻转法，不停的把start放到end后面，然后每次重置pre 后面的点为start，
     * 重复操作，直到start 和 end 重叠 ，翻转结束
     * */
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode prev = null;
        while (head != null) {
            ListNode next = head.next;
            head.next = prev;
            prev = head;
            head = next;
        }
        return prev;
    }
    public ListNode reverseList1(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode next = head.next;
        head.next = null;
        ListNode newHead = reverseList1(next);
        next.next = head;
        return newHead;
    }
    public ListNode reverseList2(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }
        ListNode tail = head;
        while (tail.next != null) {
            tail = tail.next;
        }
        ListNode pre = new ListNode(0);
        pre.next = head;
        while (head != tail) {
            pre.next = head.next;
            head.next = tail.next;
            tail.next = head;
            head = pre.next;
        }
        return pre.next;
    }
}
```