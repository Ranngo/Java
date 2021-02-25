# 一、排序算法

### 1.1 冒泡排序

原理：比较相邻的两个元素，如果前者大于后者，则交换位置。直到所有元素都完成比较，即为一次冒泡完成，此时列表的最后一个元素为整个序列的最大值。

时间复杂度：O(n<sup>2</sup>)

如图所示：

<img src="C:\Users\li\AppData\Roaming\Typora\typora-user-images\image-20201126135517707.png" alt="image-20201126135517707" style="zoom:80%;" />

代码实现如下：

```java
public class MaoPaoSort {
    public void sort(int[] n) {
        // 迭代执行n-1次排序
        for (int i = 1; i < n.length; i++) {
            // 每两个数进行一次对比
            for (int j = 0; j < (n.length - 1); j++) {
                // 如果前者大于后者，则交换位置
                if (n[j] > n[j + 1]) {
                    int tmp;
                    tmp = n[j];
                    n[j] = n[j + 1];
                    n[j + 1] = tmp;
                }
            }
        }
    }

    public static void main(String[] args) {
        MaoPaoSort maoPaoSort = new MaoPaoSort();
        int[] n = {4, 5, 6, 58, 3, 2, 1, 10};
        System.out.println(Arrays.toString(n));
        System.out.println("\t");
        maoPaoSort.sort(n);
        System.out.println(Arrays.toString(n));
    }
}
```





### 1.2 快速排序

原理：利用递归和分治的思想，将一组元素递归划分。每次将元素划分为有序的两组元素，再对这两组元素分别进行递归划分排序。

时间复杂度：O(nlogn)

代码实现如下：

```java
public class QuickSort {
    public void quickSort(int[] arr, int left, int right){
        if (left<right){
            int mid = partition(arr, left, right);
            quickSort(arr, left, mid-1);
            quickSort(arr, mid+1, right);
        }
    }

    public int partition(int[] arr, int left, int right){
        // i，j分别为左右两个指针
        int i = left;
        int j = right;
        // 通常将左边第一个元素作为中间值（将中间值看作一个空白坑）
        int midValue = arr[left];
        while (i<j){
            // 从右向左，如果s[j]小于中间值，交换s[j]和s[i]，也就是用s[j]来填入s[i]这个坑
            while(i < j && arr[j] >= midValue)
                j--;
            // 此时arr[j] < midValue
            if(i < j)
            {
                arr[i] = arr[j]; //将s[j]填到s[i]中，s[j]就形成了一个新的坑
                i++;
            }

            // 从左向右，如果s[i]大于或等于中间值，交换s[j]和s[i]，也就是用s[i]来填入s[j]这个坑
            while(i < j && arr[i] < midValue)
                i++;
            // 此时arr[i] >= midValue
            if(i < j)
            {
                arr[j] = arr[i]; //将s[i]填到s[j]中，s[i]就形成了一个新的坑
                j--;
            }
        }
        //退出时，i>=j。将中间值填到这个坑中。
        arr[i] = midValue;
        return i;
    }

    public static void main(String[] args) {
        int[] arr = {6,1,2,7,9,3,4,5,8};
        System.out.println(Arrays.toString(arr));
        QuickSort sort = new QuickSort();
        sort.quickSort(arr, 0, arr.length-1);
        System.out.println(Arrays.toString(arr));
    }
}

```

参考博客：https://blog.csdn.net/MoreWindows/article/details/6684558





### 1.3 选择排序

原理：1、每次排序将首位元素作为最小值，将剩余元素依次与首位元素对比。

​			2、若剩余元素小于首位元素，则交换两者。

​			3、直到最后一个元素完成对比，即为一次选择排序完成，此时首位元素真正成为所有元素的最小值。

时间复杂度：O(n<sup>2</sup>)

如图所示：

![image-20201126143656765](C:\Users\li\AppData\Roaming\Typora\typora-user-images\image-20201126143656765.png)

代码实现如下：

```java
public class SelectionSort {
    public void sort(int[] n) {
        // 迭代排序，每次排序完成指针向后移动一位
        for(int i =0; i<n.length; i++){
            //初始化序列第一个数值的索引
           int index = i;
           // 与后续数值进行一一对比
           for(int j = i+1; j<n.length; j++){
               // 如果首位数值不是最小值，交换数值
                if(n[index]>n[j]){
                    int tmp = n[j];
                    n[j] = n[index];
                    n[index] = tmp;
                }
           }
        }
    }

    public static void main(String[] args) {
        SelectionSort selectionSort = new SelectionSort();
        int[] n = {4,6,8,7,9,2,10,1};
        System.out.println(Arrays.toString(n));
        selectionSort.sort(n);
        System.out.println(Arrays.toString(n));
    }
}
```





### 1.4 插入排序

原理：1、将所有元素分为两组，左边为已排序元素，右边为未排序元素。

​			2、从未排序序列中依次取值作为待排序元素，按照倒序，与已排序元素比较大小。

​			3、若小于已排序元素，交换两者位置；反之结束比较。

​			4、从待排序序列中取出下个一元素进行比较和排序。

时间复杂度：O(n<sup>2</sup>)

如图所示：

![image-20201126145146409](C:\Users\li\AppData\Roaming\Typora\typora-user-images\image-20201126145146409.png)



代码实现如下：

```java
public class InsertionSort {
    public void sort(int[] n){
        // 进行n-1次迭代排序
        for(int i=1; i<n.length; i++){
            // 每次指针指向的值作为待插入排序的对象
            int tmp = n[i];
            // 向左侧已排序的集合中进行插入排序，倒序逐个对比数值大小，如果集合中存在大于tmp的数值，则交换位置
            for(int j=i; j>0 && n[j-1]>tmp; j--){
                // j在减小，由于n[j-1]比tmp大，则交换tmp所在的n[j]和n[j-1]的位置
                n[j]=n[j-1];
                n[j-1]=tmp;
                //此时对比对象tmp变为n[j-1]
            }
        }
    }

    public static void main(String[] args) {
        int[] n = {4,3,2,10,12,1,5,6};
        System.out.println(Arrays.toString(n));
        InsertionSort insertionSort = new InsertionSort();
        insertionSort.sort(n);
        System.out.println(Arrays.toString(n));
    }
}
```





### 1.5 归并排序

原理: 同样利用递归和分治的思想，将数据递归划分为两组，直到无法划分为止。划分结束时进行归并，最终得到排序后的有序序列。

时间复杂度：O(nlogn)

如图所示：

![image-20201126155524801](C:\Users\li\AppData\Roaming\Typora\typora-user-images\image-20201126155524801.png)



代码实现如下：

```java
public class MergeSort {
    public void sort(int[] n, int left, int right, int[] tmp){
        if (left<right){
            int mid = (left+right)/2;
            // 递归调用多参数sort方法
            sort(n,left,mid,tmp);
            sort(n,mid+1,right,tmp);
            // 划分结束进行两两归并，保证每两个序列进行一次归并
            merge(n,left,mid,right,tmp);
        }
    }

    // 将两个序列中的元素进行归并
    public void merge(int[] n,int left, int mid, int right, int[] tmp){
        //左指针i
        int i = left;
        //右指针j
        int j = mid+1;
        //tmp数组指针
        int t = 0;

        // i和j其中一个指针到达终点，即某个序列中元素已经全部取出，则结束循环
        while (i<=mid && j<=right) {
            // 比较左右两个分组
            if (n[i] <= n[j]) {
                // 如果左指针的元素小于等于右指针指向的元素，将左指针指向的元素存入tmp数组中，t和i指针均向右移动一位
                tmp[t++] = n[i++];
            } else {
                // 如果左指针的元素大于右指针指向的元素，将右指针指向的元素存入tmp数组中，t和j指针均向右移动一位
                tmp[t++] = n[j++];
            }
        }

        // 如果j>right(越界),而i未到达终点，则将左边序列的剩余元素全部取出复制到tmp数组中
        while (i<=mid){
            tmp[t++]=n[i++];
        }
        // 如果i>mid(越界)，而j未到达终点，则将右边序列的剩余元素全部取出复制到tmp数组中
        while (j<=right){
            tmp[t++] = n[j++];
        }

        // 将tmp数组中的所有元素赋值给数组n
        t=0;
        while (left <= right){
            n[left++] = tmp[t++];
        }
    }


    public void sort(int[] n){
        //创建长度相等的空数组tmp
        int[] tmp = new int[n.length];
        //调用多参数的sort方法
        sort(n,0,n.length-1,tmp);
    }
    public static void main(String[] args) {
        int[] n ={8,4,5,7,1,3,6,2};
        System.out.println(Arrays.toString(n));
        MergeSort mergeSort = new MergeSort();
        mergeSort.sort(n);
        System.out.println(Arrays.toString(n));
    }
}
```





### 1.6 希尔排序

原理：1、将所有元素按增量进行两两分组，然后按照插入排序方法执行一次排序。

​			2、增量减半，继续步骤1。

​			3、重复上述步骤直到增量为1时，即为常见的插入排序。

时间复杂度：O(nlogn)

如图所示：

![img](https://img-blog.csdn.net/20160518211420194?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

代码实现如下：

```java
public class ShellSort {
    public void sort(int[] n, int len){
        while (len>=1) {
            // 根据增量len进行两两分组，每次迭代确定第二个元素的值
            for (int i = len; i < n.length; i++) {
                // 根据第二个元素的值i，得到第一个元素的值i-len，满足条件则交换位置。
                // 将第一个元素的值作为新的第二个元素，往左侧继续迭代排序，满足条件则交换位置
                for (int j = i - len; j >= 0 && n[j + len] < n[j]; j -= len) {
                    int tmp = n[j + len];
                    n[j + len] = n[j];
                    n[j] = tmp;
                }
            }
            //缩小增量
            len = len/2;
            System.out.println(Arrays.toString(n));
        }
    }

    public static void main(String[] args) {
        ShellSort shellSort = new ShellSort();
        int[] n = {9,1,2,5,7,4,8,6,3,5};
        shellSort.sort(n,5);
        System.out.println(Arrays.toString(n));
    }

}
```





# 二、查找算法

### 2.1 二分查找

原理：利用递归思想，将序列划分为两部分进行查找。

代码实现如下：

```java
public class BinSearch {
    public int binSearch(int[] arr,int left,int right,int key) {
        if (left <= right) {
            int mid = (left + right) / 2;
            if (key == arr[mid]) {
                return mid;
            } else {
                // 递归调用二分查找方法
                if (key < arr[mid]) {
                    return binSearch(arr, left, mid - 1, key);
                } else if (key > arr[mid]){
                        return binSearch(arr, mid + 1, right, key);
                    }
                }
            }
        // 不存在则返回-1
        return -1;
    }
    
    public static void main(String[] args) {
        int[] arr = {1,2,3,4,5};
        int key = 2;
        int res = new BinSearch().binSearch(arr,0,arr.length,key);
        System.out.println(res);
    }
}
```





# 三、线性表

## 3.1 顺序表

定义：顺序表是以数组的形式保存的线性表，它使用地址连续的内存空间，依次存储各个元素，逻辑相邻的数据元素其物理存储位置也相邻。







## 3.2 链表

### 3.2.1 单向链表

特点：内存空间不连续，无顺序。头结点不存储数据，索引从0开始，0位置即为第一个数据结点。

代码实现如下：

```java
public class OneWayList<T> implements Iterable<T> {
    // 定义头结点
    private Node head;
    // 定义链表的长度
    private int N;

    private class Node {
        // 定义存储数据
        T item;
        // 定义下一个结点
        Node next;

        // 结点类
        public Node(T item, Node next) {
            this.item = item;
            this.next = next;
        }
    }

    public OneWayList() {
        // 初始化头结点
        this.head = new Node(null, null);
        // 初始化长度
        this.N = 0;
    }

    // 包括六种常用方法
    public void clear() {
        head.next = null;
        this.N = 0;
    }

    public boolean isEmpty() {
//        if (head.next != null){
//            return false;
//        }else {
//            return true;
//        }
        return N == 0;
    }

    public int length() {
        return this.N;
    }

    public T get(int i) {
        // 记录头结点
        Node n = head;
        // 寻找第i个结点
        for (int index = 0; index <= i; index++) {
            n = n.next;
        }
        return n.item;
    }

    public void insert(T t) {
        // 记录头结点
        Node n = head;
        // 寻找尾结点
        while (n.next != null) {
            n = n.next;
        }
        // 创建新结点
        Node newNode = new Node(t, null);
        // 尾结点指向新结点
        n.next = newNode;
        // 长度+1
        N++;
    }

    public void insert(int i, T t) {
        // 记录头结点
        Node pre = head;
        // 寻找i位置结点的pre结点
        for (int index = 0; index <= i - 1; index++) {
            pre = pre.next;
        }
        // 记录第i个结点curr
        Node curr = pre.next;
        // 创建新结点
        Node newNode = new Node(t, null);
        // 新结点指向curr结点
        newNode.next = curr;
        // pre结点指向新结点
        pre.next = newNode ;
        // 长度+1
        N++;

    }

    public T remove(int i) {
        // 记录头结点
        Node pre = head;
        // 寻找第i个结点的头结点pre
        for (int index = 0; index < i - 1; index++) {
            pre = pre.next;
        }
        // 记录第i个结点curr
        Node curr = pre.next;
        // pre指向curr的下一个结点
        pre.next = curr.next;
        // 长度-1
        N--;
        return curr.item;
    }

    public int indexOf(T t) {
        // 记录头结点
        Node n = head;
        // 遍历所有结点
        for (int index = 0; n.next != null; index++) {
            n = n.next;
            if (n.item.equals(t)) {
                return index;
            }
        }
        return -1;
    }

    // 实现Iterable接口，实现for each功能
    @Override
    public Iterator<T> iterator() {
        return new ListIterator();
    }

    private class ListIterator implements Iterator {
        private Node n;

        public ListIterator() {
            this.n = head;
        }

        @Override
        public boolean hasNext() {
            return n.next != null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }

    public static void main(String[] args) {
        OneWayList<String> s1 = new OneWayList<>();
        // 测试插入方法
        s1.insert("姚明");
        s1.insert("奥尼尔");
        s1.insert("霍华德");
        s1.insert(1,"周琦");

        for (String s : s1){
            System.out.println(s);
        }

        // 测试get方法
        String getResult = s1.get(1);
        System.out.println("获取索引1处的结果为"+getResult);
        // 测试remove方法
        String removeResult = s1.remove(0);
        System.out.println("删除的元素是"+removeResult);
        // 测试清空方法
        s1.clear();
        System.out.println("清空后链表中元素个数为"+s1.length());
    }
}
```

### 3.2.2 双向链表 

`Linkedlist`集合底层使用双向链表实现，并提供了增删改查等相关方法。

代码实现如下：

```java
public class TwoWayList<T> implements Iterable<T>{
    // 定义头结点
    private Node head;
    // 定义尾结点
    private Node last;
    // 定义链表长度
    private int N;

    // 结点类
    public class Node {
        // 定义数据
        private T item;
        // 定义前结点
        private Node pre;
        // 定义后结点
        private Node next;

        public Node(Node pre, T t, Node next) {
            this.item = t;
            this.pre = pre;
            this.next = next;
        }
    }

    public TwoWayList() {
        this.head = new Node(null, null, null);
        this.last = new Node(null, null, null);
        this.N = 0;
    }

    public void clear() {
        head.pre = null;
        head.item = null;
        head.next = null;
        last = null;
        N = 0;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int length() {
        return this.N;
    }

    public T getFirst() {
        // 判断是否空列表
        if (this.isEmpty()) {
            return null;
        }
        // 非空列表返回头结点数据
        return head.next.item;
    }

    public T getLast() {
        // 判断是否空列表
        if (this.isEmpty()) {
            return null;
        }
        // 非空列表返回尾结点数据
        return last.item;
    }

    public void insert(T t) {
        // 判断是否空列表
        if (this.isEmpty()) {
            // 空列表直接将新结点作为尾结点
            last = new Node(head, t, null);
            // 头结点next指向尾结点
            head.next = last;
        } else {
            // 非空列表将尾结点next指向新结点
            Node oldNode = last;
            Node newNode = new Node(this.last, t, null);
            oldNode.next = newNode;
            // 将新结点作为尾结点
            last = newNode;
        }
        this.N++;
    }

    public void insert(int i, T t) {
        // 标记头结点
        Node n = head;
        // 找到索引i结点的前一个结点(i-1)
        for (int index = 0; index <= i - 1; index++) {
            n = n.next;
        }
        // 记录索引i位置结点
        Node latterNode = n.next;
        // 创建新结点
        Node newNode = new Node(n, t, latterNode);
        // 将i-1位置的结点n的next指向新结点
        n.next = newNode;
        // 将后面结点的pre指向新结点
        latterNode.pre = newNode;
        // 长度+1
        this.N++;
    }

    public T get(int i) {
        // 记录头结点
        Node n = head;
        // 寻找索引i位置的结点
        for (int index = 0; index <= i; index++) {
            n = n.next;
        }
        // 返回该结点的数据
        return n.item;
    }

    public T remove(int i) {
        // 记录头结点
        Node n = head;
        // 寻找索引i结点的前一结点
        for (int index = 0; index <= i - 1; index++) {
            n = n.next;
        }
        // 记录前一结点
        Node preNode = n;
        // 记录i位置的结点
        Node curNode = n.next;
        // 记录后一结点（i+1）
        Node latterNode = curNode.next;
        // 将前结点的next指向后结点
        preNode.next = latterNode;
        // 将后结点的pre指向前结点
        latterNode.pre = preNode;
        // 返回被删除结点的数据
        return curNode.item;

    }

    public int indexOf(T t) {
        Node n = head;
        for (int index = 0; n.next!=null; index++){
            if (n.next.item.equals(t)){
                return index;
            }
            n = n.next;
        }
        return -1;
    }

    // 实现Iterable接口，实现for each功能
    @Override
    public Iterator<T> iterator() {
        return new TWListIterator();
    }

    private class TWListIterator implements Iterator{
        private Node n;

        public TWListIterator(){
            this.n = head;
        }
        @Override
        public boolean hasNext() {
            return n.next!=null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }

    public static void main(String[] args) {
        TwoWayList<String> s1 = new TwoWayList<>();
        // 测试插入方法
        s1.insert("姚明");
        s1.insert("奥尼尔");
        s1.insert("霍华德");
        s1.insert(1,"周琦");

        for (String s : s1){
            System.out.println(s);
        }
        // 测试get方法
        String getResult = s1.get(1);
        System.out.println("获取索引1处的结果为"+getResult);
        System.out.println("------------------------------------------");
        System.out.println("第一个元素为："+s1.getFirst());
        System.out.println("第二个元素为："+s1.getLast());
        // 测试remove方法
        String removeResult = s1.remove(0);
        System.out.println("删除的元素是"+removeResult);

        // 测试清空方法
        s1.clear();
        System.out.println("清空后链表中元素个数为"+s1.length());

    }
}
```

### 3.2.3 反转单向链表

#### （1）递归法：

原理：通过递归的方式，从尾结点开始，对每两个结点进行反转，最终实现整个链表的反转。

```java
    public void reverse() {
        // 判断链表是否为空
        if (this.N == 0) {
            return;
        }
        // 非空则进入递归
        reverse(head.next);
    }

    public Node reverse(Node cur){
        // 传入结点，判断结点是否为尾结点
        if (cur.next==null){
            // 是尾结点，则将头结点的next指向该节点
            head.next = cur;
            // 返回该结点
            return cur;
        }
        // 如果不是尾结点，继续递归，直到找到尾结点，最终将返回尾结点。
        // 将尾结点作为头结点
        Node pre = reverse(cur.next);
        // 将该尾结点结点的next指向上一结点cur
        pre.next = cur;
        // 反转成功，cur结点的next指向null
        cur.next = null;
        // 返回cur结点，将再次作为上一次递归的pre结点
        return cur;
    }
```

建立一个单向链表，测试反转效果：

```java
public class OneWayList<T> implements Iterable<T> {
    // 定义头结点
    private Node head;
    // 定义链表的长度
    private int N;

    private class Node {
        // 定义存储数据
        T item;
        // 定义下一个结点
        Node next;

        // 结点类
        public Node(T item, Node next) {
            this.item = item;
            this.next = next;
        }
    }

    public OneWayList() {
        // 初始化头结点
        this.head = new Node(null, null);
        // 初始化长度
        this.N = 0;
    }

    public void insert(T t) {
        // 记录头结点
        Node n = head;
        // 寻找尾结点
        while (n.next != null) {
            n = n.next;
        }
        // 创建新结点
        Node newNode = new Node(t, null);
        // 尾结点指向新结点
        n.next = newNode;
        // 长度+1
        N++;
    }

    public void insert(int i, T t) {
        // 记录头结点
        Node pre = head;
        // 寻找i位置结点的pre结点
        for (int index = 0; index <= i - 1; index++) {
            pre = pre.next;
        }
        // 记录第i个结点curr
        Node curr = pre.next;
        // 创建新结点
        Node newNode = new Node(t, null);
        // 新结点指向curr结点
        newNode.next = curr;
        // pre结点指向新结点
        pre.next = newNode ;
        // 长度+1
        N++;
    }

    // 实现Iterable接口，实现for each功能
    @Override
    public Iterator<T> iterator() {
        return new ListIterator();
    }

    private class ListIterator implements Iterator {
        private Node n;

        public ListIterator() {
            this.n = head;
        }

        @Override
        public boolean hasNext() {
            return n.next != null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }

    public void reverse() {
        // 判断链表是否为空
        if (this.N == 0) {
            return;
        }
        // 非空则进入递归
        reverse(head.next);
    }

    public Node reverse(Node cur){
        // 传入结点，判断结点是否为尾结点
        if (cur.next==null){
            // 是尾结点，则将头结点的next指向该节点
            head.next = cur;
            // 返回该结点
            return cur;
        }
        // 如果不是尾结点，继续递归，直到找到尾结点，最终将返回尾结点。
        // 将尾结点作为头结点
        Node pre = reverse(cur.next);
        // 将该尾结点结点的next指向上一结点cur
        pre.next = cur;
        // 反转成功，cur结点的next指向null
        cur.next = null;
        // 返回cur结点，将再次作为上一次递归的pre结点
        return cur;
    }

    public static void main(String[] args) {
        OneWayList<String> s1 = new OneWayList<>();
        // 测试插入方法
        s1.insert("姚明");
        s1.insert("奥尼尔");
        s1.insert("霍华德");
        s1.insert(1, "周琦");

        for (String s : s1) {
            System.out.println(s);
        }

        System.out.println("——————————————————————————反转链表——————————————————————————");

        s1.reverse();

        for (String s : s1) {
            System.out.println(s);
        }
    }
}
```

#### （2）遍历法：



# 四、栈

使用单向链表实现栈：

```java
public class Stack<T> implements Iterable<T> {
    private Node head;
    private int N;

    class Node{
        private T item;
        private Node next;

        public Node(T item, Node next){
            this.item = item;
            this.next = next;
        }
    }

    public Stack(){
        this.head = new Node(null, null);
        this.N = 0;
    }

    public boolean isEmpty(){
        return N == 0;
    }

    public int size(){
        return N;
    }

    public void push(T t){
        //记录head的下一结点tmp
        Node tmp = head.next;
        //创建新结点
        Node newNode = new Node(t,null);
        //head的next指向新结点
        head.next = newNode;
        //新结点的next指向tmp结点
        newNode.next = tmp;
        N++;
    }

    public T pop(){
        //记录head的下一结点tmp
        Node tmp = head.next;
        //栈非空则执行弹出
        if (tmp!=null) {
            //head的next指向tmp的下一结点
            head.next = tmp.next;
            //tmp的next置空
            tmp.next = null;
            N--;
            return tmp.item;
        }
        return null;
    }

    // 实现Iterable接口，实现for each功能
    @Override
    public Iterator<T> iterator() {
        return new StackIterator();
    }

    public class StackIterator implements Iterator{
        private Node n;

        public StackIterator(){
            this.n = head;
        }

        @Override
        public boolean hasNext() {
            return n.next != null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }

    public static void main(String[] args) {
        Stack<String>  stack= new Stack<>();
        stack.push("长江");
        stack.push("黄河");
        stack.push("珠江");

        System.out.println("栈大小为："+stack.size());
        for (String stackElement:stack
             ) {
            System.out.println(stackElement);
        }
        int length = stack.size();
        for (int i = 0; i < length; i++) {
           String item = stack.pop();
            System.out.println("出栈元素为："+item);
        }
        System.out.println(stack.size());
    }
}
```



# 五、队列

使用单向链表实现队列：

```java
public class Queue<T> implements Iterable<T> {
    private Node head;
    private Node last;
    private int N;

    public class Node {
        private T item;
        private Node next;

        public Node(T item, Node next) {
            this.item = item;
            this.next = next;
        }
    }

    public Queue() {
        this.head = new Node(null, null);
        this.last = null;
        this.N = 0;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void enqueue(T t) {
        //创建新结点
        Node newNode = new Node(t, null);
        //尾结点为空时，即队列为空时
        if (last==null){
            //head的next指向新结点
            head.next = newNode;
            //新结点作为尾结点
            last =  newNode;
        }
        //队列非空，尾结点的next指向新结点
        last.next = newNode;
        //新结点作为尾结点
        last = newNode;
        N++;
    }

    public T dequeue() {
        //判断队列是否为空
        if (isEmpty()){
            return null;
        }
        //非空队列，标记head结点的下一结点为tmp
        Node tmp = head.next;
        //head的next指向tmp的下一结点
        head.next = tmp.next;
        //tmp结点的next置空
        tmp.next = null;
        N--;
        
        //如果队列中元素删除完了，需要重置last为空
        if (isEmpty()){
            last = null;
        }
        return tmp.item;
    }

    @Override
    public Iterator<T> iterator() {
        return new QueueIterator();
    }

    public class QueueIterator implements Iterator {
        private Node n;

        public QueueIterator(){
            this.n = head;
        }

        @Override
        public boolean hasNext() {
            return n.next != null;
        }

        @Override
        public Object next() {
            n = n.next;
            return n.item;
        }
    }

    public static void main(String[] args) {
        Queue<String> queue = new Queue<>();
        queue.enqueue("a");
        queue.enqueue("b");
        queue.enqueue("c");

        System.out.println("队列大小为："+queue.size());
        for (String queueElement:queue
        ) {
            System.out.println(queueElement);
        }
        int length = queue.size();
        for (int i = 0; i < length; i++) {
            String item = queue.dequeue();
            System.out.println("出队元素为："+item);
        }
        System.out.println("队列大小为："+queue.size());
    }
}
```



# 六、二叉树

## 6.1 概念

满二叉树：一棵二叉树的结点要么是叶子结点，要么它有两个子结点，即每一层结点数都是满的。如果一个二叉树的层数为K，且结点总数是(2^k) -1，则它就是满二叉树。

完全二叉树：　若设二叉树的深度为k，除第 k 层外，其它各层 (1～k-1层) 的结点数都达到最大个数，第k 层所有的结点都**连续集中在最左边**，这就是完全二叉树。



# 七、B+树

B+树是基于平衡二叉树的延伸，是一种多路平衡查询树。

## 7.1 特点

* 包括索引结点和叶子结点。
* 索引节点只保存索引，数据保存在叶子节点中
* m阶B+树的节点中最多能容纳m-1个索引或者数据
* 节点中的Key有序排列
* 相邻叶子结点依据Key值按从小到大的顺序进行链接
* 节点中Key的个数最小为`ceil(m/2)-1`，最大为`m-1`

## 7.2 添加和删除数据

参考https://blog.csdn.net/Fmuma/article/details/80287924

## 7.3 Hash索引和B+树的区别

哈希索引适合等值查询，但是无法进行范围查询(大于或者小于) 

哈希索引没办法利用索引完成排序 

哈希索引不支持多列联合索引的最佳左前缀法则 

如果有大量重复键值的情况下，哈希索引的效率会很低，因为存在哈希碰撞问题

Hash索引底层是哈希表，哈希表是一种以key-value存储数据的结构，所以多个数据在存储关系上是完全没有任何顺序关系的，所以，对于区间查询是无法直接通过索引查询的，就需要**全表扫描**。**所以，哈希索引只适用于等值查询的场景。**

而B+ 树是一种多路平衡查询树，叶子节点是天然有序的（左子节点小于父节点、父节点小于右子节点），所以对于范围查询的时候不需要做全表扫描。

## 7.4 B+树和B树的区别

B树的每个节点都存储Key和data，叶子节点的指针为null，不适合范围查询。

B+树只有叶子节点存储data，其他节点均为索引节点，因此索引节点中能够存储更多的key，使得更少次数的I/0即可查询到数据。叶子节点的指针有序指向右边的节点。

## 7.5 索引的优点

1. 索引大大减少了服务器需要扫描的数据量
2. 索引可以帮助服务器避免排序和临时表
3. 索引可以将随机I/O变为顺序I/O

## 7.6 聚集索引和非聚集索引的区别

* 聚集索引：叶子节点中直接存储了要查询的行记录数据

* 非聚集索引：叶子节点中存储的是数据在表中的主键值，还需要一次回表查询。

  **主键索引查询只会查一次，而非主键索引需要回表查询多次**。通过覆盖索引，可以只查询一次。



# 八、红黑树

红黑树为一种自平衡二叉查找树。

## 8.1 红黑树的特点

* 所有的节点非红即黑
* 根节点为黑色
* 叶子节点为黑色
* 红色节点的两个子节点都必须为黑色
* 任一节点到达叶子节点的每一条路径上黑色节点的数量相等

## 8.2 红黑树的应用

HashTree、HashMap、Set等

## 8.3 红黑树和二叉搜索树(BST)、AVL的区别

红黑树牺牲了严格的高度平衡的优越条件，它只要求部分地达到平衡要求，降低了对旋转的要求，从而提高了性能。红黑树能够以O(log2 n)的时间复杂度进行搜索、插入、删除操作。此外，由于它的设计，任何不平衡都会在三次旋转之内解决。当然，还有一些更好的，但实现起来更复杂的数据结构能够做到一步旋转之内达到平衡，但红黑树能够给我们一个比较“便宜”的解决方案。

相比于BST，因为红黑树可以能确保树的最长路径不大于两倍的最短路径的长度，所以可以看出它的查找效果是有最低保证的。在最坏的情况下也可以保证O(logN)的，这是要好于二叉查找树的。因为二叉查找树最坏情况可以让查找达到O(N)。

红黑树的算法时间复杂度和AVL相同，但统计性能比AVL树更高，所以在插入和删除中所做的后期维护操作肯定会比红黑树要耗时好多，但是他们的查找效率都是O(logN)，所以红黑树应用还是高于AVL树的. 实际上插入 AVL 树和红黑树的速度取决于你所插入的数据.如果你的数据分布较好,则比较宜于采用 AVL树(例如随机产生系列数),但是如果你想处理比较杂乱的情况,则红黑树是比较快的



# 九、Hash表

## 9.1 定义

哈希表(HashTable)又叫做散列表，通过键值对的形式访问的数据结构，哈希表中存储的是数据所在的地址。通过Hash函数将关键字（key值）映射为索引，再通过索引访问对应数据。等值查询速度很快。

## 9.2 哈希冲突

不同的关键值可能会产生相同的索引，此时便产生了哈希冲突。当出现哈希冲突时，存储引擎必须遍历链表中所有的行指针，逐行进行比较，得到所有符合条件的行。

解决冲突的方法：

* 开放定址法
* 链地址法



# 十、线程安全的数据结构

* HashTable
* Vector
* ConcurrentHashMap
* CopyOnWriteArraylist
* CopyOnWriteArraySet
* StringBuffer