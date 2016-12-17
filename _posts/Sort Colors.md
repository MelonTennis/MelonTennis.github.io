---
layout:     post
title:      " Sort Colors || Simplify Path"
subtitle:   " Array || String "
date:       2016-09-30
author:     "码代码的兔子"
header-img: "img/post/post-bg-jekyllblog.jpg"
catalog: true
tags:
    - Leetcode
    - Algorithm
    - Java
    - Two Pointers
    - Array
    - String
---

### Sort Colors

##### Question

Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

##### Analysis

若是采用遍历统计各颜色个数的方法，需要遍历一次，填充数组一次。所以采用两个指针，代表当前red、blue颜色的位置，当i指针扫描到这两种颜色的时候与其交换，最终通过one pass得到有序的数组。

- i所扫描过的都是有序的，而且start(red的指针)永远小于等于i，所以在交换完red颜色后i++，当前交换得来的元素无需再次判断。
- 但在与end(blue的指针)交换的时候，i不动，因为可能交换来的也是一个blue
- 在既不是red又不是blue的时候i直接移动

##### Code

```java
public class Solution {
    public void sortColors(int[] nums) {
       int size=nums.length;
       int start=0,end=size-1;
       int i=0;
       while(i<end+1){
           if(nums[i]==0){
               swap(nums,i,start);
               start++;
               i++;
               continue;
           }
           else if(nums[i]==2){
               swap(nums,i,end);
               end--;
               continue;
           }
           i++;
       }
    }
    
    private void swap(int[]nums, int a, int b){
        int tmp=nums[a];
        nums[a]=nums[b];
        nums[b]=tmp;
    }
}
```

### Simplify Path

##### Question 

Given an absolute path for a file (Unix-style), simplify it.

For example,
`path = "/home/", => "/home"`
`path = "/a/./b/../../c/", => "/c"`

##### Analysis

利用split将字符串分隔开，判断每个子串后决定下一步操作

- split内可利用正则表达式
- res需要有初值"/"，用以应对输入为"/"的用例
- size>0与str[i].equals("..")不可用&&连接后加入同一个if，这样会在size<=0的时候跳转到下一个if，并将**..**假如到结果中
- 最后截去结果的最后一个字符，因为会多一个'/'
- !str[i].equals("")用以处理两个'/'连续的情况

##### Code

```java
public class Solution {
    public String simplifyPath(String path) {
        String[] str=path.split("/+");
        String res="/";
        ArrayList<String> result=new ArrayList<String>();
        for(int i=0;i<str.length;i++){
            int size=result.size();
            if(str[i].equals("..")){
                if(size>0)
                    result.remove(size-1);
            }
            else if(!str[i].equals(".")&&!str[i].equals(""))
                result.add(str[i]);
        }
        for(String s:result){
            res+=s+'/';
        }
        if(res.length()>1)
            return res.substring(0,res.length()-1);
        return res;
    }
}
```



