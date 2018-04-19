title: leetcode 79. Word Search
author: gsal
tags:
  - leetcode
  - 刷题
  - medium
  - ''
  - ''
categories:
  - 刷题
date: 2018-04-13 21:39:00
---
### [79\. Word Search](https://leetcode.com/problems/word-search/description/)

Difficulty: **Medium**



Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.
<!--more-->
For example,  
Given **board** =

```
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]
```

**word** = `"ABCCED"`, -> returns `true`,  
**word** = `"SEE"`, -> returns `true`,  
**word** = `"ABCB"`, -> returns `false`.  


#### Solution
```
class Solution {
    private boolean isMatch = false;
    private boolean[][] visited;
    public boolean exist(char[][] board, String word) {
        if (board.length == 0 || board[0].length == 0) {
            return false;
        }
        visited = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                dfs(word,0,board,i,j);
​
            }
        }
        
        return isMatch;
    }
    
    private boolean isValid(int i, int j, char[][] board) {
        //3
       int height = board.length;
        //4
        int width = board[0].length;
      
        if (i > height - 1 ||  j > width - 1 || i < 0 || j < 0){
            return false;
        }
        if(visited[i][j] == true) {
            return false;
        }
        return true;
        
    }
    
    private void dfs (String word, int idx, char[][]board, int i, int j) {
        if (!isValid(i,j,board)) {
            return;
        }
        if (isMatch) {
            return;
        }
​
        if (idx >= word.length()) {
            return;
        }
        if(idx == word.length() - 1 && word.charAt(idx) == board[i][j]) {
            isMatch = true;
            //ystem.out.println("MMMAtch:now word: " + word.substring(0,idx + 1));
        //stem.out.println("MMMAtch:"+ idx + "i :" + i + "j:" + j);
            for (int k = 0; k < visited.length; k++) {
                for (int h = 0; h < visited[0].length; h++) {
                   // System.out.print(visited[k][h]);
                }
                //stem.out.println(" ");
            }
            return;
        }
        //stem.out.println("now word: " + word.substring(0,idx + 1));
        //stem.out.println(idx + "i :" + i + "j:" + j);
        if(word.charAt(idx) == board[i][j]) {
              visited[i][j] = true;
              dfs(word,idx + 1,board,i + 1,j);
              dfs(word,idx + 1,board,i,j + 1);
              dfs(word,idx + 1,board,i - 1,j);
              dfs(word,idx + 1,board,i,j - 1);
              visited[i][j] = false;
        }
        return;
    }
}
```
思路：dfs即可。  
DFS使用栈，思路是对于图到的所有顶点u，如果u未被访问，则访问u所在的联通块。  
访问顶点u：设置u被访问，枚举从u出发可以到达的所有顶点v，如果v未被访问，则递归访问v  
```
DFS(u) {
    vis[u] = true;
        for (从u出发能到达的v) {
            if vis[v]  == false
            {
                DFS(v)
            }
     }
}
```
有时也可以不显式地定义一个visitted数组，可以直接更改图，访问过的改为0（如leetcode695），也可以有比较灵活的改动，比如递归调用的DFS可以有返回值之类的。  
还有一个小技巧要注意的是可以在递归调用的函数里判断是否这次调用时是否是valid的，而不是在调用这个函数时判断，这样就可以不管三七二十一调用这个函数就可以了，因为调用不valid也没有问题直接返回就可以了。  
回到这道题的话，最好在main方法里只调用一次dfs，其他的东西都扔到dfs本身去做。尽量能够简化写就简化写，可以帮助减少思维负担。  
除此之外，如果说需要像这道题一样保证每次只出现一次，就可以把visited在其所有的后续节点都遍历完了之后重新设为false。