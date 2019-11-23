---
layout: post
title: 388. Longest Absolute File Path
---
### Question
Suppose we abstract our file system by a string in the following manner:

The string `"dir\n\tsubdir1\n\tsubdir2\n\t\tfile.ext"` represents:

    
    
    dir
        subdir1
        subdir2
            file.ext
    

The directory `dir` contains an empty sub-directory `subdir1` and a sub-
directory `subdir2` containing a file `file.ext`.

The string `"dir subdir1 file1.ext subsubdir1 subdir2 subsubdir2 file2.ext"`
represents:

    
    
    dir
        subdir1
            file1.ext
            subsubdir1
        subdir2
            subsubdir2
                file2.ext
    

The directory `dir` contains two sub-directories `subdir1` and `subdir2`.
`subdir1` contains a file `file1.ext` and an empty second-level sub-directory
`subsubdir1`. `subdir2` contains a second-level sub-directory `subsubdir2`
containing a file `file2.ext`.

We are interested in finding the longest (number of characters) absolute path
to a file within our file system. For example, in the second example above,
the longest absolute path is `"dir/subdir2/subsubdir2/file2.ext"`, and its
length is `32` (not including the double quotes).

Given a string representing the file system in the above format, return the
length of the longest absolute path to file in the abstracted file system. If
there is no file in the system, return `0`.

 **Note:**  

  * The name of a file contains at least a `.` and an extension.
  * The name of a directory or sub-directory will not contain a `.`.

Time complexity required: `O(n)` where `n` is the size of the input string.

Notice that `a/aa/aaa/file1.txt` is not the longest file path, if there is
another path `aaaaaaaaaaaaaaaaaaaaa/sth.png`.

### Solution 1
    
    
     public int lengthLongestPath(String input) {
            Deque<Integer> stack = new ArrayDeque<>();
            stack.push(0); // "dummy" length
            int maxLen = 0;
            for(String s:input.split("
    ")){
                int lev = s.lastIndexOf("	")+1; // number of "	"
                while(lev+1<stack.size()) stack.pop(); // find parent
                int len = stack.peek()+s.length()-lev+1; // remove "/t", add"/"
                stack.push(len);
                // check if it is file
                if(s.contains(".")) maxLen = Math.max(maxLen, len-1); 
            }
            return maxLen;
        }
    

An even shorter and faster solution using array instead of stack:

    
    
    public int lengthLongestPath(String input) {
        String[] paths = input.split("
    ");
        int[] stack = new int[paths.length+1];
        int maxLen = 0;
        for(String s:paths){
            int lev = s.lastIndexOf("	")+1, curLen = stack[lev+1] = stack[lev]+s.length()-lev+1;
            if(s.contains(".")) maxLen = Math.max(maxLen, curLen-1);
        }
        return maxLen;
    }
    


### Solution 2
The number of tabs is my `depth` and for each depth I store the current path
length.

    
    
    def lengthLongestPath(self, input):
        maxlen = 0
        pathlen = {0: 0}
        for line in input.splitlines():
            name = line.lstrip('	')
            depth = len(line) - len(name)
            if '.' in name:
                maxlen = max(maxlen, pathlen[depth] + len(name))
            else:
                pathlen[depth + 1] = pathlen[depth] + len(name) + 1
        return maxlen


### Solution 3
After many try-and-error, I finally figured out the solution is required to
handle the blank spaces in a special way. I think it should be stated in the
problem statement, not just let people guess this from the expected answers.
This will waste many people a lot of time....



