# Remove Nodes in A Linked List

# Overview

A linked list has similar sub-structure which provides it with ********************recursion******************** property. Normally, to delete nodes in a singly linked list, we need to find the predecessor of target and link the predecessor to the next node of the target. This article talks about algorithm of removing nodes in a linked list from a new perspective of recursion.

At the end of this article, we introduces **************monotonic stack************** and ************prefix************ ********sum********. 

# Example Problems

## Remove Linked List Elements

[LC 203 Remove Linked List Elements](https://leetcode.com/problems/remove-linked-list-elements/)

To delete a node in a linked list, we need to find the predecessor and successor of the target nodes, then make the predecessor point to the successor.

![Screenshot 2023-08-27 at 1 10 24 PM](https://github.com/flyskyhighleo/data-structure-algorithm/assets/142456739/32359a6c-4b70-415f-a2f8-540921a664ca)

```java
class Solution {
    public ListNode removeElements(ListNode head, int val) {
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode prev = dummy;
        ListNode cur = head;

        while (cur != null) {
            if (cur.val == val) {
                prev.next = cur.next;
            } else {
                prev = cur;
            }
            cur = cur.next;
        }

        return dummy.next;
    }
}
```

Linked list has recursion property which provide us with another approach - **remove nodes recursively.**

That remove target elements from head is same with that remove elements from head.next. At the end we only need to handle if head is target.

Define function that remove all nodes with target value and return a head.

```java
ListNode remove(ListNode head, int val);
```

Assume that the removal function does the job for us. It removes all target nodes and return a new head.
![Screenshot 2023-08-21 at 10 49 40 PM](https://github.com/flyskyhighleo/data-structure-algorithm/assets/142456739/7677efa0-2744-4410-b877-e6edfdb301dd)
![Screenshot 2023-08-21 at 10 57 54 PM](https://github.com/flyskyhighleo/data-structure-algorithm/assets/142456739/5c5f9231-5df1-46a6-9062-695abc8ac50c)

Now we need to handle the head. We link the head to the new head returned by remove function.

And we consider the following cases:

1. head is not target. Nice, we simply return the head.
2. head is target. Don’t worry, we return new head.
    
    ```java
    class Solution {
        public ListNode removeElements(ListNode head, int val) {
            if (head == null) {
                return null;
            }
    
            ListNode newHead = removeElements(head.next, val);
    
            head.next = newHead;
    
            return head.val != val ? head : newHead;
        }
    }
    ```
    

## Remove Duplicates From an Unsorted Linked List

[LC 1836 Remove Duplicates From an Unsorted Linked List](https://leetcode.com/problems/remove-duplicates-from-an-unsorted-linked-list/description/)

This problem is almost the same with LC 203. We just need a simple modification.

We firstly count the number of nodes. When need to return head, we need to check if the head has duplicate values. 

```java
// get count for each value
private Map<Integer, Integer> getCount(ListNode head);
// check if the head has duplicate values
return count.get(head.val) == 1 ? head : newHead;
```

## Delete Node in a Linked List

![Screenshot 2023-08-27 at 2 09 13 PM](https://github.com/flyskyhighleo/data-structure-algorithm/assets/142456739/39df277f-ca3b-477b-bb76-965281d3dad3)


This problem is extension of [LC 203](https://leetcode.com/problems/remove-linked-list-elements/).

Description:

1. Given the target node to be deleted.
2. No access to head.
3. Target is not the tail node.
4. All values are unique.

To delete a node in a singly linked list, we need to find the target’s predecessor and successor, then change the reference of predecessor to the successor.

In this problem, we find that we are given the target node only. Therefore, there’s no way for us to find the predecessor of the target node.

Let’s see what we can get from the problem description and restriction.

1. Given the target node. We can access the list starting from the given node. For example, target.next.
2. No access to head. We can’t get and modify the nodes before the target node.

We can delete the next node of the target by change the reference of target to the successor of its next node.

![Screenshot 2023-08-27 at 2.09.13 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c8f7c385-e9b8-4b48-bb68-063decd82f4a/Screenshot_2023-08-27_at_2.09.13_PM.png)

However, this is not the requirement. Don’t worry, we can simple replace the target node’s value with the value of its next node. The condition “target is not the tail node” make sure that there’s no NPE.

![Screenshot 2023-08-27 at 2.10.36 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1daf7cec-f631-4a20-8b15-4c0a0b7ae75d/Screenshot_2023-08-27_at_2.10.36_PM.png)

```java
class Solution {
    public void deleteNode(ListNode node) {
        ListNode nextNode = node.next;
        node.next = nextNode.next;
        node.val = nextNode.val;
        nextNode.next = null;
    }
}
```

**Update the problem**

What if values are not unique in the singly linked list. Delete all nodes starting from the given node whose value is same with the target node’s value.

We just need to add a while loop to continually delete the target node.

```java
class Solution {
    public void deleteNode(ListNode node) {
        int targetVal = node.val;
        ListNode p = node;
        while (p != null) {
            while (p.val == targetVal) {
                delete(p);
            }
            p = p.next;
        }
    }

    private void delete(ListNode node) {
        ListNode nextNode = node.next;
        node.next = nextNode.next;
        node.val = nextNode.val;
        nextNode.next = null;
    }
}
```

## Remove Duplicates from Sorted List

[LC 83 Move Duplicates from Sorted List](https://leetcode.com/problems/remove-duplicates-from-sorted-list/description/)

**Recursion Approach**

Let’s define the function, and believe that this function does the job for us: remove duplicate nodes such that each element appears only once, and return a new head.

```java
ListNode deleteDuplicates(ListNode head);
```

We apply this function to the next node of head.

![Screenshot 2023-08-27 at 4.19.31 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/79e9109a-8aa0-48ee-ad30-2d12546f9d5d/Screenshot_2023-08-27_at_4.19.31_PM.png)

Now, we get a new head after applying the deleteDuplicates function to next node of head.

Next step is to link the original head to the newly returned head.

![Screenshot 2023-08-27 at 4.21.59 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5b97589f-48ec-4881-937e-e7e49054f156/Screenshot_2023-08-27_at_4.21.59_PM.png)

Now, all nodes starting from the newly returned head are unique. Our final step is to check if the original head is duplicated.

```java
return head.val == head.next.val ? head.next : head;
```

Implementation

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) {
            return head;
        }

        ListNode newHead = deleteDuplicates(head.next);
        head.next = newHead;

        return head.val == newHead.val ? newHead : head;
    }
}
```

**Iteration Approach**

We use two pointers, slow and fast. From head to slow, we maintain all unique nodes. We move fast point to find next different element, then link it to slow next.

**Since the linked list is sorted, it’s guaranteed that the different node found by fast point is different with any nodes from head to slow pointer.** 

```java
while (fast != null) {
	if (fast.val != slow.val) {
		slow.next = fast;
		slow = slow.next;
	}
	fast = fast.next;
}
slow.next = null;
```

## Remove Duplicates from Sorted List II

[LC 82 Remove Duplicates from Sorted List II](https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/description/)

A singly linked list is sorted. Remove all duplicate nodes.

**Recursion**

Let’s define the function to delete duplicates and return a head. We trust this function can do the job for us. 😊

```java
ListNode deleteDuplicates(ListNode head);
```

1. We firstly find the node whose next is different from it. This node the the start node.
    
    ![Screenshot 2023-08-27 at 11.37.29 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bd6c9393-30a7-4262-9f0f-3805916994fe/Screenshot_2023-08-27_at_11.37.29_PM.png)
    
2. Now, we need to consider how to handle head to start.
    1. if head is different from its next. We simply link head to the newly returned head.
        
        ![Screenshot 2023-08-27 at 11.43.37 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d4c46b75-f017-441e-9217-b4864219f29c/Screenshot_2023-08-27_at_11.43.37_PM.png)
        
    2. if head val is same with its next, we simply return the newly returned head because the original head should be deleted.

![Screenshot 2023-08-27 at 11.44.39 PM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0fb53bd8-0c8b-4a6e-88f7-fd65c5fc8f30/Screenshot_2023-08-27_at_11.44.39_PM.png)

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null) {
            return null;
        }
        
        ListNode start = head;
        while (start.next != null && start.val == start.next.val) {
            start = start.next;
        }
        ListNode newHead = deleteDuplicates(start.next);
        if (head.next != null && head.val == head.next.val) {
            return newHead;
        } else {
            head.next = newHead;
            return head;
        }
    }
}
```

**Iteration**

We maintain two pointers, prev and cur. The prev pointer always point to unique nodes. The cur pointer moves on nodes and skip all duplicates.

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode dummy = new ListNode(-1);
        ListNode prev = dummy;
        ListNode cur = head;

        while (cur != null) {
            if (cur.next != null && cur.next.val == cur.val) {
                while (cur.next != null && cur.next.val == cur.val) {
                    cur = cur.next;
                }
                cur = cur.next;
                if (cur == null) {
                    prev.next = null;
                }
            } else {
                prev.next = cur;
                prev = cur;
                cur = cur.next;
            }
        }

        return dummy.next;
    }
}
```

## Delete the Middle Node of a Linked List

[LC2095 Delete the Middle Node of a Linked List](https://leetcode.com/problems/delete-the-middle-node-of-a-linked-list/description/)

We use two pointers, slow and fast, to find the middle node of a linked list. To delete the middle node, we have to find its predecessor. Therefore, we define a dummy prev node.

```java
class Solution {
    public ListNode deleteMiddle(ListNode head) {
        
        if (head == null || head.next == null) {
            return null;
        }

        ListNode prev = new ListNode(-1);
        prev.next = head;
        ListNode slow, fast;
        slow = head;
        fast = head;

        while (fast != null && fast.next != null) {
            fast = fast.next.next;
            slow = slow.next;
            prev = prev.next;
        }

        prev.next = slow.next;

        return head;
    }
}
```

## Delete N Nodes After M Nodes of a Linked List

[LC1474 Delete N Nodes After M Nodes of a Linked List](https://leetcode.com/problems/delete-n-nodes-after-m-nodes-of-a-linked-list/description/)

```java
class Solution {
    public ListNode deleteNodes(ListNode head, int m, int n) {
        ListNode p = head;
        ListNode q = head;

        while (p != null && q != null) {
            for (int k = 0; k < m - 1; k++) {
                if (p != null) {
                    p = p.next;
                }
            }
            for (int k = 0; k < m + n; k++) {
                if (q != null) {
                    q = q.next;
                }
            }
            if (p == null) {
                break;
            }
            p.next = q;
            p = p.next;
        }

        return head;
    }
}
```

## Remove Nodes From Linked List

[LC2487 Remove Nodes From Linked List](https://leetcode.com/problems/remove-nodes-from-linked-list/)

This problem asks us to delete every node which has a node with a strictly greater valye anywhere to the right side of it.

Example: 5 → 2 → 13 → 3 → 8 → 1

We should delete nodes 5, 2, 3 because they have node whose value is greater than them on the right side of them.

**Brute Force**

For each node, we check if there’s a node on the right side of it that has a greater value.

Time complexity is O(n^2).

Bottleneck: To check if there’s a node with a greater value, we need to check every nodes on the right side of the current node, which takes O(n) time. Therefore, the overall time complexity is O(n^2).

**Monotonic Stack**

We delete a node if there’s a node with a greater value on the right side of it. We move rightward from the head node. For the current node, we continually pop the top of the stack if the current node value is greater than stack top. In other words, the node in stack are left side of the current node. If the current node is greater than nodes in stack, the nodes in stack should be removed.

Exmaple: 5 → 2 → 13 → 3 → 8 → 1

Current node is 5, stack is empty, push 5 into stack.

Current node is 2, stack top is 5. 2 is smaller 5, we keep 5 and push 2 into stack.

Current node is 13, pop top because top is 2, pop top because top is 5. Push 13 into stack.

Current node is 3, 3 can not remove 13 because it’s smaller. Push 3 into stack.

Current node is 8, pop top 3. Push 8 into stack. Now nodes in stack are 13, 8.

Current node is 1, cannot pop any nodes in stack. Push 1 into stack.

At the end, the stack has 13, 8, 1. Link these nodes as a linked list.

```java
class Solution {
    public ListNode removeNodes(ListNode head) {
        Stack<ListNode> stack = new Stack<>();
        ListNode cur = head;
        while (cur != null) {
            while (!stack.isEmpty() && cur.val > stack.peek().val) {
                stack.pop();
            }
            stack.push(cur);
            cur = cur.next;
        }
        ListNode dummy = new ListNode(-1);
        while (!stack.isEmpty()) {
            ListNode top = stack.pop();
            ListNode curHead = dummy.next;
            dummy.next = top;
            top.next = curHead;
        }
        return dummy.next;
    }
}
```

Such stack is monotonic stack. We continually pop the top until the current value is smaller/larger than current top.

This data structure is usually used to find the next bigger or smaller element. We will talk about more in Monotonic Stack section.

## Remove Zero Sum Consecutive Nodes from Linked List

[LC1171 Remove Zero Sum Consecutive Nodes from Linked List](https://leetcode.com/problems/remove-zero-sum-consecutive-nodes-from-linked-list/)

**Brute Force**

The brute force solution is straightforward. We start at the current node and continually sum the values from the current node rightward. However, the time complexity of this solution  is $O(N^2)$.

The bottleneck of brute force solution is that we need to calculate the sum for each node.

**Prefix Sum**

We want find out if there’s a sequence of nodes from p to q whose sum of values is 0. The prefix sum algorithm can help us achieve it.

**Prefix sum algorithm helps us calculate sum of of a consecutive sequence of nodes in O(1).**

Let’s use array as example to explain how prefix sum works.

Imaging there is an array nums = [3, 2, -5, 2, -1, 4] with length n = 6.

We define and initialize an array “preSum” with length n + 1.

$preSum[i] = preSum[i - 1] + nums[i - 1]$

```java
int[] preSum = new int[n + 1];
preSum[0] = 0;

for (int i = 1; i < n + 1; i++) {
	preSum[i] = preSum[i - 1] + nums[i - 1];
}
```

![Screenshot 2023-08-31 at 12.11.19 AM.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/309b522c-d5ed-4e66-94cb-e7915135c7c6/Screenshot_2023-08-31_at_12.11.19_AM.png)

**To get sum value of a consecutive sequence, e.g. from index i to index j, in the original array, we use the formula:**

$$
sum(nums[i, j]) = preSum[j + 1] - preSum[i]
$$

**Why this formula can return us the sum of a consecutive sequence?**

$$
preSum[i] = preSum[i-1] + nums[i-1]\\
nums[i-1] = preSum[i] - preSum[i-1]\\
nums[i] = preSum[i+1] - preSum[i]\\
nums[i+1] = preSum[i + 2] - preSum[i+1]\\
.\\
.\\
nums[j-1] = preSum[j] - preSum[j-1]\\
$$

Sum up the above equations.

$$
nums[i - 1] + nums[i] + nums[i+1] + ...+ nums[j-1] = preSum[j] - preSum[i-1]\\
sum(nums[i,j]) = preSum[j] - preSum[i-1]
$$

**Solution**

Let $preSum[p]$ be the sum from head to node p.

Let $preSum[q]$ be the sum from head to node q.

The sum between node p (exclusive) and node q (inclusive) is $sum(p, q]$.

$$
sum(p, q] = preSum[q] - preSum[p] = 0\\
preSum[p] = preSum[q]
$$

1. Pre-calculate the pre sum on each node.
2. Iterate every single node $p$ and sum up values. If there’s already a ********same******** pre-sum value exist, we’re confident that a node $q$ exists on the right side of $p$, such that $preSum[p] = preSum[q]$.
3. Linke node $p$ to node $q$ next.
