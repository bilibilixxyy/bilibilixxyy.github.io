---
layout: post
title: 806. Domino and Tromino Tiling
---
### Question
We have two types of tiles: a 2x1 domino shape, and an "L" tromino shape.
These shapes may be rotated.

    
    
    XX  <- domino
    
    XX  <- "L" tromino
    X
    

Given N, how many ways are there to tile a 2 x N board? **Return your answer
modulo 10^9 + 7**.

(In a tiling, every square must be covered by a tile. Two tilings are
different if and only if there are two 4-directionally adjacent cells on the
board such that exactly one of the tilings has both squares occupied by a
tile.)

    
    
    **Example:**
    **Input:** 3
    **Output:** 5
    **Explanation:** 
    The five different ways are listed below, different letters indicates different tiles:
    XYZ XXZ XYY XXY XYY
    XYZ YYZ XZZ XYY XXY

 **Note:**

  * N  will be in range `[1, 1000]`.

### Solution 1
when N==0, we need return 0, but in dp , we need make dp[0]=1 for easy to
construct formula  
![image](https://s3-lc-
upload.s3.amazonaws.com/users/zhengkaiwei/image_1519539268.png)  
sorry my handwriting is ugly

dp[n]=dp[n-1]+dp[n-2]+ 2*(dp[n-3]+...+d[0])  
=dp[n-1]+dp[n-2]+dp[n-3]+dp[n-3]+2*(dp[n-4]+...+d[0])  
=dp[n-1]+dp[n-3]+(dp[n-2]+dp[n-3]+2*(dp[n-4]+...+d[0]))  
=dp[n-1]+dp[n-3]+dp[n-1]  
=2*dp[n-1]+dp[n-3]

    
    
     int numTilings(int N) {
        int md=1e9;
        md+=7;
        vector<long long> v(1001,0);
        v[1]=1;
        v[2]=2;
        v[3]=5;
        if(N<=3)
            return v[N];
        for(int i=4;i<=N;++i){
            v[i]=2*v[i-1]+v[i-3]; 
            v[i]%=md;
        }
        return v[N];
        
    }
    


### Solution 2
First, how many types of tiles do we have (including rotation)?

The answer is six: two types from domino and four types from trimino. I label
them as follows:

type 1: ![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519580711.png)representing
vertical domino  
type 2: ![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519580764.png)representing
horizontal domino  
type 3: ![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519580948.png)representing
`L`-shaped trimino  
type 4: ![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519580968.png)representing
`Gamma`-shaped trimino  
type 5: ![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519581008.png) representing
mirrored-`L`-shaped trimino  
type 6: ![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519581060.png)representing
mirrored-`Gamma`-shaped trimino

Now let's define `T(N)` as the number of ways tiling the `2 x N` board. To
obtain the recurrence relations for `T(N)`, we shall consider the very **last
tile** in the `2 x N` board (this is the tile which occupies at least one of
the two grids at index `N` and completes the `2 x N` board). So what type can
it be?

Immediately we can rule out type 3 and 4, because they will never complete the
board, thus cannot be the last tile. So we end up with four choices for the
last tile:

  1.  **The last tile is of type 1** : in this case, the rest tiles will fill up the `2 x (N-1)` board, then by definition, the number of ways for this case will be `T(N-1)`.

  2.  **The last tile is of type 2** : in this case, the second to last tile must also be of type 2 (so they together fill up the last `2 x 2` region), and the rest tiles will fill up the `2 x (N-2)` board, again by definition, the number of ways for this case will be `T(N-2)`.

  3.  **The last tile is of type 5** : in this case, the rest tiles must fill up the `2 x (N-1)` board except for the lower grid at index `N-1`, like this shape: ![image](https://s3-lc-upload.s3.amazonaws.com/users/fun4leetcode/image_1519582901.png). Our definition of `T(N)` does not cover this case, so we have to generalize it and define `T_up(N)` as the the number of ways to fill a `2 x N` board except for the last lower grid. Then the number of ways for this case will be `T_up(N-1)`.

  4.  **The last tile is of type 6** : in this case, the rest tiles must fill up the `2 x (N-1)` board except for the upper grid at index `N-1`, like this shape: ![image](https://s3-lc-upload.s3.amazonaws.com/users/fun4leetcode/image_1519583189.png). Our definition of `T(N)` does not cover this case either, so we define `T_down(N)` as the the number of ways to fill a `2 x N` board except for the last upper grid. Then the number of ways for this case will be `T_down(N-1)`.

It's easy to show that the four cases will not overlap with each other
(because at least the last tile will be different), and since the last tile
must be one of the four cases, we conclude:

`T(N) = T(N-1) + T(N-2) + T_up(N-1) + T_down(N-1)`

This looks like a recurrence formula, except that we do not know `T_up(N)` and
`T_down(N)` yet. To figure them out, we can follow exactly the same analyses
above by considering the very last tile for each of them.

Take `T_up(N)` as an example. We want to fill up a board shape like this:
![image](https://s3-lc-
upload.s3.amazonaws.com/users/fun4leetcode/image_1519583923.png). What could
the type of the very last tile be? The answer is: **type 2** (horizontal
domino) and **type 4** (`Gamma`-shaped trimino). For the former, the rest of
tiles will fill up a `2 x (N-1)` board except for the last upper grid, and by
definition, there are `T_down(N-1)` ways to do this. For the latter, the rest
tiles will fill up a `2 x (N-2)` board completely, and by definition, there
are `T(N-2)` ways to do so. So we conclude:

`T_up(N) = T_down(N-1) + T(N-2)`

Similarly we can obtain:

`T_down(N) = T_up(N-1) + T(N-2)`

And, there you go, we have found the recurrence relations for `T(N)`,
`T_up(N)`, `T_down(N)`:

`T(N) = T(N-1) + T(N-2) + T_up(N-1) + T_down(N-1)`

`T_up(N) = T_down(N-1) + T(N-2)`

`T_down(N) = T_up(N-1) + T(N-2)`

The termination conditions are as follows:

For `N = 0`, we have `T(0) = 1`, `T_up(0) = T_down(0) = 0`;  
For `N = 1`, we have `T(1) = 1`, `T_up(1) = T_down(1) = 0`.

Now it is straightforward to write the `O(N)` space solution. However, if you
notice that `T(N)`, `T_up(N)`, `T_down(N)` are only related to those at
indices `N-1` and `N-2`, the space can be cut down to `O(1)`. Here is a quick
implementation in Java:

    
    
    private static final int MOD = 1_000_000_007;
        
    public int numTilings(int N) {
        int T_prepre = 1, T_pre = 1;
        int T_up_pre = 0, T_down_pre = 0;
            
        for (int n = 2; n <= N; n++) {
            int T_cur = (int)((0L + T_prepre + T_pre + T_up_pre + T_down_pre) % MOD);
            int T_up_cur = (T_prepre + T_down_pre) % MOD;
            int T_down_cur = (T_prepre + T_up_pre) % MOD;
                
            T_prepre = T_pre;
            T_pre = T_cur;
                
            T_up_pre = T_up_cur;
            T_down_pre = T_down_cur;
        }
            
        return T_pre;
    }
    

  

Wait..., we are not done yet. It turns out that the recurrence relations for
`T(N)`, `T_up(N)`, `T_down(N)` can be combined into a single one. To see how,
first note that we have:

`T(N) = T(N-1) + T(N-2) + T_up(N-1) + T_down(N-1)`

`T_up(N-1) = T_down(N-2) + T(N-3)`

`T_down(N-1) = T_up(N-2) + T(N-3)`

Now plugging the second and third equations into the first one, we get:

`T(N) = T(N-1) + T(N-2) + T_down(N-2) + T(N-3) + T_up(N-2) + T(N-3)`

which can be regrouped as:

`T(N) = T(N-1) + T(N-3) + [T(N-2) + T(N-3) + T_up(N-2) + T_down(N-2)]`

Now if you recognize the part in **square brakets** which is simply `T(N-1)`,
we arrive at:

`T(N) = 2 * T(N-1) + T(N-3)`.

I would refer you to this [post](https://leetcode.com/problems/domino-and-
tromino-tiling/discuss/116581/Detail-and-explanation-of-O\(n\)-solution-why-
dpn2*dn-1+dpn-3) shared by [zhengkaiwei](https://leetcode.com/zhengkaiwei/)
for more explanations of the meaning of this formula. Anyway, the following is
the `O(N)` time and `O(1)` space based on this formula, where I used `p3`,
`p2`, `p1` to denote `T(N-3)`, `T(N-2)`, `T(N-1)` respectively, and
initialized them to `-1`, `0` and `1` to account for correct recurrence
values.

    
    
    private static final int MOD = 1_000_000_007;
        
    public int numTilings(int N) {
        int p3 = -1, p2 = 0, p1 = 1;
            
        for (int n = 1; n <= N; n++) {
            int cur = (int)((p1 * 2L + p3) % MOD);
            p3 = p2;
            p2 = p1;
            p1 = cur;
        }
            
        return p1;
    }
    


### Solution 3
The way to solve is too simple ,it's easy to understand by see the pictrue
drawing here:

![image](https://s3-lc-
upload.s3.amazonaws.com/users/yuweiming70/image_1519549786.png)

so the code can be write as this:

    
    
        int numTilings(int N) 
        {
            long long g[1001],u[1001];
            int mod=1000000007;
            g[0]=0; g[1]=1; g[2]=2;
            u[0]=0; u[1]=1; u[2]=2;
            
            for(int i=3;i<=N;i++)
            {
                u[i] = (u[i-1] + g[i-1]           )   %mod;
                g[i] = (g[i-1] + g[i-2] + 2*u[i-2])   %mod;
            }
            return g[N]%mod;
        }
    



