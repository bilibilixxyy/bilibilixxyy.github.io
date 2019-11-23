---
layout: post
title: 1006. Vowel Spellchecker
---
### Question
Given a `wordlist`, we want to implement a spellchecker that converts a query
word into a correct word.

For a given `query` word, the spell checker handles two categories of spelling
mistakes:

  * Capitalization: If the query matches a word in the wordlist ( **case-insensitive** ), then the query word is returned with the same case as the case in the wordlist. 
    * Example: `wordlist = ["yellow"]`, `query = "YellOw"`: `correct = "yellow"`
    * Example: `wordlist = ["Yellow"]`, `query = "yellow"`: `correct = "Yellow"`
    * Example: `wordlist = ["yellow"]`, `query = "yellow"`: `correct = "yellow"`
  * Vowel Errors: If after replacing the vowels ('a', 'e', 'i', 'o', 'u') of the query word with any vowel individually, it matches a word in the wordlist ( **case-insensitive** ), then the query word is returned with the same case as the match in the wordlist. 
    * Example: `wordlist = ["YellOw"]`, `query = "yollow"`: `correct = "YellOw"`
    * Example: `wordlist = ["YellOw"]`, `query = "yeellow"`: `correct = ""` (no match)
    * Example: `wordlist = ["YellOw"]`, `query = "yllw"`: `correct = ""` (no match)

In addition, the spell checker operates under the following precedence rules:

  * When the query exactly matches a word in the wordlist ( **case-sensitive** ), you should return the same word back.
  * When the query matches a word up to capitlization, you should return the first such match in the wordlist.
  * When the query matches a word up to vowel errors, you should return the first such match in the wordlist.
  * If the query has no matches in the wordlist, you should return the empty string.

Given some `queries`, return a list of words `answer`, where `answer[i]` is
the correct word for `query = queries[i]`.



 **Example 1:**

    
    
     **Input:** wordlist = ["KiTe","kite","hare","Hare"], queries = ["kite","Kite","KiTe","Hare","HARE","Hear","hear","keti","keet","keto"]
    **Output:** ["kite","KiTe","KiTe","Hare","hare","","","KiTe","","KiTe"]



 **Note:**

  * `1 <= wordlist.length <= 5000`
  * `1 <= queries.length <= 5000`
  * `1 <= wordlist[i].length <= 7`
  * `1 <= queries[i].length <= 7`
  * All strings in `wordlist` and `queries` consist only of **english**  letters.

### Solution 1
For each word in the wordlist,  
get its the lower pattern and devowel pattern,

For each lower pattern, record the first such match to hashmap `cap`.  
For each vowel pattern, record the first such match to hashmap `vowel`.

For each query,  
check if it's in the `words` set,  
check if there is a match in `cap`,  
check if there is a match in `vowel`,  
otherwise return `""`.

 **Java:**

    
    
         public String[] spellchecker(String[] wordlist, String[] queries) {
            Set<String> words = new HashSet<>(Arrays.asList(wordlist));
            HashMap<String, String> cap = new HashMap<>();
            HashMap<String, String> vowel = new HashMap<>();
            for (String w : wordlist) {
                String lower = w.toLowerCase(), devowel = lower.replaceAll("[aeiou]", "#");
                cap.putIfAbsent(lower, w);
                vowel.putIfAbsent(devowel, w);
            }
            for (int i = 0; i < queries.length; ++i) {
                if (words.contains(queries[i])) continue;
                String lower = queries[i].toLowerCase(), devowel = lower.replaceAll("[aeiou]", "#");
                if (cap.containsKey(lower)) {
                    queries[i] = cap.get(lower);
                } else if (vowel.containsKey(devowel)) {
                    queries[i] = vowel.get(devowel);
                } else {
                    queries[i] = "";
                }
            }
            return queries;
        }
    

**C++:**

    
    
         vector<string> spellchecker(vector<string>& wordlist, vector<string> queries) {
            set<string> words(wordlist.begin(), wordlist.end());
            unordered_map<string, string> cap, vowel;
            for (string w : wordlist) {
                string lower = tolow(w), devowel = todev(w);
                cap.insert({lower, w});
                vowel.insert({devowel, w});
            }
            for (int i = 0; i < queries.size(); ++i) {
                if (words.count(queries[i])) continue;
                string lower = tolow(queries[i]), devowel = todev(queries[i]);
                if (cap.count(lower)) {
                    queries[i] = cap[lower];
                } else if (vowel.count(devowel)) {
                    queries[i] = vowel[devowel];
                } else {
                    queries[i] = "";
                }
            }
            return queries;
        }
    
        string tolow(string w) {
            for (auto & c: w)
                c = tolower(c);
            return w;
        }
    
        string todev(string w) {
            w = tolow(w);
            for (auto & c: w)
                if (c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u')
                    c = '#';
            return w;
        }
    

**Python:**

    
    
        def spellchecker(self, wordlist, queries):
            words = {w: w  for w in wordlist}
            cap = {w.lower(): w for w in wordlist[::-1]}
            vowel = {re.sub("[aeiou]", '#', w.lower()): w for w in wordlist[::-1]}
            return [words.get(w) or cap.get(w.lower()) or vowel.get(re.sub("[aeiou]", '#', w.lower())) or "" for w in queries]
    


### Solution 2
  * Register every word (w) in wordlist in related cap(capitalization) and vow(vowel error) hash table.
  * For every word (w) in queries, apply the stated precedence rules

    
    
    class Solution:
        def spellchecker(self, wordlist, queries):
            st, cap, vow = set(wordlist), {}, {}
            for w in wordlist:
                newC = w.lower()
                newW = "".join(c if c not in "aeiou" else "*" for c in newC)
                if newC not in cap:
                    cap[newC] = w
                if newW not in vow:
                    vow[newW] = w
            for i, w in enumerate(queries):
                if w in st:
                    pass
                elif w.lower() in cap:
                    queries[i] = cap[w.lower()]
                else:
                    new = "".join(c if c not in "aeiou" else "*" for c in w.lower())
                    if new in vow:
                        queries[i] = vow[new]
                    else:
                        queries[i] = ""
            return queries
    


### Solution 3
use TrieTree to improve query efficiency.  
One thing to note is that when init the trieTree you need to check every word
is built in the tree only once.  
when query word with case sensitivity or vowel sensiticity using dfs is ok,
what we need to do is to choose the min result.

    
    
    public class Solution {
    	class Node{
    		Node[] next;
    		char now;
    		boolean flag;//标志这里是否是一个完整的单词
    		int index;//标志这个单词在字典里的顺序
    		public Node(char now) {
    			this.now = now;
    			this.next = new Node[52];
    		}
    	}
    	public String[] spellchecker(String[] wordlist, String[] queries) {
            Node root = new Node(' ');
            buildTree(wordlist, root);
            String[] res = new String[queries.length];
            for(int i = 0;i < queries.length;++i) {
            	int match = matchSearch(queries[i], root);
            	if(match >= 0) {//精准匹配上了
            		res[i] = wordlist[match];
            		continue;
            	}
            	char[] chars = queries[i].toCharArray();
            	match = likeSearch(chars, root, 0);
            	if(match != Integer.MAX_VALUE) {//模糊匹配上了
            		res[i] = wordlist[match];
            		continue;
            	}
            	match = vowelSearch(chars, root, 0);
            	if(match != Integer.MAX_VALUE) {//元音匹配上了
            		res[i] = wordlist[match];
            		continue;
            	}
            	res[i] = "";//没有匹配上
            }
            return res;
        }
    	
    	//构造字典树
    	private void buildTree(String[] wordlist,Node root) {
    		Node now = root;
    		for(int i=0;i<wordlist.length;++i) {
    			char[] chars = wordlist[i].toCharArray();
    			for(int j=0;j<chars.length;++j) {
    				int id = 0;
    				if(chars[j]>='a') id = 26+chars[j]-'a';
    				else id = chars[j]-'A';
    				Node next = now.next[id];
    				if(next == null) {
    					now.next[id] = new Node(chars[j]);
    					next = now.next[id];
    				}
    				now = next;
    			}
    			if(!now.flag) {//检查是否已经被初始化
    				now.flag = true;
    				now.index = i;
    			}
    			now = root;//重新组织下一个单词
    		}
    		
    	}
    	//完全匹配
    	private int matchSearch(String word,Node root) {
    		Node now = root;
    		for(int i = 0;i < word.length();++i) {
    			char c = word.charAt(i);
    			int id = c >= 'a'?26+c-'a':c-'A';
    			Node next = now.next[id];
    			if(next == null) return -1;//表示没有匹配上
    			now = next;
    		}
    		return now.flag?now.index:-1;//如果这个词是单词 就返回下标 否则没有匹配上
    	}
    	
    	//模糊替换
    	private int likeSearch(char[] word,Node root,int index) {
    		if(index == word.length) return root.flag?root.index:Integer.MAX_VALUE;//如果这个词是单词 就返回下标 否则没有匹配上
    		int may = word[index] >= 'a' ? word[index]-'a' : word[index]-'A'+26;
    		int now = word[index] >= 'a' ? word[index]-'a'+26 : word[index]-'A';
    		int res = Integer.MAX_VALUE;
    		Node next = root.next[now];
    		if(next != null) {//有这个节点
    			res = likeSearch(word, next, index+1);
    		}
    		next = root.next[may];
    		if(next != null) {//有这个节点
    			res = Math.min(res, likeSearch(word, next, index+1));
    		}
    		return res;
    	}
    	//元音替换
    	private int vowelSearch(char[] word,Node root,int index) {
    		if(index == word.length) return root.flag?root.index:Integer.MAX_VALUE;//如果这个词是单词 就返回下标 否则没有匹配上
    		int res = Integer.MAX_VALUE;
    		if(word[index] == 'a'||word[index] == 'e'||word[index] == 'i'||word[index] == 'o'|| word[index] == 'u'||
    				word[index] == 'A'||word[index] == 'E'||word[index] == 'I'||word[index] == 'O'||word[index] == 'U') {
    			int[] may = new int[] {26,30,34,40,46,0,4,8,14,20};
    			for(int i:may) {
    				Node next = root.next[i];
    				if(next != null) res = Math.min(res, vowelSearch(word,next,index+1));
    			}
    		}else{//否则不是元音字母 仅触发大小写敏感匹配
    			int may = word[index] >= 'a' ? word[index]-'a' : word[index]-'A'+26;
    			int now = word[index] >= 'a' ? word[index]-'a'+26 : word[index]-'A';
    			Node next = root.next[now];
    			if(next != null) {
    				res = vowelSearch(word, next, index+1);
    			}
    			next = root.next[may];
    			if(next != null) {//有这个节点
    				res = Math.min(res, vowelSearch(word, next, index+1));
    			}
    		}
    		return res;
    	}
    	
    	
    }
    
    
    



