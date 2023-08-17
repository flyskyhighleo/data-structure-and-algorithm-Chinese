# 链表中双指针技巧

## 双指针分类

1. 快慢指针，定义fast, slow指针
2. 左右指针

## 单链表双指针基本技巧

1. 合并两个有序链表 （普通双指针）
2. 合并k个有序链表 （普通双指针）
3. 单链表的翻转 （普通双指针）
4. 寻找单链表中点 （快慢指针）
5. 寻找单链表中倒数第k个节点 （快慢指针）
6. 判断单链表是否成环并找出环的起点 （快慢指针）
7. 判断两个单链表是否相交并找出交点 （普通指针）

## 例题

1. [Merge Two Sorted List](https://leetcode.com/problems/merge-two-sorted-lists/)
    
    双指针技巧 + 虚拟节点。通过对比两个指针节点的数值，将小的节点链接在虚拟节点之后。
    
2. [Partition List](https://leetcode.com/problems/partition-list/)
    1. Link all nodes whose values are smaller than x to small dummy
    2. Link all nodes whose values are greater or equal to x to large dummy
    3. Link small and large
3. [Merge k Sorted Lists](https://leetcode.com/problems/merge-k-sorted-lists/)
    
    Goal is to find the smallest node among all current node heads.
    
    - naive approach: make k pointers and compare current k node heads, then link the smallest node to dummy. Time complexity for reach comparison is O(k). Total time complexity is more than O(k^N). Bad!
    - optimal approach: since our goal is to find the smallest node, we can have a priority queue to help us.
        1. initially, we put all heads into PQ.
        2. while the PQ is not empty, poll the smallest element from the PQ, and link it to dummy. worst case time complexity is O(N * log k)
4. [Remove Nth Node From End of List](https://leetcode.com/problems/remove-nth-node-from-end-of-list/)
    
    Goal is to find the prev node of the target. Two pass or one pass both work.
    
5. [Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/)
    
    Use two pointers, fast and slow. If cycle exists, fast will catch slow in the cycle, otherwise, they will never meet.
    
6. [Linked List Cycle II](https://leetcode.com/problems/linked-list-cycle-ii/)
    
    Use tow pointers to find the point where they meet in the cycle.
    
    Assume that slow moves s steps when arriving the meeting point, then the fast moves 2s. The difference s steps is k times cycle.
    
    Assume that distance from the start point to meeting point is m, then the distance from the meeting point is s-m, which is same with the distance from head to the start point.
    
    When two pointer meet in the cycle, set the fast back to head and move fast and slow one step until they meet.
    
    The meeting point is the start point in the cycle.
    
7. [Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/description/)
    
    Similar with cycle detection in linked list.
    
    We use two pointers in linked list to detect a cycle. Similarly in arrays.
    
    We define slow and fast.
    
    slow = nums[slow], fast = nums[nums[fast]]
    
    When there is a cycle in array with n + 1 numbers in range [1, n], the duplicated number is the cycle entry.
    
    We find the cycle when slow and fast meet.
    
8. [Intersection of Two Linked Lists](https://leetcode.com/problems/intersection-of-two-linked-lists/)

## 更多练习 https://leetcode.com/tag/linked-list/

# 单链表的翻转

## 翻转整个链表

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
    

## 翻转单链表的前k个节点

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

## 翻转单链表区间[m, n]

[LC 92 Reverse Linked List II](https://leetcode.com/problems/reverse-linked-list-ii/description/)

### 迭代思想

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

### 递归思想

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

## k个一组翻转单链表

[LC 25 Reverse Nodes in k-Group](https://leetcode.com/problems/reverse-nodes-in-k-group/)
