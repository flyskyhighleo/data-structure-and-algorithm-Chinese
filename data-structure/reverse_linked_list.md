# Reverse A Linked List

# Overview

Normally, we use two pointers to reverse a linked list by changing nodes reference. In this article we are going to talk about

1. reverse a linked list with iteration approach (two pointers method)
2. reverse a linked list with recursion approach.
3. reverse top k nodes in a linked list.
4. reverse an interval in a linked list.
5. reverse nodes of group k in a linked list.

# 翻转整个链表

翻转整个链表比较常用的方法为

1. 迭代翻转。通过将“当前指针”指向“前驱指针”，然后移动“前驱指针”和“当前指针”来实现。
    
    ```java
    ListNode reverse(ListNode head) {
        ListNode prev = null;
        ListNode cur = head;
    
        while (cur != null) {
            ListNode nextNode = cur.next;
            cur.next = prev;
            prev = cur;
            cur = nextNode;
        }
    
        return prev;
    }
    ```
    
2. 递归翻转。单链表的特殊结构（无论从哪个节点起都具有相同结构）使得它非常适合递归。
    
    明确递归函数 ListNode reverse(ListNode head) 的含义：翻转一个单链表，并返回新的头节点。
    
    n0 → n1 → n2 → n3 → n4 翻转后 n0 ← n1 ← n2 ← n3 ← n4，返回 n4
    
    当我们将递归函数作用于 heand.next 的时候，reverse(head.next), n2 → n3 → n4 得到翻转，并且返回 n4。我们将返回的头节点计作 newHead。
    
    ListNode newHead = reverse(head.next);
    
    那么原单链表就变成了 n1 → reverse(n2 → n3 → n4), reverse(head.next) 返回n4
    
    n1 → n2 ← n3 ← n4。
    
    现在我们需要将头节点的后驱节点指向头节点head，同节点只想后正确的驱节点 null。
    
    head.next.next = head;
    
    head.next = null;
    
    最后返回新的头节点，newHead。
    
    ```java
    ListNode reverse(ListNode head) {
    
        if (head == null || head.next == null) {
            return head;
        }
    
        ListNode newHead = reverse(head.next);
        head.next.next = head;
        head.next = null;
    
        return newHead;
    }
    ```
    

# 翻转单链表的前k个节点

假如，我们知道单链表的节点数 size, 那么翻转整个单链表就相当于翻转前size个节点。函数可以定义为 ListNode reverse(ListNode head, int k)； 其中 k = size。当 k 等于 1 的时候，就是递归结束条件，可以理解为，只有一个节点需要翻转。

现在，我们有单链表 n0 → n1 → n2 → n3 → n4，k = 3.

同翻转单链表一样，首先明确递归函数定义，ListNode reverse(ListNode head, int k)，翻转单链表中的前k个节点，并返回新的头节点。如果从头节点开始翻转需要翻转前k个节点，那么从head.next开始呢？那么就是翻转前 k - 1 个。同理，head.next.next时，翻转 k - 2个。。。

翻转后的单链表变为 n4 ← n3 ← n0 ← n1 ← n2。可以发现，此时原头节点指向了“终止节点“ n2的后驱节点，successor. 因此，我们需要记录这个后驱节点。

递归结束条件，当 k == 1 的时候我们可以理解为，只有一个节点需要翻转（head），这时，我们记录后驱节点 successor = head.next; 并且返回 head。

```java
ListNode successor = null;

ListNode reverse(ListNode head, int k) {
    if (k == 1) {
        successor = head.next;
        return head;
    }

    ListNode newHead = reverse(head.next, k - 1);
    head.next.next = head;
    head = successor;

    return newHead;
}
```

# 翻转单链表区间[m, n]

[LC 92 Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/description/)

## 迭代思想

我们已经知道翻转整个单链表的方法。如果翻转单链表区间[m, n]，比较容易想的方法就是找到开始需要翻转的节点 start 和 结束节点 end。然后翻转链表从 start 到 end。这种方法用了迭代的思路。

当翻转目标区间以后，我们需要特别注意如何处理开始节点的前驱节点和结束节点的后驱节点，从而将整个链表连接起来。

例如，[m, n] = [2, 4]

https://documents.lucid.app/documents/dd1cae9b-6c19-4921-862c-3b204ee6bf61/pages/0_0?a=472&x=-540&y=30&w=859&h=199&store=1&accept=image%2F*&auth=LCA%20c35cb8220bfd418737f61fead272ec67720b58a86eb7cd3e4aa782640b840932-ts%3D1692251338

翻转之后的链表指针如下所示

https://documents.lucid.app/documents/dd1cae9b-6c19-4921-862c-3b204ee6bf61/pages/0_0?a=563&x=-554&y=210&w=912&h=199&store=1&accept=image%2F*&auth=LCA%20c0a67c299c4f94792eb869a284fe83e63863d640e39803b8b5b428d2c4624f15-ts%3D1692251338

将start的前驱节点predecessor指向end 节点，将start 节点只想end节点的后驱节点successor。

https://documents.lucid.app/documents/dd1cae9b-6c19-4921-862c-3b204ee6bf61/pages/0_0?a=605&x=-650&y=429&w=1095&h=223&store=1&accept=image%2F*&auth=LCA%20c1ab284bd3afc7dd82749e958c8d337e40e297a1ca836efba548370510852519-ts%3D1692251338

代码实现

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        ListNode dummyHead = new ListNode(-501);
        dummyHead.next = head;

        ListNode start = findTarget(dummyHead, left);
        ListNode startPrev = findTarget(dummyHead, left - 1);
        ListNode end = findTarget(dummyHead, right);
        ListNode endNext = findTarget(dummyHead, right + 1);

        ListNode newHead = reverse(start, end);

        startPrev.next = newHead;
        start.next = endNext;
        
        return dummyHead.next;
    }

   private ListNode findTarget(ListNode dummyHead, int index) {
       ListNode cur = dummyHead;
       while (index > 0 && cur != null) {
           cur = cur.next;
           index--;
       }
       return cur;
   }

    private ListNode reverse(ListNode start, ListNode end) {
        ListNode prev = null;
        ListNode cur = start;

        while (prev != end) {
            ListNode next = cur.next;
            cur.next = prev;
            prev = cur;
            cur = next;
        }

        return prev;
    }
}
```

## 递归思想

我们已经知道了如何使用递归思想翻转单链表的前k个节点。同样的，这种思想也可以使用在翻转单链表区间[m, n]。 

Start from head, reverse from m node to n node. 

Start from head.next, reverse from m - 1 node to n - 1 node. 

Start from head.next.next, reverse from m - 2 node to n - 2 node.

If we reach a node where we need to reverse “1st” node to n - m + 1 node, we can apply the same algorithm of reverse top kth nodes in a linked list.

https://documents.lucid.app/documents/dd1cae9b-6c19-4921-862c-3b204ee6bf61/pages/0_0?a=873&x=-683&y=730&w=1371&h=639&store=1&accept=image%2F*&auth=LCA%2031c60025b225c04ab21e38668daaae1454c8ca7cab46edb8746fef166094e54a-ts%3D1692251338

**明确递归函数定义**

ListNode reverse(ListNode head, int m, int n) reverse a linked list interval [m, n] and return head.

ListNode reverse(ListNode head, int k) reverse a linked list top kth nodes and return new head.

**代码实现**

```java
class Solution {
    public ListNode reverseBetween(ListNode head, int left, int right) {
        if (left == 1) {
            return reverseTopK(head, right);
        }

        head.next = reverseBetween(head.next, left - 1, right - 1);

        return head;
    }

    ListNode successor = null;

    private ListNode reverseTopK(ListNode head, int k) {
        if (k == 1) {
            successor = head.next;
            return head;
        }    

        ListNode newHead = reverseTopK(head.next, k - 1);
        head.next.next = head;
        head.next = successor;

        return newHead;
    }
}
```

# k个一组翻转单链表

[LC 25 Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)

Given a linked list, reverse the nodes of the list k at a time and return the modified list.

![Flowcharts - Page 1.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5c099ae5-ff0e-49dc-952b-576113f44653/Flowcharts_-_Page_1.jpeg)

Reverse groups of nodes of size k

![Flowcharts.jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/51a4346f-c0a8-4f87-ac70-12e4909edd56/Flowcharts.jpeg)

**Algorithm:**

Linked list has recursion property, that reverse groups of size k from head has same process as that reverse groups of size k from “next head by skipping k nodes” .

![Flowcharts (4).jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fbcc4cd5-85e2-44ce-9970-72de0a942892/Flowcharts_(4).jpeg)

1. split the original list into segments of size k.
    
    ![Flowcharts (2).jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/cdb029e6-fea9-4f0f-8226-ee2fd6737b45/Flowcharts_(2).jpeg)
    
2. Given the head, the next recursion’s head will be the head of the second segment.
    
    Our goal in this step is to find the next head of recursion, and pass it to the reverseKGroup function.
    
    We start from the original head, and count k steps to find the end. The end node is the next head for recursion, also the head of the second segment.
    
    ```java
    ListNode start, end;
    start = end = head;
    for (int i = 0; i < k; i++) {
    	end = end.next;
    }
    ```
    
    Define the function **ListNode reverseKGroup(ListNode head, int k);** to reverse nodes of group k and return a head.
    
    The next head that will be passed to the recursion call is the end.
    
3. Let’s look back what we have done. 
    1. We found the start and end node.
    2. We passed the end node to the next recursion call and get a head.
4. Assume that the recursion call “reverseKGroup” did the job for us, which is reversing a linked list k group and return a head.
    
    ![Flowcharts (5).jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/69ad2cdb-8bdd-4f2b-ab0b-b304091a5e91/Flowcharts_(5).jpeg)
    
5. The next step is to reverse the first segment 1 → 2, and link it to the reversed linked list processed by the original function.
    
    ![Flowcharts (6).jpeg](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d424fad5-2831-412a-bb7e-e64330b7f658/Flowcharts_(6).jpeg)
    

**Code**
