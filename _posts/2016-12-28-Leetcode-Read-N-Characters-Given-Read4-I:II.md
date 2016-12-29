---
layout: post
title: Leetcode Read N Characters Given Read4 I/II
subtitle: LC157, LC158
date: 2016-12-28
categories: Leetcode
tags: [Design]
catalog: true
---

### LC157. Read N Characters Given Read4

The API: `int read4(char *buf)` reads 4 characters at a time from a file. The return value is the actual number of characters read. For example, it returns 3 if there is only 3 characters left in the file. By using the `read4` API, implement the function `int read(char *buf, int n)` that reads *n* characters from the file.

**Note:**
The `read` function will only be called once for each test case.

一开始没读懂题意，这题是说给一个read4的API，它能够从文件中读取4个char并存到buf这个地址中，并返回读取到的char数量。如果文件中不到4个char，那么就读取剩下的char。现在利用这个API实现一个readn的API，对一个文件只调用一次。

```java
/* The read4 API is defined in the parent class Reader4.
      int read4(char[] buf); */

public class Solution extends Reader4 {
    /**
     * @param buf Destination buffer
     * @param n   Maximum number of characters to read
     * @return    The number of characters read
     */
    public int read(char[] buf, int n) {
        char[] buffer = new char[4];
        int pos = 0;
        while (pos < n) {
            int temp = read4(buffer);
            int next = Math.min(n - pos, temp);
            for (int i= 0; i < next; i++) {
                buf[pos + i] = buffer[i];
            }
            pos += next;
            if(temp < 4)    break;
        }
        return pos;
    }
}
```

题意理解了之后利用pos指针记录读取的位置，最后返回读取的总数即可。

### LC158. Read N Characters Given Read4 II - Call multiple times 

这题和刚才的区别是对于一个文件可以调用多次readn，也就是说假设文件长度足够，第一次n = 1，第二次n = 2，总读取的char数目为3，分别返回1，2.

> [全局变量+指针](https://discuss.leetcode.com/topic/7094/a-simple-java-code)

```java
/* The read4 API is defined in the parent class Reader4.
      int read4(char[] buf); */

public class Solution extends Reader4 {
    /**
     * @param buf Destination buffer
     * @param n   Maximum number of characters to read
     * @return    The number of characters read
     */
    // it can be called for one buffer for several times
    // so used private static variables
    private int buffPtr = 0;
    private int buffCnt = 0;
    private char[] buffer = new char[4];
    public int read(char[] buf, int n) {
        int count = 0; // count of number read from buffer
        while(count < n){
            if(buffPtr == 0)    buffCnt = read4(buffer); 
            while(count < n && buffPtr < buffCnt)   buf[count++] = buffer[buffPtr++];
            if(buffCnt == buffPtr)  buffPtr = 0; // if this read4 has all been copied 
            if(buffCnt < 4) break; 
        }
        return count;
    }
}
```

因为每次读取起始位置的变化需要利用全局变量来记录。不断利用read4读取，直到read4读取 < 4或读取总数< n停止。