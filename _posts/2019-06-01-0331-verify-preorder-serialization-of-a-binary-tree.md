---
layout: post
title: 331. Verify Preorder Serialization of a Binary Tree
---
### Question
One way to serialize a binary tree is to use pre-order traversal. When we
encounter a non-null node, we record the node's value. If it is a null node,
we record using a sentinel value such as `#`.

    
    
         _9_
        /   \
       3     2
      / \   / \
     4   1  #  6
    / \ / \   / \
    # # # #   # #
    

For example, the above binary tree can be serialized to the string
`"9,3,4,#,#,1,#,#,2,#,6,#,#"`, where `#` represents a null node.

Given a string of comma separated values, verify whether it is a correct
preorder traversal serialization of a binary tree. Find an algorithm without
reconstructing the tree.

Each comma separated value in the string must be either an integer or a
character `'#'` representing `null` pointer.

You may assume that the input format is always valid, for example it could
never contain two consecutive commas such as `"1,,3"`.

**Example 1:**

    
    
    **Input:**"9,3,4,#,#,1,#,#,2,#,6,#,#"
    **Output:**true

**Example 2:**

    
    
    **Input:**"1,#"
    **Output:**false
    

**Example 3:**

    
    
    **Input:**"9,#,#,1"
    **Output:**false

### Solution 1
Some used stack. Some used the depth of a stack. Here I use a different
perspective. In a binary tree, if we consider null as leaves, then

  * all non-null node provides 2 outdegree and 1 indegree (2 children and 1 parent), except root
  * all null node provides 0 outdegree and 1 indegree (0 child and 1 parent).

Suppose we try to build this tree. During building, we record the difference
between out degree and in degree `diff` = `outdegree - indegree`. When the
next node comes, we then decrease `diff` by 1, because the node provides an in
degree. If the node is not `null`, we increase diff by `2`, because it
provides two out degrees. If a serialization is correct, diff should never be
negative and diff will be zero when finished.

    
    
    public boolean isValidSerialization(String preorder) {
        String[] nodes = preorder.split(",");
        int diff = 1;
        for (String node: nodes) {
            if (--diff < 0) return false;
            if (!node.equals("#")) diff += 2;
        }
        return diff == 0;
    }


### Solution 2
We just need to remember how many empty slots we have during the process.

Initially we have one ( for the root ).

for each node we check if we still have empty slots to put it in.

  * a null node occupies one slot.
  * a non-null node occupies one slot before he creates two more. the net gain is one.

* * *
    
    
    class Solution(object):
        def isValidSerialization(self, preorder):
            """
            :type preorder: str
            :rtype: bool
            """
            # remember how many empty slots we have
            # non-null nodes occupy one slot but create two new slots
            # null nodes occupy one slot
            
            p = preorder.split(',')
            
            #initially we have one empty slot to put the root in it
            slot = 1
            for node in p:
                
                # no empty slot to put the current node
                if slot == 0:
                    return False
                    
                # a null node?
                if node == '#':
                    # ocuppy slot
                    slot -= 1
                else:
                    # create new slot
                    slot += 1
            
            #we don't allow empty slots at the end
            return slot==0


### Solution 3
See detailed comments below. Time complexity is O(n), space is also O(n) for
the stack.

    
    
    public class Solution {
        public boolean isValidSerialization(String preorder) {
            // using a stack, scan left to right
            // case 1: we see a number, just push it to the stack
            // case 2: we see #, check if the top of stack is also #
            // if so, pop #, pop the number in a while loop, until top of stack is not #
            // if not, push it to stack
            // in the end, check if stack size is 1, and stack top is #
            if (preorder == null) {
                return false;
            }
            Stack<String> st = new Stack<>();
            String[] strs = preorder.split(",");
            for (int pos = 0; pos < strs.length; pos++) {
                String curr = strs[pos];
                while (curr.equals("#") && !st.isEmpty() && st.peek().equals(curr)) {
                    st.pop();
                    if (st.isEmpty()) {
                        return false;
                    }
                    st.pop();
                }
                st.push(curr);
            }
            return st.size() == 1 && st.peek().equals("#");
        }
    }



