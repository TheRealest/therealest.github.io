---
title: Linked List Cycles
---

### Prompt

Write a function that returns true if a linked list contains a cycle, or false if it terminates somewhere.

Generally, we assume that a linked list will terminate in a null next pointer, as follows:
`A -> B -> C -> D -> E -> null`. A cycle in a linked list is when traversing the list would result in visiting the same nodes over and over. This is caused by pointing a node in the list to another node that already appeared earlier in the list. Example:

```
A -> B -> C
     ^    |
     |    v
     E <- D
```

Assume the linked list is made up of `Node` objects with the following implementation:

```js
var Node = function(value) {
  return { value: value, next: null };
};
```

### Solution

```js
var hasCycle = function(linkedList) {
  var cycleLength = 1;
  var finished = false;
  var cycle = false;
  var pointer = linkedList;

  var nthNode = function(node, n) {
    var current = node;
    for (var i = 0; i < n; i++) {
      if (current !== null) current = current.next;
    }
    return current;
  };

  while (!finished) {
    var newPointer = nthNode(pointer,cycleLength);
    if (newPointer === pointer) {
      finished = true;
      cycle = true;
    } else if (newPointer === null) {
      finished = true;
    }
    pointer = newPointer;
    cycleLength++;
  }

  return cycle;
};
```