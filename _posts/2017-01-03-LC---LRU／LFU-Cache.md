---
layout: post
title: LC - LRU／LFU Cache
subtitle: LC146, LC460
date: 2017-01-03
categories: Leetcode
tags: [Design]
catalog: true
---

#### LC146. LRU Cache

Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: `get` and `set`.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
`set(key, value)` - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.

1个月之前写的，交了26次AC？可是现在还是不记得了QAQ

题目要求实现一个缓冲存储器，其中村的是最近被探访到的capacity个数据。Hard题的奥义大概就在于虽然我大概知道这是个什么，但是总是错吧。我应该是参考了[discuss](https://discuss.leetcode.com/topic/34701/java-easy-version-to-understand/2)做了改动。利用自己定义的类是比较方便的实现方法。另外利用[LinkedHashMap](https://discuss.leetcode.com/topic/43961/laziest-implementation-java-s-linkedhashmap-takes-care-of-everything)也可以解决这个问题，内部API好厉害。

对于每一个Node，定义成具有key, value, preNode, nextNode的形式很适合这类具有要求的设计题。存在map里就可以O(1)查找了。每次访问到一个Node就删除这个Node然后加到头上。注意一下head，end的更新即可。

```java
public class LRUCache {
    class Node{
        int val;
        int key;
        Node pre;
        Node next;
        
        private Node(int k, int v){
            this.val = v;
            this.key = k;
        }
    }
    
    int size;
    HashMap<Integer, Node> map = new HashMap<Integer, Node>();
    Node head =  null;
    Node end = null;
    public LRUCache(int capacity) {
        this.size = capacity;
    }
    
    public int get(int key) {
        if(map.containsKey(key)){
            Node cur = map.get(key);
            remove(cur);
            setHead(cur);
          return cur.val;  
        }
        return -1;
    }
    
    public void set(int key, int value) {
        if(map.containsKey(key)){
            Node cur = map.get(key);
            cur.val = value;
            remove(cur);
            setHead(cur);
        }else if(!map.containsKey(key) && map.size() >= size){
            map.remove(end.key);
            remove(end);
            Node cur = new Node(key, value);
            setHead(cur);
            map.put(key, cur);
        }else if(!map.containsKey(key) && map.size() < size){
            Node cur = new Node(key, value);
            setHead(cur);
            map.put(key, cur);
        }
    }
    
    public void remove(Node n){
        if(n.pre != null){
            n.pre.next = n.next;
        }else{
            head = n.next;
        }
        if(n.next != null){
            n.next.pre = n.pre;
        }else{
            end = n.pre;
        }
    }
    
    public void setHead(Node n){
        if(head != null){
            head.pre = n;
        }
        n.next = head;
        n.pre = null;
        head = n;
        if(end == null) end = n;
    }
}
```

#### LC460. LFU Cache

Design and implement a data structure for [Least Frequently Used (LFU)](https://en.wikipedia.org/wiki/Least_frequently_used) cache. It should support the following operations: `get`and `put`.

`get(key)` - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
`put(key, value)` - Set or insert the value if the key is not already present. When the cache reaches its capacity, it should invalidate the least frequently used item before inserting a new item. For the purpose of this problem, when there is a tie (i.e., two or more keys that have the same frequency), the least **recently** used key would be evicted.

**Follow up:**
Could you do both operations in **O(1)** time complexity?

过了300多题，这次要实现最近的最经常访问的存储器。同样的也是自己实现Node类，包括key, preNode, nextNode. 但是这次的要求是最常访问的capacity数据集，所以Node包括访问次数count，代表访问次数为count的key集合。利用LinkedHashSet，这样count一致时保留最近的那个。

如果get和set都要求O(1), 那代表一定有存储（key, value）的HashTable，也要有存（count, Node) 的HashTable。head代表count最小的Node，所以每次增加新节点和删除旧节点都从head开始。

> [Discuss](https://discuss.leetcode.com/topic/69137/java-o-1-accept-solution-using-hashmap-doublelinkedlist-and-linkedhashset/16)

```java
public class LFUCache {
    // Node save the key with frequency count
    class Node{
        int count = 0;
        // with order
        public LinkedHashSet<Integer> keys;
        public Node pre, next;
        public Node(int count){
            this.count = count;
            keys = new LinkedHashSet<Integer>();
            pre = next = null;
        }
    }
    
    private Node head = null;
    private int cap = 0;
    private HashMap<Integer, Integer> valueHash = null;
    private HashMap<Integer, Node> nodeHash = null;
    
    public LFUCache(int capacity) {
        this.cap = capacity;
        valueHash = new HashMap<>();
        nodeHash = new HashMap<>();
    } // LFUCache
    
    public int get(int key) {
        if(valueHash.containsKey(key)){
            increaseCount(key);
            return valueHash.get(key);
        }
        return -1;
    }
    
    public void set(int key, int value) {
        if(cap == 0)    return;
        if(valueHash.containsKey(key)){
            valueHash.put(key, value);
        }else{
            if(valueHash.size() < cap)  valueHash.put(key, value);
            else {
                removeOld();
                valueHash.put(key, value);
            }
            addToHead(key);
        }
        increaseCount(key);
    } // set
    
    private void addToHead(int key){
        if(head == null){
            head = new Node(0);
            head.keys.add(key);
        }else if(head.count > 0){
            Node node = new Node(0);
            node.keys.add(key);
            node.next = head;
            head.pre = node;
            head = node;
        }else{
            head.keys.add(key);
        }
        nodeHash.put(key, head);
    } // addToHead
    
    private void removeOld(){
        if(head == null)    return;
        int old = 0;
        for(int n: head.keys){
            old = n;
            break;
        }
        head.keys.remove(old);
        if(head.keys.size() == 0)   removeNode(head);
        nodeHash.remove(old);
        valueHash.remove(old);
    } // removeOld
    
    private void removeNode(Node node){
        if(node.pre == null) head = node.next;
        else node.pre.next = node.next;
        if(node.next != null) node.next.pre = node.pre;
    } // removeNode
    
    private void increaseCount(int key){
        Node node = nodeHash.get(key);
        node.keys.remove(key);
        if(node.next == null){
            node.next = new Node(node.count + 1);
            node.next.pre = node;
            node.next.keys.add(key);
        }else if(node.next.count == node.count + 1){
            node.next.keys.add(key);
        }else{
            Node temp = new Node(node.count + 1);
            temp.keys.add(key);
            temp.pre = node;
            temp.next = node.next;
            node.next.pre = temp;
            node.next = temp;
        }
        nodeHash.put(key, node.next);
        if(node.keys.size() == 0) removeNode(node);
    } // increaseCount
    
} // LFU

/**
 * Your LFUCache object will be instantiated and called as such:
 * LFUCache obj = new LFUCache(capacity);
 * int param_1 = obj.get(key);
 * obj.set(key,value);
 */
```

