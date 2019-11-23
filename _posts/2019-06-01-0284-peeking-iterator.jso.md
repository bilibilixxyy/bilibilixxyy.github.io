---
layout: post
title: 284. Peeking Iterator
---
### Question
Given an Iterator class interface with methods: `next()` and `hasNext()`,
design and implement a PeekingIterator that support the `peek()` operation --
it essentially peek() at the element that will be returned by the next call to
next().

 **Example:**

    
    
    Assume that the iterator is initialized to the beginning of the list: **[1,2,3]**.
    
    Call **next()** gets you **1** , the first element in the list.
    Now you call **peek()** and it returns **2** , the next element. Calling **next()** after that _**still**_ return **2**. 
    You call **next()** the final time and it returns **3** , the last element. 
    Calling **hasNext()** after that should return **false**.
    

**Follow up** : How would you extend your design to be generic and work with
all types, not just integer?

### Solution 1
    
    
    class PeekingIterator implements Iterator<Integer> {  
        private Integer next = null;
        private Iterator<Integer> iter;
    
        public PeekingIterator(Iterator<Integer> iterator) {
            // initialize any member here.
            iter = iterator;
            if (iter.hasNext())
                next = iter.next();
        }
        
        // Returns the next element in the iteration without advancing the iterator. 
        public Integer peek() {
            return next; 
        }
    
        // hasNext() and next() should behave the same as in the Iterator interface.
        // Override them if needed.
        @Override
        public Integer next() {
            Integer res = next;
            next = iter.hasNext() ? iter.next() : null;
            return res; 
        }
    
        @Override
        public boolean hasNext() {
            return next != null;
        }
    }
    

cache the next element. If next is null, there is no more elements in
iterator.

Edit: check AlexTheGreat's answer. It's better.

Edit after 2 years: the old solution didn't consider null values and
@AlexTheGreat already posted the correct solution but looks like no one is
checking his/her answer. So I took his/her answer as a reference:

    
    
    import java.util.NoSuchElementException;
    class PeekingIterator implements Iterator<Integer> {
        Integer next;
        Iterator<Integer> iter;
        boolean noSuchElement;
    
        public PeekingIterator(Iterator<Integer> iterator) {
    	// initialize any member here.
    	iter = iterator;
            advanceIter();
        }
    
        // Returns the next element in the iteration without advancing the iterator.
        public Integer peek() {
            // you should confirm with interviewer what to return/throw
            // if there are no more values
            return next;
        }
    
        // hasNext() and next() should behave the same as in the Iterator interface.
        // Override them if needed.
        @Override
        public Integer next() {
            if (noSuchElement)
                throw new NoSuchElementException();
            Integer res = next;
            advanceIter();
            return res;
        }
    
        @Override 
        public boolean hasNext() {
            return !noSuchElement;
        }
        
        private void advanceIter() {
            if (iter.hasNext()) {
                next = iter.next();
            } else {
                noSuchElement = true;
            }
        }
    }
    


### Solution 2
Since `Iterator` has a copy constructor, we can just use it:

    
    
    class PeekingIterator : public Iterator
    {
    public:
        PeekingIterator(const vector<int> &nums) : Iterator(nums)
        {
        }
    
        int peek()
        {
            return Iterator(*this).next();
        }
    
        int next()
        {
            return Iterator::next();
        }
    
        bool hasNext() const
        {
            return Iterator::hasNext();
        }
    };


### Solution 3
Store the next value outside the iterator. When next is called return the
stored value and populate with next value from iterator.

    
    
    class PeekingIterator(object):
        def __init__(self, iterator):
            self.iter = iterator
            self.temp = self.iter.next() if self.iter.hasNext() else None
    
        def peek(self):
            return self.temp
    
        def next(self):
            ret = self.temp
            self.temp = self.iter.next() if self.iter.hasNext() else None
            return ret
    
        def hasNext(self):
            return self.temp is not None



