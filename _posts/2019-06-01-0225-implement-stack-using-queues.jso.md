---
layout: post
title: 225. Implement Stack using Queues
---
### Question
Implement the following operations of a stack using queues.

  * push(x) -- Push element x onto stack.
  * pop() -- Removes the element on top of the stack.
  * top() -- Get the top element.
  * empty() -- Return whether the stack is empty.

 **Example:**

    
    
    MyStack stack = new MyStack();
    
    stack.push(1);
    stack.push(2);  
    stack.top();   // returns 2
    stack.pop();   // returns 2
    stack.empty(); // returns false

 **Notes:**

  * You must use _only_ standard operations of a queue -- which means only `push to back`, `peek/pop from front`, `size`, and `is empty` operations are valid.
  * Depending on your language, queue may not be supported natively. You may simulate a queue by using a list or deque (double-ended queue), as long as you use only standard operations of a queue.
  * You may assume that all operations are valid (for example, no pop or top operations will be called on an empty stack).

### Solution 1
    
    
    class Stack {
    public:
    	queue<int> que;
    	// Push element x onto stack.
    	void push(int x) {
    		que.push(x);
    		for(int i=0;i<que.size()-1;++i){
    			que.push(que.front());
    			que.pop();
    		}
    	}
    
    	// Removes the element on top of the stack.
    	void pop() {
    		que.pop();
    	}
    
    	// Get the top element.
    	int top() {
    		return que.front();
    	}
    
    	// Return whether the stack is empty.
    	bool empty() {
    		return que.empty();
    	}
    };


### Solution 2
    
    
    class MyStack 
    {
        Queue<Integer> queue;
        
        public MyStack()
        {
            this.queue=new LinkedList<Integer>();
        }
        
        // Push element x onto stack.
        public void push(int x) 
        {
           queue.add(x);
           for(int i=0;i<queue.size()-1;i++)
           {
               queue.add(queue.poll());
           }
        }
    
        // Removes the element on top of the stack.
        public void pop() 
        {
            queue.poll();
        }
    
        // Get the top element.
        public int top() 
        {
            return queue.peek();
        }
    
        // Return whether the stack is empty.
        public boolean empty() 
        {
            return queue.isEmpty();
        }
    }


### Solution 3
 **Explanation:**

Just use a queue where you _"push to front"_ by pushing to back and then
rotating the queue until the new element is at the front (i.e., size-1 times
move the front element to the back).

* * *

 **C++:** 0 ms

    
    
     class Stack {
        queue<int> q;
    public:
        void push(int x) {
            q.push(x);
            for (int i=1; i<q.size(); i++) {
                q.push(q.front());
                q.pop();
            }
        }
    
        void pop() {
            q.pop();
        }
    
        int top() {
            return q.front();
        }
    
        bool empty() {
            return q.empty();
        }
    };
    

* * *

**Java:** 140 ms

    
    
     class MyStack {
    
        private Queue<Integer> queue = new LinkedList<>();
    
        public void push(int x) {
            queue.add(x);
            for (int i=1; i<queue.size(); i++)
                queue.add(queue.remove());
        }
    
        public void pop() {
            queue.remove();
        }
    
        public int top() {
            return queue.peek();
        }
    
        public boolean empty() {
            return queue.isEmpty();
        }
    }
    

* * *

**Python:** 36 ms

    
    
     class Stack:
    
        def __init__(self):
            self._queue = collections.deque()
    
        def push(self, x):
            q = self._queue
            q.append(x)
            for _ in range(len(q) - 1):
                q.append(q.popleft())
            
        def pop(self):
            return self._queue.popleft()
    
        def top(self):
            return self._queue[0]
        
        def empty(self):
            return not len(self._queue)



