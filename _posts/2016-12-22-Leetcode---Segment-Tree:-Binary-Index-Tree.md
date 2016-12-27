---
layout: post
title: Leetcode - Segment Tree/ Binary Index Tree
subtitle: LC315, LC308, LC307, LC218
date: 2016-12-22
categories: Leetcode
tags: [SegmentTree, BIT]
catalog: true
---

### LC 315 Count of Smaller Numbers After Self

Given an array nums[i], count the number of integers to the right of nums[i] and put them out in a list. 

~~这题我面试遇到了，把smaller改成larger，也是一样的呢~~

Naive BinarySearch: O(nlogn) 55s

For each n in the ***nums***, binary search its index, from the **end** of ***nums*** to the **start**, and copy the result into another list. 

```java
public class Solution {
    public List<Integer> countSmaller(int[] nums) {
        Integer[] ans = new Integer[nums.length];
        List<Integer> sorted = new ArrayList<Integer>();
        for(int i = nums.length - 1; i >= 0; i--){
            int index = findIndex(sorted, nums[i]);
            ans[i] = index;
            sorted.add(index, nums[i]);
        } // for i
        return Arrays.asList(ans);
    } // smaller
    
    public int findIndex(List<Integer> sorted, int cur){
        if(sorted.size() == 0)  return 0;
        int start = 0, end = sorted.size() - 1;
        if(sorted.get(end) < cur)   return end + 1;
        if(sorted.get(start) >= cur) return start;
        while(start < end){
            int mid = start + (end - start)/2;
            if(sorted.get(mid) < cur){
                start = mid + 1;
            }else{
                end = mid;
            }
        }
        if (sorted.get(start) >= cur) return start;
        return end;
    } // findIndex
}
```

Binary search tree: O(nlgn) 10 ms

~~这个是面试官推荐的解法呢 👇~~

>  [Discuss](https://discuss.leetcode.com/topic/31405/9ms-short-java-bst-solution-get-answer-when-building-bst)

When building the BST, maintain a record of the total number on it's left (smaller numbers), and duplicate numbers. When insertign new numbers, the answer should be the add of that two number of all nodes that turns right(not the number of itself). 

```java
public class Solution {
    class Node {
        Node left;
        Node right;
        int val;
        int sum;
        int dup = 1;
        public Node(int v, int s){
            val = v;
            sum = s;
        }
    } // Node
    
    public List<Integer> countSmaller(int[] nums) {
        // record result
        Integer[] ans = new Integer[nums.length];
        Node root = null;
        // build from end to begin
        for(int i = nums.length-1; i >=0; i--){
            root = insert(nums[i], root, ans, i, 0);
        } // for
        return Arrays.asList(ans); // int[] cannot use this method
    } // countSmaller
    
    // building BST and maintain sum and duplicate
    private Node insert(int num, Node node, Integer[] ans, int i, int preSum){
        if(node == null){   // the last item || new children
            node = new Node(num, 0);
            ans[i] = preSum;
        }else if(node.val == num){  // duplicate items
            node.dup++;
            ans[i] = preSum + node.sum;
        }else if(node.val > num){ // new left child
            node.sum++;
            node.left = insert(num, node.left, ans, i, preSum);
        }else{
            node.right = insert(num, node.right, ans, i, preSum + node.dup + node.sum);
        }
        return node;
    }
}
```

Binary Index tree: O(lg(maxValue)) O(maxValue)

>  [discuss](https://discuss.leetcode.com/topic/31154/complicated-segmentree-solution-hope-to-find-a-better-one/2)

完全想不出的解法QAQ 将所有的num阈值构造成BIT，利用BIT的结构特性对存在的节点进行计数。~~他们怎么什么都会🙄~~

```java
public class Solution {

    private void add(int[] bit, int i, int val) {
        for (; i < bit.length; i += i & -i) bit[i] += val;
    }

    private int query(int[] bit, int i) {
        int ans = 0;
        for (; i > 0; i -= i & -i) ans += bit[i];
        return ans;
    }

    public List<Integer> countSmaller(int[] nums) {
        int[] tmp = nums.clone();
        Arrays.sort(tmp);
        for (int i = 0; i < nums.length; i++) nums[i] = Arrays.binarySearch(tmp, nums[i]) + 1;
        int[] bit = new int[nums.length + 1];
        Integer[] ans = new Integer[nums.length];
        for (int i = nums.length - 1; i >= 0; i--) {
            ans[i] = query(bit, nums[i] - 1);
            add(bit, nums[i], 1);
        }
        return Arrays.asList(ans);
    }
}
```

### LC307. Range Sum Query - Mutable

Given an integer array *nums*, find the sum of the elements between indices *i* and *j* (*i* ≤ *j*), inclusive. The update(i, val) function modifies nums by updating the element at index  ***i*** to ***val***.

BIT入门题，抄起BIT开始写。

```java
public class NumArray {
    
    int[] tree;
    int[] num;
    int N;
    public NumArray(int[] nums) {
        N = nums.length;
        tree = new int[N + 1];
        num = new int[N];
        for(int i = 0; i < N; i++){
            num[i] = nums[i];
        } // construct a BIT
        for(int i = 0; i < N; i++)
            makeBIT(i);
    }

    void update(int i, int val) {
        int dif = val - num[i];
        num[i] += dif;
        i++;
        while(i <= N){
            tree[i] += dif;
            i += i & (-i);
        }
    }

    public int sumRange(int i, int j) {
        int sum1 = 0;
        int sum2 = 0;
        int idx1 = i, idx2 = j+1;
        System.out.println(idx2);
        while(idx1 > 0){
            sum1 += tree[idx1];
            idx1 -= (idx1 & -idx1);
        }
        while(idx2 > 0){
            sum2 += tree[idx2];
            idx2 -= (idx2 & -idx2);
        }
        return sum2 - sum1;
    }
    
    void makeBIT(int idx){
        int i = idx+1;
        int j = idx + 1;
        while( j > i-(i & -i)){
            tree[idx+1] += num[j-1];
            j--;
        }
    }
}


// Your NumArray object will be instantiated and called as such:
// NumArray numArray = new NumArray(nums);
// numArray.sumRange(0, 1);
// numArray.update(1, 10);
// numArray.sumRange(1, 2);
```

我的BIT更新方式应该是不太好，如果👇时间会好一些。

```java
public void makeBIT(int pos, int val) {
		while (pos <= N) {
			tree[pos] += val;
			pos += (pos & -pos);
		}
	}
```

Time for sum and update: O(lgn)

>  Segment  Tree 入门题，参考[discuss](https://discuss.leetcode.com/topic/29918/17-ms-java-solution-with-segment-tree)

```java
public class NumArray {
    class Node{
        int start, end;
        int sum;
        Node left, right;
        public Node(int s, int e){
            start = s;
            end = e;
            left = null;
            right = null;
            sum = 0;
        }
    } // Node
    
    Node node;
    int num[];
    public NumArray(int[] nums){
        num = new int[nums.length];
        for(int i = 0; i < nums.length; i++){
            num[i] = nums[i];
        }
        node = buildTree(num, 0, num.length-1);
    } // NumArray
    
    public void update(int i, int val){
        int dif = num[i] - val;
        num[i] = val;
        nodeupdate(node, i, dif);
    } // update
    
    public int sumRange(int i, int j){
        return node == null?0:sum(node, i, j);
    } // sumRange
    
    public Node buildTree(int[] num, int start, int end){
        if(start > end) return null;
        Node root = new Node(start, end);
        if(start == end){
            root.sum = num[start];
        }
        else{
            int mid = start + (end - start)/2;
            root.left = buildTree(num, start, mid);
            root.right = buildTree(num, mid+1, end);
            root.sum = root.left.sum + root.right.sum;
        }
        return root;
    } // buildTree
    
    public int sum(Node root, int start, int end){
        if(root == null || start > root.end || end < root.start) return 0;
        if(start <= root.start && end >= root.end)  return root.sum;
        int left = sum(root.left, start, end);
        int right = sum(root.right, start, end);
        return left + right;
    } // sum
    
    public void nodeupdate(Node root, int index, int dif){
        if(root == null || index > root.end || index < root.start)  return;
        if(index >= root.start && index <= root.end)    root.sum -= dif;
        nodeupdate(root.left, index, dif);
        nodeupdate(root.right, index, dif);
    } // nodeupdate
}


// Your NumArray object will be instantiated and called as such:
// NumArray numArray = new NumArray(nums);
// numArray.sumRange(0, 1);
// numArray.update(1, 10);
// numArray.sumRange(1, 2);
```

基本操作 ：O(lgn)

### LC308. Range Sum Query 2D - Mutable

Given a 2D matrix *matrix*, find the sum of the elements inside the rectangle defined by its upper left corner (*row*1, *col*1) and lower right corner (*row*2, *col*2).

**Example:**

```
Given matrix = [
  [3, 0, 1, 4, 2],
  [5, 6, 3, 2, 1],
  [1, 2, 0, 1, 5],
  [4, 1, 0, 1, 7],
  [1, 0, 3, 0, 5]
]

sumRegion(2, 1, 4, 3) -> 8
update(3, 2, 2)
sumRegion(2, 1, 4, 3) -> 10
```

1. 不用BIT 256ms：对于每个位置计算左下到当前点的和，然后进行减法计算。~~我真的naive, 视tag若无物~~

```java
public class NumMatrix {
    int[][] board;
    int r;
    int c;
    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0 || matrix[0].length == 0) return;
        r = matrix.length;
        c = matrix[0].length;
        board = new int[r][c];
        board[0][0] = matrix[0][0];
        for(int i = 1; i < r; i++){
            board[i][0] = board[i-1][0] + matrix[i][0];
        }
        for(int j = 1; j < c; j++){
            board[0][j] = board[0][j-1] + matrix[0][j];
        }
        for(int i = 1; i < r; i++){
            for(int j = 1; j < c; j++){
                board[i][j] = -board[i-1][j-1] + matrix[i][j] + board[i-1][j] + board[i][j-1];
            }
        }
    }

    public void update(int row, int col, int val) {
        if(r == 0 || c == 0){
            return;
        }
        int ori = 0;
        if(row == 0 && col == 0)    ori = board[0][0];
        else if(row == 0 && col != 0)   ori = board[row][col] - board[row][col-1];
        else if(row != 0 && col == 0)   ori = board[row][col] - board[row-1][col];
        else    ori = board[row][col] + board[row-1][col-1] - board[row-1][col] - board[row][col-1];
        int dif = val - ori;
        for(int i = row; i < r; i += 1){
            for(int j = col; j < c; j += 1){
                board[i][j] += dif;
            }
        }
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {
        if(c == 0 || r == 0)    return 0;
        int res = 0;
        if(row1 == 0 && col1 == 0)  res = board[row2][col2];
        else if(row1 == 0 && col1 != 0) res = board[row2][col2] - board[row2][col1-1];
        else if(row1 != 0 && col1 == 0) res = board[row2][col2] - board[row1-1][col2];
        else    res = board[row2][col2] + board[row1-1][col1-1] - board[row1-1][col2] - board[row2][col1-1];
        return res;
    }
}


// Your NumMatrix object will be instantiated and called as such:
// NumMatrix numMatrix = new NumMatrix(matrix);
// numMatrix.sumRegion(0, 1, 2, 3);
// numMatrix.update(1, 1, 10);
// numMatrix.sumRegion(1, 2, 3, 4);
```

O(n), O(n)

2. 2D BIT,  正如许多[discuss](https://discuss.leetcode.com/topic/30343/java-2d-binary-indexed-tree-solution-clean-and-short-17ms/16)说的那样，非常理想的做法，20s。道理和上面差不多，也是sum之差的计算。

```java
public class NumMatrix {
    int[][] board;
    int[][] num;
    int r;
    int c;
    public NumMatrix(int[][] matrix) {
        if (matrix.length == 0 || matrix[0].length == 0) return;
        r = matrix.length;
        c = matrix[0].length;
        board = new int[r+1][c+1];
        num = new int[r][c];
        for(int i = 0; i < r; i++){
            for(int j = 0; j < c; j++){
                update(i, j, matrix[i][j]);
            }
        }
        
    }

    public void update(int row, int col, int val) {
        if(r == 0 || c == 0){
            return;
        }
        int dif = val - num[row][col];
        num[row][col] = val;
        for(int i = row+1; i <= r; i += i&(-i)){
            for(int j = col+1; j <= c; j += j&(-j)){
                board[i][j] += dif;
            }
        }
    }

    public int sumRegion(int row1, int col1, int row2, int col2) {
        if(c == 0 || r == 0)    return 0;
        return sum(row2+1, col2+1)+sum(row1, col1)-sum(row1, col2+1)-sum(row2+1, col1);
    }
    
      public int sum(int row, int col) {
        int sum = 0;
        for (int i = row; i > 0; i -= i & (-i)) {
            for (int j = col; j > 0; j -= j & (-j)) {
                sum += board[i][j];
            }
        }
        return sum;
    }
}


// Your NumMatrix object will be instantiated and called as such:
// NumMatrix numMatrix = new NumMatrix(matrix);
// numMatrix.sumRegion(0, 1, 2, 3);
// numMatrix.update(1, 1, 10);
// numMatrix.sumRegion(1, 2, 3, 4);
```

O(lgn)的更新与求和

### LC218 Skyline Problem

~~题目太长不走心复制。~~ Now suppose you are **given the locations and height of all the buildings** as shown on a cityscape photo (Figure A), write a program to **output the skyline**formed by these buildings collectively (Figure B).

大概就是在横轴上有互相重叠的矩形，求能够画出轮廓的所有左上右下点的集合，顺序输出。

> 这个model太好了吧 看这里👉[Skyline](https://briangordon.github.io/2014/08/the-skyline-problem.html)

解法是用一个list把每个矩形的左边和右边存起来，左边存成负数，右边存成正数，然后根据横坐标进行升序排序，横坐标相同就按照高度升序排序（同一坐标左在右前面）。然后构造降序PriorityQueue（存高度），遇到左边就存入队列，遇到右边就移除这个高度，如果队首进行了高度的更新就存入res中一个元素。~~我也不知道我在说什么呢~~

> 1. When we encounter the left edge of a rectangle, we add that rectangle to the heap with its height as the key.
> 2. When we encounter the right edge of a rectangle, we remove that rectangle from the heap.
>    **Any time we encounter a critical point, after updating the heap, we set the height of that critical point to the value peeked from the top of the heap**.

```java
public class Solution {
    public List<int[]> getSkyline(int[][] buildings) {
        List<int[]> height = new ArrayList<int[]>();
        List<int[]> res = new ArrayList<int[]>();
        for(int i = 0; i < buildings.length; i++){
            height.add(new int[]{buildings[i][0], -buildings[i][2]});
            height.add(new int[]{buildings[i][1], buildings[i][2]});
        }
        Collections.sort(height, new Comparator<int[]>(){
            public int compare(int[] arr1, int[] arr2){
                if(arr1[0] != arr2[0])  return arr1[0] - arr2[0];
                else return arr1[1] - arr2[1];
            }
        });
        PriorityQueue<Integer> pq = new PriorityQueue<Integer>((a, b) -> (b-a));
        pq.offer(0);
        int pre = 0;
        for(int[] h : height){
            if(h[1] < 0)    pq.offer(-h[1]);
            else    pq.remove(h[1]);
            int cur = pq.peek();
            if(pre != cur){
                res.add(new int[]{h[0], cur});
                pre = cur;
            }
        }
        return res;
    }
}
```

O(nlgn)

PS. 这个问题油管上有讲解视频。