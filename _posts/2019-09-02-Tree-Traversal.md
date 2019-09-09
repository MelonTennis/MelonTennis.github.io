---
layout: post
title: Tree Traversal
subtitle: Preorder, Inorder, Postorder
date: 2019-09-02
categories: Note
tags: [Tree]
description: 
catalog: true
---

## Tree Traversal

### Preorder Traversal

LC144.

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> res = new ArrayList<>();
        if(root == null)    return res;
        stack.add(root);
        while(!stack.isEmpty()) {
            TreeNode cur = stack.pop();
            res.add(cur.val);
            if(cur.right != null)    stack.push(cur.right);
            if(cur.left != null)   stack.push(cur.left);
        }
        return res;  
    } 
}
```

```java
class Solution {
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        preorder(root, list);
        return list;
    }
    
    private void preorder(TreeNode node, List<Integer> arr) {
        if(node == null)    return;
        arr.add(node.val);
        preorder(node.left, arr);
        preorder(node.right, arr);
    }
}
```



### Inorder Traversal

LC94

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if(root == null)    return res;
        Stack<TreeNode> stack = new Stack<>();
        TreeNode cur = root;
        while(cur != null || !stack.isEmpty()) {
            while(cur != null) {
                stack.push(cur);
                cur = cur.left;
            }
            cur = stack.pop();
            res.add(cur.val);
            cur = cur.right;
        }
        return res;
    }
}
```

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        inorder(res, root);
        return res;
    }
    
    private void inorder(List<Integer> res, TreeNode node) {
        if(node == null)    return;
        inorder(res, node.left);
        res.add(node.val);
        inorder(res, node.right);
    }
}
```



### Postorder Traversal

LC145

```java
public class Solution {
    public List<Integer> postorderTraversal(TreeNode root) {
        LinkedList<Integer> res = new LinkedList<>();
        Deque<TreeNode> stack = new ArrayDeque<>();
        if(root == null)    return res;
        TreeNode pre = null;
        stack.push(root);
        while(!stack.isEmpty()) {
            TreeNode cur = stack.peek();
            // push node till leaf
            if(pre == null || pre.left == cur || pre.right == cur) {
                if(cur.left != null)    stack.push(cur.left);
                else if(cur.right != null)  stack.push(cur.right);
            // pop from leaf
            } else if(pre == cur.left) {
                if(cur.right != null)   stack.push(cur.right);
            } else if(cur == pre.right || cur == pre) {
                res.add(cur.val);
                stack.pop();  
            }
            pre = cur;
        }
        return res;
    }
}
```

```java
public class Solution {
    List<Integer> res = new ArrayList<Integer>();
    public List<Integer> postorderTraversal(TreeNode root) {
        postorder(root);
        return res;
    }
    
    private void postorder(TreeNode root){
        if(root == null)    return;
        postorder(root.left);
        postorder(root.right);
        res.add(root.val);
    }  
}
```

