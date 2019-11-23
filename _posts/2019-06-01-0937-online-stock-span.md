---
layout: post
title: 937. Online Stock Span
---
### Question
Write a class `StockSpanner` which collects daily price quotes for some stock,
and returns the _span_  of that stock's price for the current day.

The span of the stock's price today is defined as the maximum number of
consecutive days (starting from today and going backwards) for which the price
of the stock was less than or equal to today's price.

For example, if the price of a stock over the next 7 days were `[100, 80, 60,
70, 60, 75, 85]`, then the stock spans would be `[1, 1, 1, 2, 1, 4, 6]`.



 **Example 1:**

    
    
     **Input:** ["StockSpanner","next","next","next","next","next","next","next"], [[],[100],[80],[60],[70],[60],[75],[85]]
    **Output:** [null,1,1,1,2,1,4,6]
    **Explanation:**
    First, S = StockSpanner() is initialized.  Then:
    S.next(100) is called and returns 1,
    S.next(80) is called and returns 1,
    S.next(60) is called and returns 1,
    S.next(70) is called and returns 2,
    S.next(60) is called and returns 1,
    S.next(75) is called and returns 4,
    S.next(85) is called and returns 6.
    
    Note that (for example) S.next(75) returned 4, because the last 4 prices
    (including today's price of 75) were less than or equal to today's price.
    



 **Note:**

  1. Calls to `StockSpanner.next(int price)` will have `1 <= price <= 10^5`.
  2. There will be at most `10000` calls to `StockSpanner.next` per test case.
  3. There will be at most `150000` calls to `StockSpanner.next` across all test cases.
  4. The total time limit for this problem has been reduced by 75% for C++, and 50% for all other languages.

### Solution 1
# Explanation

You can refer to the same problem 739. Daily Temperatures.

Push every pair of `<price, result>` to a stack.  
Pop lower price from the stack and accumulate the count.

One price will be pushed once and popped once.  
So `2 * N` times stack operations and `N` times calls.  
I'll say time complexity is `O(1)`  
  

 **C++:**

    
    
         stack<pair<int, int>> s;
        int next(int price) {
            int res = 1;
            while (!s.empty() && s.top().first <= price) {
                res += s.top().second;
                s.pop();
            }
            s.push({price, res});
            return res;
        }
    

**Java:**

    
    
        Stack< int[]> stack = new Stack<>();
        public int next(int price) {
            int res = 1;
            while (!stack.isEmpty() && stack.peek()[0] <= price)
                res += stack.pop()[1];
            stack.push(new int[]{price, res});
            return res;
        }
    

**Python:**

    
    
         def __init__(self):
            self.stack = []
    
        def next(self, price):
            res = 1
            while self.stack and self.stack[-1][0] <= price:
                res += self.stack.pop()[1]
            self.stack.append([price, res])
            return res
    

  

# Similar Problems

@Sithis suggest some similar problems to practice monotone stack:

  496. Next Greater Element I
  497. Next Greater Element II
  498. Daily Temperatures
  499. Sum of Subarray Minimums
  500. Largest Rectangle in Histogram


### Solution 2
Store the prices in a stack along with the current index which acts as a
counter. Whenever next is called:  
If stack is empty  
//store the price with current index and return 1  
else  
//Remove the prices from the stack for which the price is lesser than or equal
to the current price.  
//Now push the current price and return the difference of the indexes of
current price and the index of top element in stack.  
//this difference is the number of elements lesser than or equal to the
current price.

    
    
    class StockSpanner {
        stack<pair<int, int>> storage;
        int index = 0;
    public:
        StockSpanner() {
            
        }
        
        int next(int price) {
            if (storage.empty()) {
                storage.push({price, index++});
                return 1;
            } else {
                while (!storage.empty() && price >= storage.top().first) {
                    storage.pop();
                }
                int res = 0;
                res = storage.empty() ? index + 1 : index - storage.top().second;
                storage.push({price, index++});
                return res;
            }
        }
    };
    


### Solution 3
    
    
    class StockSpanner {
            Stack<Item> s = new Stack<>();
    
            public int next(int price) {
                int lessOrEqual = 1;
                while (!s.isEmpty() && s.peek().price <= price)
                    lessOrEqual += s.pop().lessOrEqual;
                s.push(new Item(lessOrEqual, price));
                return s.peek().lessOrEqual;
            }
    
            class Item {
                final int lessOrEqual, price;
    
                Item(int lessCount, int val) {
                    this.lessOrEqual = lessCount;
                    this.price = val;
                }
            }
        }



