---
layout: post
title: 130. Surrounded Regions
---
### Question
Given a 2D board containing `'X'` and `'O'` ( **the letter O** ), capture all
regions surrounded by `'X'`.

A region is captured by flipping all `'O'`s into `'X'`s in that surrounded
region.

 **Example:**

    
    
    X X X X
    X O O X
    X X O X
    X O X X
    

After running your function, the board should be:

    
    
    X X X X
    X X X X
    X X X X
    X O X X
    

**Explanation:**

Surrounded regions shouldn’t be on the border, which means that any `'O'` on
the border of the board are not flipped to `'X'`. Any `'O'` that is not on the
border and it is not connected to an `'O'` on the border will be flipped to
`'X'`. Two cells are connected if they are adjacent cells connected
horizontally or vertically.

### Solution 1
  * First, check the four border of the matrix. If there is a element is  
'O', alter it and all its neighbor 'O' elements to '1'.

  * Then ,alter all the 'O' to 'X'

  * At last,alter all the '1' to 'O'

For example:

    
    
             X X X X           X X X X             X X X X
             X X O X  ->       X X O X    ->       X X X X
             X O X X           X 1 X X             X O X X
             X O X X           X 1 X X             X O X X
        
    
    class Solution {
    public:
    	void solve(vector<vector<char>>& board) {
            int i,j;
            int row=board.size();
            if(!row)
            	return;
            int col=board[0].size();
    
    		for(i=0;i<row;i++){
    			check(board,i,0,row,col);
    			if(col>1)
    				check(board,i,col-1,row,col);
    		}
    		for(j=1;j+1<col;j++){
    			check(board,0,j,row,col);
    			if(row>1)
    				check(board,row-1,j,row,col);
    		}
    		for(i=0;i<row;i++)
    			for(j=0;j<col;j++)
    				if(board[i][j]=='O')
    					board[i][j]='X';
    		for(i=0;i<row;i++)
    			for(j=0;j<col;j++)
    				if(board[i][j]=='1')
    					board[i][j]='O';
        }
    	void check(vector<vector<char> >&vec,int i,int j,int row,int col){
    		if(vec[i][j]=='O'){
    			vec[i][j]='1';
    			if(i>1)
    				check(vec,i-1,j,row,col);
    			if(j>1)
    				check(vec,i,j-1,row,col);
    			if(i+1<row)
    				check(vec,i+1,j,row,col);
    			if(j+1<col)
    				check(vec,i,j+1,row,col);
    		}
    	}
    };


### Solution 2
    
    
    class UF
    {
    private:
    	int* id;     // id[i] = parent of i
    	int* rank;  // rank[i] = rank of subtree rooted at i (cannot be more than 31)
    	int count;    // number of components
    public:
    	UF(int N)
    	{
    		count = N;
    		id = new int[N];
    		rank = new int[N];
    		for (int i = 0; i < N; i++) {
    			id[i] = i;
    			rank[i] = 0;
    		}
    	}
    	~UF()
    	{
    		delete [] id;
    		delete [] rank;
    	}
    	int find(int p) {
    		while (p != id[p]) {
    			id[p] = id[id[p]];    // path compression by halving
    			p = id[p];
    		}
    		return p;
    	}
    	int getCount() {
    		return count;
    	}
    	bool connected(int p, int q) {
    		return find(p) == find(q);
    	}
    	void connect(int p, int q) {
    		int i = find(p);
    		int j = find(q);
    		if (i == j) return;
    		if (rank[i] < rank[j]) id[i] = j;
    		else if (rank[i] > rank[j]) id[j] = i;
    		else {
    			id[j] = i;
    			rank[i]++;
    		}
    		count--;
    	}
    };
    
    class Solution {
    public:
        void solve(vector<vector<char>> &board) {
            int n = board.size();
            if(n==0)    return;
            int m = board[0].size();
            UF uf = UF(n*m+1);
            
            for(int i=0;i<n;i++){
                for(int j=0;j<m;j++){
                    if((i==0||i==n-1||j==0||j==m-1)&&board[i][j]=='O') // if a 'O' node is on the boundry, connect it to the dummy node
                        uf.connect(i*m+j,n*m);
                    else if(board[i][j]=='O') // connect a 'O' node to its neighbour 'O' nodes
                    {
                        if(board[i-1][j]=='O')
                            uf.connect(i*m+j,(i-1)*m+j);
                        if(board[i+1][j]=='O')
                            uf.connect(i*m+j,(i+1)*m+j);
                        if(board[i][j-1]=='O')
                            uf.connect(i*m+j,i*m+j-1);
                        if(board[i][j+1]=='O')
                            uf.connect(i*m+j,i*m+j+1);
                    }
                }
            }
            
            for(int i=0;i<n;i++){
                for(int j=0;j<m;j++){
                    if(!uf.connected(i*m+j,n*m)){ // if a 'O' node is not connected to the dummy node, it is captured
                        board[i][j]='X';
                    }
                }
            }
        }
    };
    

Hi. So here is my accepted code using **Union Find** data structure. The idea
comes from the observation that if a region is NOT captured, it is connected
to the boundry. So if we connect all the 'O' nodes on the boundry to a dummy
node, and then connect each 'O' node to its neighbour 'O' nodes, then we can
tell directly whether a 'O' node is captured by checking whether it is
connected to the dummy node.  
For more about Union Find, the first assignment in the algo1 may help:  
<https://www.coursera.org/course/algs4partI>


### Solution 3
Phase 1: "Save" every O-region touching the border, changing its cells to 'S'.  
Phase 2: Change every 'S' on the board to 'O' and everything else to 'X'.

    
    
    def solve(self, board):
        if not any(board): return
    
        m, n = len(board), len(board[0])
        save = [ij for k in range(m+n) for ij in ((0, k), (m-1, k), (k, 0), (k, n-1))]
        while save:
            i, j = save.pop()
            if 0 <= i < m and 0 <= j < n and board[i][j] == 'O':
                board[i][j] = 'S'
                save += (i, j-1), (i, j+1), (i-1, j), (i+1, j)
    
        board[:] = [['XO'[c == 'S'] for c in row] for row in board]
    

In case you don't like my last line, you could do this instead:

    
    
        for row in board:
            for i, c in enumerate(row):
                row[i] = 'XO'[c == 'S']



