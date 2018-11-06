# Problems

| Table of Contents |
|-----------------|
| [Strings](#strings) |
| [Linked Lists](#linked-lists) |
| [Graphs](#graphs) |

---

## Strings

### Check if s2 is a rotation of s1 (e.g. 'waterbottle' and 'erbottlewat') with one call to `isSubstring`

`isSubstring(s2, s1 + s1)`

## Linked Lists

### Check for intersection in singly LL's

1. Two pointers that run through their entire list. If tails are not equal, no intersection.
2. Now that we have lengths, move pointers back to list starts, and advance the longer list's pointer by the difference in lengths
3. Traverse together until they equal

### Return start of a circular loop

Is there a circular loop? Use Fast (2 steps)/Slow (1 step). If a circular loop exists, they must collide.

If there is a circular loop, start Fast/Slow at beginning of the LL.
1. After k steps, when Slow enters the loop, Fast has travelled 2k, so it is k into the loop. So Fast is N - k behind Slow, where N = size of the loop.
2. Each Slow step, Fast gets 1 step closer to slow. So they collide after N - k steps, and Slow is N - k nodes into the loop. 
3. Both Slow and the start of the LL are k nodes from the loop. So move Fast to the start of the loop and step both pointers one node at a time, until they collide.

Implementation:
1. Have a Fast and Slow.
2. When they collide, move Fast to LLHead.
3. Move Slow and Fast at a rate of 1 step until they collide. Return the new collision point.

---
