---
layout: post
title: 138. Copy List with Random Pointer
category: LeetCode
---

```
A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.

Return a deep copy of the list.
```

The question is an extension of [deep-copying](https://stackoverflow.com/questions/184710/what-is-the-difference-between-a-deep-copy-and-a-shallow-copy) a linked list. 

A linked list is a [linear data structure](https://techdifferences.com/difference-between-linear-and-non-linear-data-structure.html). A deep-copy can be done by iterating through the linked-list. However, in this case,
the random linked-list can possiblly be a non-linear data structure. A cycle or short-cut may exsists because of the "random" pointer. 


# Solution

Starting doing this question by brute force. 

```
 //copy linked-list, create a new node for random and next 
 // recursively
 copy :: Node Int Node Node-> Node Int Node Node  
```

The critial issue for this question is the data structure can be non-linear. If one naively copys linked-list follow next and random pointer, one may end up with duplicated data nodes such that the node has been copied by following random and then being copied again by following next.

For exmaple, A's next is B and the random is B as well. If blindly copies random and next, it will be two B's. 

The reason we have this anomaly is that the blindly copying method creates a one-to-many mapping from the original node set (M) to the result node set (N). To make one-to-one mapping, we will need to have a mapping from old nodes to new nodes (R) so that when we saw an old node is in R, we know it has already been copied. For people who learned graph search, it is just like the magic visited array to store visited node to avoid infinite loop. 

A proper solution can be:

```python
class Solution(object):
  def __init__(self):
    self.visited = {}
    
  def copyRandomList(self, head):
    if head == None:
      return None
    if head in self.visited:
      return self.visited[head]
    node = RandomListNode(head.label)
    self.visited[head] = node
    
    node.next = self.copyRandomList(head.next)
    node.random = self.copyRandomList(head.random)
    return node
```

However, the code above uses recursion and [recursion isn't always good](https://stackoverflow.com/questions/41469031/is-recursion-a-bad-practice-in-general) .

Starting changing this to iterative solution by looking at the invariant and some properties of operations. For the data structure, the ramdom pointer is either null or pointed to some node in the list.

	$\forall$ node $\in$ list. node->random $\in$ {p| p = null $\vee$ $\exists$ q. q $\in$ list $\wedge$ p = q} 

So that we could confidently do naive copying of the linked-list by following the next field and create the mapping while copying. Once the mapping is constructed, we could start again from the beginning of the list and fill the random field. 


Both methods take O(n) where n is the length if the linked list. 

For JavaScript developers 

```
Please note that all keys in the square bracket notation are converted to String type, since objects in JavaScript can only have String type as key type. For example, in the above code, when the key obj is added to the myObj, JavaScript will call the obj.toString() method, and use this result string as the new key.

```

Wondering reasons? go ahead and look at this [V8 Object](https://github.com/v8/v8/blob/master/src/objects.cc#L903). 