[TOC]

# 链表

### 1 slist

#### 1 IntList



```java
public class IntList {
    public int first;
    public IntList rest;

    public IntList(int f, IntList r) {
        first = f;
        rest = r;
    }

    /** Return the size of the list using... recursion! */
    public int size() {
        if (rest == null) {
            return 1;
        }
        return 1 + this.rest.size();
    }

    /** Return the size of the list using no recursion! */
    public int iterativeSize() {
        IntList p = this;
        int totalSize = 0;
        while (p != null) {
            totalSize += 1;
            p = p.rest;
        }
        return totalSize;
    }

    /** Returns the ith item of this IntList. */
    public int get(int i) {
        if (i == 0) {
            return first;
        }
        return rest.get(i - 1);
    }

    public static void main(String[] args) {
        IntList L = new IntList(15, null);
        L = new IntList(10, L);
        L = new IntList(5, L);

        System.out.println(L.get(100));
    }
} 
```





#### 2 SList





![img](https://cdn.nlark.com/yuque/0/2019/png/388749/1571236577165-9575f99f-3f49-4b8e-b3af-0699ee1c433d.png)





![image.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1571237111105-5b243bd8-8a74-4a61-90b1-f3958b5137b3.png)

- 解决了什么问题



1. intList -> IntNode
2. Bureaucracy: SLList
3. Access control: public ->> private
4. Nested class: Bring IntNode to SLList
5. Ceching: saving size as an int
6. Generalizing: Adding a senitial node to allow represetation of the empty list





```java
public class SLList {   
    private static class IntNode {
        public int item;
        public IntNode next;

        public IntNode(int i, IntNode n) {
            item = i;
            next = n;
            System.out.println(size);
        }
    } 

    /* The first item (if it exists) is at sentinel.next. */
    private IntNode sentinel;
    private int size;

    private static void lectureQuestion() {
        SLList L = new SLList();
        IntNode n = IntNode(5, null);
    }

    /** Creates an empty SLList. */
    public SLList() {
        sentinel = new IntNode(63, null);
        size = 0;
    }

    public SLList(int x) {
        sentinel = new IntNode(63, null);
        sentinel.next = new IntNode(x, null);
        size = 1;
    }

    /** Adds x to the front of the list. */
    public void addFirst(int x) {
        sentinel.next = new IntNode(x, sentinel.next);
        size = size + 1;
    }

    /** Returns the first item in the list. */
    public int getFirst() {
        return sentinel.next.item;
    }

    /** Adds x to the end of the list. */
    public void addLast(int x) {
        size = size + 1;        

        IntNode p = sentinel;

        /* Advance p to the end of the list. */
        while (p.next != null) {
            p = p.next;
        }

        p.next = new IntNode(x, null);
    }
    
    /** Returns the size of the list. */
    public int size() {
        return size;
    }

    public static void main(String[] args) {
        /* Creates a list of one integer, namely 10 */
        SLList L = new SLList();
        L.addLast(20);
        System.out.println(L.size());
    }
}
```



#### 3 泛型SSList



```java
public class SLList<LochNess> { 
    private class StuffNode {
        public LochNess item;
        public StuffNode next;

        public StuffNode(LochNess i, StuffNode n) {
            item = i;
            next = n;
        }
    } 

    private StuffNode first;
    private int size;

    public SLList(LochNess x) {
        first = new StuffNode(x, null);
        size = 1;
    }

    /** Adds x to the front of the list. */
    public void addFirst(LochNess x) {
        first = new StuffNode(x, first);
        size += 1;
    }

    /** Returns the first item in the list. */
    public LochNess getFirst() {
        return first.item;      
    }

    /** Adds an item to the end of the list. */
    public void addLast(LochNess x) {
        size += 1;

        StuffNode p = first;

        /* Move p until it reaches the end of the list. */
        while (p.next != null) {
            p = p.next;
        }

        p.next = new StuffNode(x, null);
    }

    public int size() {
        return size;
    }
}
```

####  





#### 4 DLList

![image.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1571237122288-a368a5d5-e12e-49e1-8266-5d18e2ea6aa1.png)







![image.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1571237130858-307ac7ba-c81b-420b-8f66-2a41cf7f934a.png)







```java
public class DLList{
    private static class IntNode {
        public int item;
        public IntNode next;
        public IntNode pre;

        public IntNode(int i, IntNode n, IntNode  P) {
            item = i;
            next = n;
            pre = p;
            System.out.println(size);
        }
    } 

    private IntNode sentinel;
    private int size;

    public static void letrueQuestion(){
        SLList L = new SLList();
        IntNode n=  IntNode(5, null,null);
    }


    public SLList(){
        sentinel = new IntNode(63, null, null);
        size = 0;
    }

    // 一个猜想， 还没验证
    public SLList(int x){
        sentinel  = new IntNode(63, null, null);
        sentinel.next = new IntNode(x, sentinel, sentinel);
        sentinel.pre = new IntNode(x, sentinel, sentinel);
        
        size =1;
    }

    public void addFirst(int x){
        IntNode second = sentinel.next;
        sentinel.next = new IntNode(x, sentinel.next, sentinel);
        second.pre = new IntNode(x, sentinel.next, sentinel);

        size= size+1;
    }

    public void addLast(int x){
        IntNode Last  = sentinel.pre;
        sentinel.pre = new IntNode(x, sentinel, sentinel.pre);
        Last.next = new IntNode(x, sentinel, sentinel.pre);

        size= size+1;
    }

    public int getFirst(){
        return sentinel.next.item;
    }

    public int getFirst(){
        return sentinel.pre.item;
    }



    public int size(){
        return size;
    }

    public static void main(String[] args){
        SLList L = new SLList();
        L.addLast(20);
        System.out.println(L.size());
    }

}
```





#### 5 AList



```java
public class AList {
    private int[] items;
    private int size;

    /** Creates an empty list. */
    public AList() {
        items = new int[100];
        size = 0;
    }

    /** Resizes the underlying array to the target capacity. */
    private void resize(int capacity) {
        int[] a = new int[capacity];
        System.arraycopy(items, 0, a, 0, size);
        items = a;
    }

    /** Inserts X into the back of the list. */
    public void addLast(int x) {
        if (size == items.length) {
            resize(size + 100);
        }

        items[size] = x;
        size = size + 1;
    }

    /** Returns the item from the back of the list. */
    public int getLast() {
        return items[size - 1];
    }
    /** Gets the ith item in the list (0 is the front). */
    public int get(int i) {
        return items[i];
    }

    /** Returns the number of items in the list. */
    public int size() {
        return size;
    }

    /** Deletes item from back of the list and
      * returns deleted item. */
    public int removeLast() {
        int x = getLast();
        size = size - 1;
        return x;
    }
} 
```





#### 6 SLList  vs  Alist

- size 为 n 



|            | SLList | AList |
| ---------- | ------ | ----- |
| 插入与删除 | o(1)   | o(n)  |
| 查找       | o(n)   | o(1)  |



 