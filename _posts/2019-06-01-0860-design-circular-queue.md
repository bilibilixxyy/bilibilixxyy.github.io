---
layout: post
title: 860. Design Circular Queue
---
### Question
Design your implementation of the circular queue. The circular queue is a
linear data structure in which the operations are performed based on FIFO
(First In First Out) principle and the last position is connected back to the
first position to make a circle. It is also called "Ring Buffer".

One of the benefits of the circular queue is that we can make use of the
spaces in front of the queue. In a normal queue, once the queue becomes full,
we cannot insert the next element even if there is a space in front of the
queue. But using the circular queue, we can use the space to store new values.

Your implementation should support following operations:

  * `MyCircularQueue(k)`: Constructor, set the size of the queue to be k.
  * `Front`: Get the front item from the queue. If the queue is empty, return -1.
  * `Rear`: Get the last item from the queue. If the queue is empty, return -1.
  * `enQueue(value)`: Insert an element into the circular queue. Return true if the operation is successful.
  * `deQueue()`: Delete an element from the circular queue. Return true if the operation is successful.
  * `isEmpty()`: Checks whether the circular queue is empty or not.
  * `isFull()`: Checks whether the circular queue is full or not.



 **Example:**

    
    
    MyCircularQueue circularQueue = new MyCircularQueue(3); // set the size to be 3
    circularQueue.enQueue(1);  // return true
    circularQueue.enQueue(2);  // return true
    circularQueue.enQueue(3);  // return true
    circularQueue.enQueue(4);  // return false, the queue is full
    circularQueue.Rear();  // return 3
    circularQueue.isFull();  // return true
    circularQueue.deQueue();  // return true
    circularQueue.enQueue(4);  // return true
    circularQueue.Rear();  // return 4
    



 **Note:**

  * All values will be in the range of [0, 1000].
  * The number of operations will be in the range of [1, 1000].
  * Please do not use the built-in Queue library.

### Solution 1
    
    
    class MyCircularQueue {
            final int[] a;
            int front, rear = -1, len = 0;
    
            public MyCircularQueue(int k) { a = new int[k];}
    
            public boolean enQueue(int val) {
                if (!isFull()) {
                    rear = (rear + 1) % a.length;
                    a[rear] = val;
                    len++;
                    return true;
                } else return false;
            }
    
            public boolean deQueue() {
                if (!isEmpty()) {
                    front = (front + 1) % a.length;
                    len--;
                    return true;
                } else return false;
            }
    
            public int Front() { return isEmpty() ? -1 : a[front];}
    
            public int Rear() {return isEmpty() ? -1 : a[rear];}
    
            public boolean isEmpty() { return len == 0;}
    
            public boolean isFull() { return len == a.length;}
        }


### Solution 2
    
    
    class Node:
        def __init__(self, value):
            self.val = value
            self.next = self.pre = None
    class MyCircularQueue:
    
        def __init__(self, k):
            self.size = k
            self.curSize = 0
            self.head = self.tail = Node(-1)
            self.head.next = self.tail
            self.tail.pre = self.head
    
        def enQueue(self, value):
            if self.curSize < self.size:
                node = Node(value)
                node.pre = self.tail.pre
                node.next = self.tail
                node.pre.next = node.next.pre = node
                self.curSize += 1
                return True
            return False
    
        def deQueue(self):
            if self.curSize > 0:
                node = self.head.next
                node.pre.next = node.next
                node.next.pre = node.pre
                self.curSize -= 1
                return True
            return False
    
        def Front(self):
            return self.head.next.val
    
        def Rear(self):
            return self.tail.pre.val
    
        def isEmpty(self):
            return self.curSize == 0
    
        def isFull(self):
            return self.curSize == self.size
    


### Solution 3
    
    
    class MyCircularQueue {
    public:
        /** Initialize your data structure here. Set the size of the queue to be k. */
        MyCircularQueue(int k) {
            data.resize(k);
            head = 0;
            tail = 0;
            reset = true;
        }
        
        /** Insert an element into the circular queue. Return true if the operation is successful. */
        bool enQueue(int value) {
            if (isFull()) return false;
            // update the reset value when first enqueue happens
            if (head == tail && reset) reset = false;
            data[tail] = value;
            tail = (tail + 1) % data.size();
            return true;
        }
        
        /** Delete an element from the circular queue. Return true if the operation is successful. */
        bool deQueue() {
            if (isEmpty()) return false;
            head = (head + 1) % data.size();
            // update the reset value when last dequeue happens
            if (head == tail && !reset) reset = true; 
            return true;
        }
        
        /** Get the front item from the queue. */
        int Front() {
            if (isEmpty()) return -1;
            return data[head];
        }
        
        /** Get the last item from the queue. */
        int Rear() {
            if (isEmpty()) return -1;
            return data[(tail + data.size() - 1) % data.size()];
        }
        
        /** Checks whether the circular queue is empty or not. */
        bool isEmpty() {
            if (tail == head && reset) return true;
            return false;
        }
        
        /** Checks whether the circular queue is full or not. */
        bool isFull() {
            if (tail == head && !reset) return true;
            return false;
        }
    private:
        vector<int> data;
        int head;
        int tail;
        // reset is the mark when the queue is empty
        // to differentiate from queue is full
        // because in both conditions (tail == head) stands
        bool reset;
    };
    
    /**
     * Your MyCircularQueue object will be instantiated and called as such:
     * MyCircularQueue obj = new MyCircularQueue(k);
     * bool param_1 = obj.enQueue(value);
     * bool param_2 = obj.deQueue();
     * int param_3 = obj.Front();
     * int param_4 = obj.Rear();
     * bool param_5 = obj.isEmpty();
     * bool param_6 = obj.isFull();
     */



