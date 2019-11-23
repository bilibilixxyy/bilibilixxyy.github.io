---
layout: post
title: 859. Design Circular Deque
---
### Question
Design your implementation of the circular double-ended queue (deque).

Your implementation should support following operations:

  * `MyCircularDeque(k)`: Constructor, set the size of the deque to be k.
  * `insertFront()`: Adds an item at the front of Deque. Return true if the operation is successful.
  * `insertLast()`: Adds an item at the rear of Deque. Return true if the operation is successful.
  * `deleteFront()`: Deletes an item from the front of Deque. Return true if the operation is successful.
  * `deleteLast()`: Deletes an item from the rear of Deque. Return true if the operation is successful.
  * `getFront()`: Gets the front item from the Deque. If the deque is empty, return -1.
  * `getRear()`: Gets the last item from Deque. If the deque is empty, return -1.
  * `isEmpty()`: Checks whether Deque is empty or not. 
  * `isFull()`: Checks whether Deque is full or not.



 **Example:**

    
    
    MyCircularDeque circularDeque = new MycircularDeque(3); // set the size to be 3
    circularDeque.insertLast(1);			// return true
    circularDeque.insertLast(2);			// return true
    circularDeque.insertFront(3);			// return true
    circularDeque.insertFront(4);			// return false, the queue is full
    circularDeque.getRear();  			// return 2
    circularDeque.isFull();				// return true
    circularDeque.deleteLast();			// return true
    circularDeque.insertFront(4);			// return true
    circularDeque.getFront();			// return 4
    



 **Note:**

  * All values will be in the range of [0, 1000].
  * The number of operations will be in the range of [1, 1000].
  * Please do not use the built-in Deque library.

### Solution 1
    
    
    #include <vector>
    #include <iostream>
    
    using namespace std;
    
    class MyCircularDeque {
    private:
        vector<int> buffer;
        int cnt;
        int k;
        int front;
        int rear;
    public:
        /** Initialize your data structure here. Set the size of the deque to be k. */
        MyCircularDeque(int k): buffer(k, 0), cnt(0), k(k), front(k - 1), rear(0) {
        }
        
        /** Adds an item at the front of Deque. Return true if the operation is successful. */
        bool insertFront(int value) {
            if (cnt == k) {
                return false;
            }
            buffer[front] = value;
            front = (front - 1 + k) % k;
            ++cnt;
    
            return true;
        }
        
        /** Adds an item at the rear of Deque. Return true if the operation is successful. */
        bool insertLast(int value) {
            if (cnt == k) {
                return false;
            }
            buffer[rear] = value;
            rear = (rear + 1) % k;
            ++cnt;
    
            return true;
        }
        
        /** Deletes an item from the front of Deque. Return true if the operation is successful. */
        bool deleteFront() {
            if (cnt == 0) {
                return false;
            }
            front = (front + 1) % k;
            --cnt;
    
            return true;
        }
        
        /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
        bool deleteLast() {
            if (cnt == 0) {
                return false;
            }
            rear = (rear - 1 + k) % k;
            --cnt;
    
            return true;
        }
        
        /** Get the front item from the deque. */
        int getFront() {
            if (cnt == 0) {
                return -1;
            }
            return buffer[(front + 1) % k];
        }
        
        /** Get the last item from the deque. */
        int getRear() {
            if (cnt == 0) {
                return -1;
            }
            return buffer[(rear - 1 + k) % k];
        }
        
        /** Checks whether the circular deque is empty or not. */
        bool isEmpty() {
            return cnt == 0;
        }
        
        /** Checks whether the circular deque is full or not. */
        bool isFull() {
            return cnt == k;
        }
    };
    
    /**
     * Your MyCircularDeque object will be instantiated and called as such:
     * MyCircularDeque obj = new MyCircularDeque(k);
     * bool param_1 = obj.insertFront(value);
     * bool param_2 = obj.insertLast(value);
     * bool param_3 = obj.deleteFront();
     * bool param_4 = obj.deleteLast();
     * int param_5 = obj.getFront();
     * int param_6 = obj.getRear();
     * bool param_7 = obj.isEmpty();
     * bool param_8 = obj.isFull();
     */
    
    #if DEBUG
    int main(int argc, char** argv) {
        return 0;
    }
    #endif
    
    


### Solution 2
Straightforward solution, by using doubly linkedlist node

    
    
    class MyCircularDeque {
        int size;
        int k;
        DoubleListNode head;
        DoubleListNode tail;
        /** Initialize your data structure here. Set the size of the deque to be k. */
        public MyCircularDeque(int k) {
            head = new DoubleListNode(-1);
            tail = new DoubleListNode(-1);
            head.pre = tail;
            tail.next = head;
            this.k = k;
            this.size = 0;
        }
        
        /** Adds an item at the front of Deque. Return true if the operation is successful. */
        public boolean insertFront(int value) {
            if (size == k)
                return false;
            DoubleListNode node = new DoubleListNode(value);
            node.next = head;
            node.pre = head.pre;
            head.pre.next = node;
            head.pre = node;
            size++;
            return true;
        }
        
        /** Adds an item at the rear of Deque. Return true if the operation is successful. */
        public boolean insertLast(int value) {
            if (size == k)
                return false;
            DoubleListNode node = new DoubleListNode(value);
            node.next = tail.next;
            tail.next.pre = node;
            tail.next = node;
            node.pre = tail;
            size++;
            return true;
        }
        
        /** Deletes an item from the front of Deque. Return true if the operation is successful. */
        public boolean deleteFront() {
            if (size == 0)
                return false;
            head.pre.pre.next = head;
            head.pre = head.pre.pre;
            size--;
            return true;
        }
        
        /** Deletes an item from the rear of Deque. Return true if the operation is successful. */
        public boolean deleteLast() {
            if (size == 0)
                return false;
            tail.next.next.pre = tail;
            tail.next = tail.next.next;
            size--;
            return true;
        }
        
        /** Get the front item from the deque. */
        public int getFront() {
            return head.pre.val;
        }
        
        /** Get the last item from the deque. */
        public int getRear() {
            return tail.next.val;
        }
        
        /** Checks whether the circular deque is empty or not. */
        public boolean isEmpty() {
            return size == 0;
        }
        
        /** Checks whether the circular deque is full or not. */
        public boolean isFull() {
            return size == k;
        }
    }
    
    class DoubleListNode {
        DoubleListNode pre;
        DoubleListNode next;
        int val;
        public DoubleListNode(int val) {
            this.val = val;
        }
    }
    


### Solution 3
I started to implement this, with prevs etc. and then realized -- wait this
doesn't use any of the attributes of a circular deque... so then I just did it
with a LinkedList.

Weird naming



