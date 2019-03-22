# 排序算法

#### 一、选择排序

从数组头部开始，通过比较找出数组元素中的最大值（最小值），交换至数组头部，不断通过交换从剩余未排序元素中选择最大的加入已排序部分。

```java
class Solution{
    public void SelectionSort(int [] array){
        int length = array.length;
        int k=0,tmp=0;
        for(int i=0;i<length;i++){
            k=i;
            for(int j=i+1;j<length;j++){
                if(array[j]>array[k]){
                    k=j;
                }
            }
            tmp = array[i];
            array[i] = array[k];
            array[k] = tmp;
        }
    }
}
```

#### 二、冒泡排序

循环n-1次，每次将相邻的元素进行比较，可以将最大值（最小值）像泡泡一样排到队尾。

```java
class Solution{
    public void BubbleSort(int [] array){
        int length = array.length;
        int k=0,tmp=0;
        for(int i=1;i<length;i++){
            for(int j=0;j+1<length;j++){
                if(array[j]>array[j+1]){
                    tmp = array [j];
                    array[j] = array[j+1];
                    array[j] = tmp;
                }
            }
        }
    }
}
```

上面算法只是简单描述冒泡思想，冒泡排序有很多的优化方法，比如设置一个交换计数器，如果某一趟没有发生交换，则可以直接结束算法，以算法数据正序为例，此时第一趟排序不会发生交换，所以只需要执行一轮即可直接退出。还有就是第s趟排序之后末尾的s个元素为有序，只需要对前n-s个元素进行比较，可以减少比较次数。

#### 三、插入排序

插入排序是维持前s个元素有序，再将未排序的元素插入有序的队列中，保持s+1个元素的有序性。

```java
class Solution{
    public void InsertionSort(int [] array){
        int length = array.length;
        int k=0,tmp=0;
        for(int i=1;i<length;i++){
            tmp = array[i];
            for(int j = i-1;j>=0;j--){
                if(array[j]<array[i]){
                    break;//找到插入位置  
                }
                //没找到的话j=-1退出循环
            }
            if(j!=i-1){
                for(k=i-1;k>j;k--){
                    array[k+1] = array[k];
                }
                array[k+1] =tmp;    
            }            
        }
    }
}
```

#### 四、计数排序

计数排序的时间复杂度可以达到O(n+k)，计数排序之所以有相对低的时间复杂度是因为计数排序对排序数组的要求比较严格，首先排序元素必须为整数，因为计数排序是通过统计数组的下标确定元素值的，而下表必须为正整数。其次计数排序的元素必须控制在一定范围[0，k）以内，计数排序与其他排序方式不同，没有元素之间的比较，但是局限性比较大。

对于处于[0，k）内的所有数进行计数，如果数的值为s,则统计数组s位置的值加一，最终将统计数组中不为0的项的下标连续输出对应统计值的次数。

例子：

对于数组     **9，3，5，4，9，1，2，7，8，1，3，6，5，3，4，0，10，9 ，7，9**

统计结果如下：

![img](http://5b0988e595225.cdn.sohucs.com/images/20181011/8dfb5b43e4694281b046952079e7851a.png)

输出结果    **0，1，1，2，3，3，3，4，4，5，5，6，7，7，8，9，9，9，9，10**

```java
class Solution{
    public void InsertionSort(int [] array){
        int length = array.length;
        int k = 100,index=0;
        int [] CountArray = new int[k];
        for(int i=0;i<length;i++){
            CountArray[array[i]]++;
        }
        for(int i=0;i<k;i++){
            for(int j=0;j<CountArray[i];j++){
                array[index++] = i;
            }
        }
    }
}
```

#### 五、Shell排序

**希尔排序**，也称**递减增量排序算法**，是插入排序的一种更高效的改进版本。希尔排序是非稳定排序算法。

希尔排序是基于插入排序的以下两点性质而提出改进方法的：

- 插入排序在对几乎已经排好序的数据操作时，效率高，即可以达到线性排序的效率
- 但插入排序一般来说是低效的，因为插入排序每次只能将数据移动一位

```java
public static void shellSort(int[] array) {
    int number = array.length / 2;
    int i;
    int j;
    int temp;
    while (number >= 1) {
        for (i = number; i < array.length; i++) {
            temp = array[i];
            j = i - number;
            while (j >= 0 && array[j] < temp) { //需要注意的是，这里array[j] < temp将会使数组从大到小排序。
                array[j + number] = array[j];
                j = j - number;
            }
            array[j + number] = temp;
        }
        number = number / 2;
    }
}

```

#### 六、快速排序

**快速排序**的基本思想是，通过一轮的排序将序列分割成独立的两部分，其中一部分序列的关键字（这里主要用值来表示）均比另一部分关键字小。继续对长度较短的序列进行同样的分割，最后到达整体有序。在排序过程中，由于已经分开的两部分的元素不需要进行比较，故减少了比较次数，降低了排序时间。

```java
public void quicksort(int [] s, int a, int b){
	if(a < b){
		int low = a;
		int high = b;
		int key = s[a];//基准元素选最左元素
		while(low < high){
			while(low < high && s[high]>=key){
				high--;
			}
			if(low < high){
				s[low++] = s[high];
			}
			while(low < high && s[low] < key){
				low++;
			}
			if(low < high){
				s[high--] = s[low];
			}
		}
		s[low] = key;
		quicksort(s,a,low-1);
		quicksort(s,low+1,b);
	}
}
```

快排常见两种常见方式：

1、通过交换基准值

2、不交换基准值，通过与基准值比较找到两端分别大于基准值和小于基准值的项，交换两项的值。

**快排的优化：**

分治法在每次序列划分时，如果能分成两个等长的子序列时，那么分治算法效率会达到最大。

快速排序对于有序或局部有序的序列效率低下。

**优化1：通过基准的选取**，减少出现不好的分割的概率。

方法(1)：固定位置

方法(2)：随机选取基准

方法(3)：三数取中（median-of-three）

**优化2：当待排序序列的长度分割到一定大小后，使用插入排序。**

**优化3：优化递归操作**

```

void QSort(int arr[],int low,int high)
{ 
	int pivotPos = -1;
	if (high - low + 1 < 10)
	{
		InsertSort(arr,low,high);  // 小数组使用插入排序
		return;
	}
	while(low < high)
	{
		pivotPos = Partition(arr,low,high); // 选取基准并分割后的基准的位置
		QSort(arr,low,pivotPos-1);
		low = pivotPos + 1;     //将第二次递归改为循环
	}
}
```

#### 七、堆排序

堆排序是利用**堆**这种数据结构而设计的一种排序算法，堆排序是一种**选择排序，**它的最坏，最好，平均时间复杂度均为O(nlogn)，它也是不稳定排序。首先简单了解下堆结构。

**堆是具有以下性质的完全二叉树：每个结点的值都大于或等于其左右孩子结点的值，称为大顶堆；或者每个结点的值都小于或等于其左右孩子结点的值，称为小顶堆。**

a.将无需序列构建成一个堆，根据升序降序需求选择大顶堆或小顶堆;

b.将堆顶元素与末尾元素交换，将最大元素"沉"到数组末端;

c.重新调整结构，使其满足堆定义，然后继续交换堆顶元素与当前末尾元素，反复执行调整+交换步骤，直到整个序列有序。

```java
public class HeapSort {
    public static void main(String []args){
        int []arr = {9,8,7,6,5,4,3,2,1};
        sort(arr);
        System.out.println(Arrays.toString(arr));
    }
    public static void sort(int []arr){
        //1.构建大顶堆
        for(int i=arr.length/2-1;i>=0;i--){
            //从第一个非叶子结点从下至上，从右至左调整结构
            adjustHeap(arr,i,arr.length);
        }
        //2.调整堆结构+交换堆顶元素与末尾元素
        for(int j=arr.length-1;j>0;j--){
            swap(arr,0,j);//将堆顶元素与末尾元素进行交换
            adjustHeap(arr,0,j);//重新对堆进行调整
        }

    }

    /**
     * 调整大顶堆（仅是调整过程，建立在大顶堆已构建的基础上）
     * @param arr
     * @param i
     * @param length
     */
    public static void adjustHeap(int []arr,int i,int length){
        int temp = arr[i];//先取出当前元素i
        for(int k=i*2+1;k<length;k=k*2+1){//从i结点的左子结点开始，也就是2i+1处开始
            if(k+1<length && arr[k]<arr[k+1]){//如果左子结点小于右子结点，k指向右子结点
                k++;
            }
            if(arr[k] >temp){//如果子节点大于父节点，将子节点值赋给父节点（不用进行交换）
                arr[i] = arr[k];
                i = k;
            }else{
                break;
            }
        }
        arr[i] = temp;//将temp值放到最终的位置
    }

    /**
     * 交换元素
     * @param arr
     * @param a
     * @param b
     */
    public static void swap(int []arr,int a ,int b){
        int temp=arr[a];
        arr[a] = arr[b];
        arr[b] = temp;
    }
```

#### 八、归并排序

将两个的有序数列合并成一个有序数列，我们称之为"**归并**"。
归并排序(Merge Sort)就是利用归并思想对数列进行排序。根据具体的实现，归并排序包括"**从上往下**"和"**从下往上**"2种方式。

1. **从下往上的归并排序**：将待排序的数列分成若干个长度为1的子数列，然后将这些数列两两合并；得到若干个长度为2的有序数列，再将这些数列两两合并；得到若干个长度为4的有序数列，再将它们两两合并；直接合并成一个数列为止。这样就得到了我们想要的排序结果。(参考下面的图片)

2. **从上往下的归并排序**：它与"从下往上"在排序上是反方向的。它基本包括3步：
   ① 分解 -- 将当前区间一分为二，即求分裂点 mid = (low + high)/2; 
   ② 求解 -- 递归地对两个子区间a[low...mid] 和 a[mid+1...high]进行归并排序。递归的终结条件是子区间长度为1。
   ③ 合并 -- 将已排序的两个子区间a[low...mid]和 a[mid+1...high]归并为一个有序的区间a[low...high]。

```java
//从上往下归并
class Solution{
    //两个有序子列归并
    public static void Merge(int [] array,int left ,int middle,int right){
        int i=left,j=middle+1,tmp=0;
        int [] tmpArray = new int[right-left+1];
        while(i<=middle && j<=right){
            if(array[i]<=array[j]){
                tmpArray[tmp++] = array[i++];
            }
            else {
                tmpArray[tmp++] = array[j++];
            }
        }
        while(i<=middle){
            tmpArray[tmp++] = array[i++];
        }
        while(j<=right){
            tmpArray[tmp++] = array[j++];
        }
        for(i=left;i<=right;i++){
            array[i] = tmpArray[i-left];
        }
    }
    //递归分组
    public static void MergeSort(int [] array,int left,int right ){
        if(right-left>=1){
            int middle = (left+right)/2;
            MergeSort(array,left,middle);
            MergeSort(array,middle+1,right);
            Merge(array,left,middle,right);
        }
        return;
    }
}
```



#### 九、常见排序算法时空复杂度与稳定性：

![img](https://images2015.cnblogs.com/blog/975503/201702/975503-20170214211234550-1109833343.png)





