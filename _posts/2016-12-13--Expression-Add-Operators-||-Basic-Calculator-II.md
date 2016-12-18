---
layout:     post
title:      " Expression Add Operators || Basic Calculator II"
subtitle:   " Divide and Conquer "
date:       2016-12-13
author:     "码代码的兔子"
header-img: "img/post/post-bg-jekyllblog.jpg"
catalog: true
tags:
    - Leetcode
    - Algorithm
    - Java
    - Divide and Conquer

---

### Expression Add Operators

#### Question

Given a string that contains only digits 0-9 and a target value, return all possibilities to add binary operators (not unary) +, -, or * between the digits so they evaluate to the target value.

Examples: 

```
"123", 6 -> ["1+2+3", "123"] 
"232", 8 -> ["23+2", "2+32"]
"105", 5 -> ["1*0+5","10-5"]
"00", 0 -> ["0+0", "0-0", "0*0"]
"3456237490", 9191 -> []
```

#### Analysis

> [Java Standard Backtrace AC Solutoin, short and clear](https://discuss.leetcode.com/topic/24523/java-standard-backtrace-ac-solutoin-short-and-clear)

> [中文参考](https://segmentfault.com/a/1190000003797204)

#### Code

```java
public class Solution {
    public List<String> addOperators(String num, int target) {
        List<String> res=new ArrayList<>();
        if(num==null||num.length()==0)    return res;
        helper(res,"",num,0,target,0,0);
        return res;
    }
    
    private void helper(List<String> res, String path, String num, long cal, int target, int pos, long multi){
        if(pos==num.length()){
            if(cal==target){
                res.add(path);
                return ;
            }
        }
        for(int i=pos;i<num.length();i++){
            if(i!=pos&&num.charAt(pos)=='0')  break;
            long cur=Long.parseLong(num.substring(pos,i+1));
            if(pos==0){
                helper(res,path+cur,num,cur,target,i+1,cur);
            }else{
                helper(res,path+"+"+cur,num,cal+cur,target,i+1,cur);
                helper(res,path+"-"+cur,num,cal-cur,target,i+1,-cur);
                helper(res,path+"*"+cur,num,cal-multi+cur*multi,target,i+1,cur*multi);
            }
        }
    }
}
```

### Basic Calculator II

#### Question

Implement a basic calculator to evaluate a simple expression string.

The expression string contains only non-negative integers, +, -, *, / operators and empty spaces . The integer division should truncate toward zero.

You may assume that the given expression is always valid.

Some examples:

```
"3+2*2" = 7
" 3/2 " = 1
" 3+5 / 2 " = 5
```

#### Analysis

> [LeetCode Discuss Solution](https://discuss.leetcode.com/topic/16935/share-my-java-solution)

#### Code

```java
public class Solution {
    public int calculate(String s) {
        if(s==null||s.length()==0)  return 0;
        Stack<Integer> stack=new Stack<>();
        char sign='+';
        int num=0;
        int res=0;
        for(int i=0;i<s.length();i++){
            char c=s.charAt(i);
            if(Character.isDigit(c)){
                num=num*10+(c-'0');
            }
            if(!Character.isDigit(c)&&c!=' '||i==s.length()-1){ //i=length-1最后一个数必须加入结果res,且不可用else if，无法应对s="1"的状况
                if(sign=='-'){
                    stack.push(-num);
                }
                if(sign=='+'){
                    stack.push(num);
                }
                if(sign=='/'){
                    stack.push(stack.pop()/num);
                }
                if(sign=='*'){
                    stack.push(stack.pop()*num);
                }
                sign=c;
                num=0;
            }
        }
        for(int item:stack){
            res+=item;
        }
        return res;
    }
}
```

