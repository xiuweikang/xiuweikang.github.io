title: 常见排序的java实现
date: 2014-11-30 15:46:00
tags: 算法
---
常见排序java实现
<!-- more -->
- 插入排序（二分插入排序）
- 希尔排序
- 快速排序（三数中值快排）
- 冒泡排序
- 选择排序
- 堆排序
- 归并排序
- 基数排序
- 计数排序
- 桶排序
- 睡眠排序

```java

public class SleepSort {
    /**
     * 方法名：main
     * 说明：睡眠排序
     */
    public static void main(String[] args) {
        int[] ints = {1,4,7,3,8,9,2,6,5};
        SortThread[] sortThreads = new SortThread[ints.length];
        for (int i = 0; i < sortThreads.length; i++) {
            sortThreads[i] = new SortThread(ints[i]);
        }
        for (int i = 0; i < sortThreads.length; i++) {
            sortThreads[i].start();
        }
    }
}
class SortThread extends Thread{
    int ms = 0;
    public SortThread(int ms){
        this.ms = ms;
    }
    public void run(){
        try {
            sleep(ms*10+10);
        } catch (InterruptedException e) {
            // TODO Auto-generated catch block
            e.printStackTrace();
        }
        System.out.println(ms);
    }
}
```

```java

/**
 * 文件名：SortTest.java
 * 时间：2014年11月5日下午6:05:13
 * 作者：修维康
 */
package chapter7;

import java.util.Arrays;

/**
 * 类名：SortTest 说明：各类排序分析详解
 */
public class SortTest {

	/**
	 * 方法名：insertionSort 说明：插入排序 时间复杂度O(N^2)
	 */
	public static <AnyType extends Comparable<? super AnyType>> void insertionSort(
			AnyType[] a) {
		for (int i = 1; i < a.length; i++) {
			AnyType temp = a[i];
			int j = i - 1;
			while (j >= 0 && temp.compareTo(a[j]) < 0) {
				a[j + 1] = a[j];
				j--;
			}
			a[j + 1] = temp;
		}
	}

	/**
	 * 方法名：BInsertSort 说明：二分插入排序 时间复杂度O(N^2) 因为插入排序的前i - 1个元素是排好序的
	 * 所有将第i个元素插入到前面查到元素的时候 可以用二分查找
	 */
	public static <AnyType extends Comparable<? super AnyType>> void BInsertSort(
			AnyType[] a) {
		for (int i = 1; i < a.length; i++) {
			int low = 0;
			AnyType temp = a[i];
			int high = i - 1;
			int position = 0;
			// 二分找不到时 low 的位置是大于key的最小元素，high是小于key的最大元素
			while (low <= high) {
				int mid = (low + high) / 2;
				if (a[mid].compareTo(temp) < 0)
					low = mid + 1;
				else
					high = mid - 1;
			}
			position = high;
			for (int j = i; j > position && j > 0; j--)
				a[j] = a[j - 1];
			a[position + 1] = temp;
		}
	}

	/**
	 * 方法名：shellSort 说明：希尔排序 时间复杂度(N^2) 利用增量序列 对用增量分组得到的序列进行插入排序。
	 */
	public static <AnyType extends Comparable<? super AnyType>> void shellSort(
			AnyType[] a) {
		for (int gap = a.length / 2; gap > 0; gap /= 2) {
			for (int i = 0; i < gap; i++) {
				for (int j = i + gap; j < a.length; j += gap) {
					AnyType temp = a[j];
					if (temp.compareTo(a[j - gap]) < 0) {
						int k = j - gap;
						while (k >= 0 && a[k].compareTo(temp) > 0) {
							a[k + gap] = a[k];
							k -= gap;
						}
						a[k + gap] = temp;
					}
				}
			}
		}
	}

	/**
	 * 方法名：bubbleSort 说明：冒泡排序 时间复杂度O(N^2)
	 */
	public static <AnyType extends Comparable<? super AnyType>> void bubbleSort(
			AnyType[] a) {
		for (int i = 0; i < a.length; i++)
			for (int j = i + 1; j < a.length; j++) {
				if (a[i].compareTo(a[j]) > 0) {
					AnyType temp = a[i];
					a[i] = a[j];
					a[j] = temp;
				}
			}

	}

	/************ 堆排序 ***********************************/

	public static <AnyType extends Comparable<? super AnyType>> void buildHeap(
			AnyType[] array) {

		for (int i = array.length / 2; i >= 0; i--)
			shifDown(array, i, array.length);
	}

	/**
	 * 方法名：shifUp 说明：上滤，其实只用下滤就可以完成建堆，排序
	 */

	public static <AnyType extends Comparable<? super AnyType>> void shifUp(
			AnyType[] array, int valPos) {
		AnyType temp = array[valPos];
		while (valPos > 0 && array[(valPos - 1) / 2].compareTo(temp) < 0) {
			array[valPos] = array[(valPos - 1) / 2];
			valPos = (valPos - 1) / 2;
		}
		array[valPos] = temp;
	}

	/**
	 * 方法名：shifDown 说明：下滤 注意数组越界
	 */
	public static <AnyType extends Comparable<? super AnyType>> void shifDown(
			AnyType[] array, int valPos, int n) {

		AnyType temp = array[valPos];
		while (valPos * 2 + 1 < n) {
			int child = valPos * 2 + 1;// 左儿子
			if (child != n - 1 && array[child].compareTo(array[child + 1]) < 0)
				child++;
			if (temp.compareTo(array[child]) < 0)
				array[valPos] = array[child];
			else
				break;
			valPos = child;
		}
		array[valPos] = temp;

	}

	public static <AnyType extends Comparable<? super AnyType>> void heapSort(
			AnyType[] a) {
		buildHeap(a);
		for (int i = a.length - 1; i > 0; i--) {
			AnyType temp = a[0];
			a[0] = a[i];
			a[i] = temp;
			shifDown(a, 0, i);
		}
	}

	/**
	 * 方法名：mergeSort 说明：归并排序，JAVA中对泛型的排序用该排序，对基本类型的排序用快排
	 * 因为归并排序是比较次数最少的，java中对两个对象的比较 代价是昂贵的
	 */
	public static <AnyType extends Comparable<? super AnyType>> void mergeSort(
			AnyType[] a) {
		AnyType[] tempArray = (AnyType[]) new Comparable[a.length];
		mergeSort(a, tempArray, 0, a.length - 1);
	}

	private static <AnyType extends Comparable<? super AnyType>> void mergeSort(
			AnyType[] a, AnyType[] tempArray, int low, int high) {
		if (low < high) {
			int mid = (low + high) / 2;
			mergeSort(a, tempArray, low, mid);
			mergeSort(a, tempArray, mid + 1, high);
			merge(a, tempArray, low, mid + 1, high);
		}

	}

	private static <AnyType extends Comparable<? super AnyType>> void merge(
			AnyType[] a, AnyType[] tempArray, int lowPos, int highPos,
			int highEnd) {
		int leftEnd = highPos - 1;
		int temPos = lowPos;
		int numElements = highEnd - lowPos + 1;
		while (lowPos <= leftEnd && highPos <= highEnd) {
			if (a[lowPos].compareTo(a[highPos]) <= 0)
				tempArray[temPos++] = a[lowPos++];
			else
				tempArray[temPos++] = a[highPos++];
		}
		while (lowPos <= leftEnd)
			tempArray[temPos++] = a[lowPos++];
		while (highPos <= highEnd)
			tempArray[temPos++] = a[highPos++];
		for (int q = 0; q < numElements; q++, highEnd--)
			a[highEnd] = tempArray[highEnd];

	}

	/**
	 * 方法名：QuickSort 说明：三数取中值快排 因为选第一个或者选最后一个可能会面对已经排好序的序列，那样会导致快排像冒泡一样
	 * 时间复杂度是O(N^2) 三数取中值减少这种情况
	 */

	public static <AnyType extends Comparable<? super AnyType>> void quickSort(
			AnyType[] a) {
		quickSort(a, 0, a.length - 1);
	}

	private static <AnyType extends Comparable<? super AnyType>> void quickSort(
			AnyType[] a, int low, int high) {
		if (low < high) {
			int keyPos = partition(a, low, high);
			quickSort(a, low, keyPos - 1);
			quickSort(a, keyPos + 1, high);
		}

	}

	/**
	 * 方法名：partition 说明：在原序列上进行划分，因为一个临时变量可以保存一个key，所以在key的原位置上可以插入
	 * 类似在key的位置上挖坑，在找到要移动的元素，挖该元素移到这个坑里，又留下另一个坑， 不断的进行下去 直到low==down。
	 */
	private static <AnyType extends Comparable<? super AnyType>> int partition(
			AnyType[] a, int low, int high) {
		AnyType key = median3(a, low, high);
		while (low < high) {
			while (low < high && a[high].compareTo(key) >= 0)
				--high;
			a[low] = a[high];
			while (low < high && a[low].compareTo(key) <= 0)
				++low;
			a[high] = a[low];
		}
		a[low] = key;
		return low;
	}

	public static <AnyType extends Comparable<? super AnyType>> AnyType median3(
			AnyType[] a, int low, int high) {
		int mid = (low + high) / 2;
		if (a[low].compareTo(a[mid]) > 0) {
			AnyType temp = a[low];
			a[low] = a[mid];
			a[mid] = temp;
		}
		if (a[mid].compareTo(a[high]) > 0) {
			AnyType temp = a[mid];
			a[mid] = a[high];
			a[high] = temp;
		}
		if (a[low].compareTo(a[mid]) > 0) {
			AnyType temp = a[low];
			a[low] = a[mid];
			a[mid] = temp;
		}
		AnyType tem = a[low];
		a[low] = a[mid];
		a[mid] = tem;
		return a[low];
	}

	/**
	 * 方法名：selectSort 说明：选择排序O(N^2)的算法
	 */
	public static <AnyType extends Comparable<? super AnyType>> void selectSort(
			AnyType[] a) {
		for (int i = 0; i < a.length; i++) {
			int j = selectMin(a, i);
			if (i != j) {
				AnyType temp = a[j];
				a[j] = a[i];
				a[i] = temp;
			}
		}

	}

	private static <AnyType extends Comparable<? super AnyType>> int selectMin(
			AnyType[] a, int n) {
		AnyType min = a[n];
		int minPos = n;
		;
		for (int i = n + 1; i < a.length; i++)
			if (a[i].compareTo(min) < 0) {
				min = a[i];
				minPos = i;
			}
		return minPos;

	}

	/**
	 * 方法名：radixSort 说明：基数排序 参数是数组和该数组中的最多位数。 O(N)的算法，将数组中的数按照从低位到高位不断的分配，收集
	 * 基数排序里面需要用到对1位数的稳定排序，在这里我们用到了 计数排序 作为基数排序的子程序，即将其直接映射到0-9的桶里
	 */
	public static void radixSort(Integer[] a, int dataNum) {
		int[][] array = new int[10][a.length + 1];
		for (int i = 0; i <= 9; i++)
			array[i][0] = 0;
		int bit = dataNum;
		while (dataNum-- > 0) {
			// 分配
			for (int i = 0; i < a.length; i++) {
				int index = ++array[getNum(a[i], bit - dataNum)][0];
				array[getNum(a[i], bit - dataNum)][index] = a[i];
			}
			// 收集

			for (int i = 0, w = 0; i < 10; i++) {
				for (int j = 1; j <= array[i][0]; j++)
					a[w++] = array[i][j];
				array[i][0] = 0;
			}
		}

	}

	/**
	 * 方法名：getNum 说明：返回数n的倒数第i位数
	 */
	public static int getNum(int n, int i) {
		int count = 0;
		while (n != 0) {
			int x = n % 10;
			if (++count == i)
				return x;
			n /= 10;
		}
		return 0;

	}

	/**
	 * 方法名：countSort 说明：计数排序 O(N)的算法 空间换时间
	 */
	public static int[] countSort(int[] a) {

		int[] c = new int[findMax(a) + 1];
		int[] b = new int[a.length];
		for (int i = 0; i < c.length; i++)
			c[i] = 0;
		for (int i = 0; i < a.length; i++)
			c[a[i]]++;
		int cNum = c.length - 1;
		for (int i = a.length - 1; i >= 0; i--) {
			while (cNum >= 0 && c[(cNum)] == 0)
				cNum--;
			;
			b[i] = cNum;
			c[cNum]--;
		}
		return b;
	}

	public static int findMax(int[] a) {
		int max = a[0];
		for (int i = 1; i < a.length; i++) {
			if (a[i] > max)
				max = a[i];
		}
		return max;
	}

	/**
	 * 方法名：bucketSort 说明：桶排序 排0-999 数量不超过1000 桶排序的思想是
	 * 将数组里的数按照某种映射均匀的分布在n个桶里，每个桶里的数都在一定的范围内 在对每个桶进行排序 最后按照桶的范围在合并在一起
	 * 在这里直接按照0-99 100-199划分桶，最后直接合并就行
	 */
	public static void bucketSort(Integer[] a){
		Integer[][] bucket = new Integer[10][101];//定义
		for(int i = 0;i < 10;i++)
			bucket[i][0] = 0;
		for(int i = 0; i < a.length;i++){
			for(int j = 0;j< 10;j++){
				if(a[i]>=100*j&&a[i] <= 100*j+99){
					int index = ++bucket[j][0];
					bucket[j][index] = a[i]; 
				}
			}		
		}
		//利用快排对每个桶进行排序
		for(int i = 0;i < 10;i++)
			quickSort(bucket[i],1,bucket[i][0]);
		//将10个桶合并
		int w = 0;
		for(int i = 0;i < 10;i++){
			for(int j = 1;j <= bucket[i][0];j++)
				a[w++] = bucket[i][j];
		}
	}

	/**
	 * 方法名：main 说明：测试
	 */
	public static void main(String[] args) {
		// TODO Auto-generated method stub
		Integer[] a = new Integer[] { 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		Integer[] b = new Integer[] { 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		Integer[] c = new Integer[] { 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		Integer[] d = new Integer[] { 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		Integer[] e = new Integer[] { 991,122,333,1,44,588,656 };
		Integer[] f = new Integer[] { 8, 7, 6, 8, 9, 10, 12, 5, 4 };
		Integer[] g = new Integer[] { 10, 9, 8, 7, 6, 5, 4, 3, 2, 1 };
		Integer[] h = new Integer[] { 70, 76, 60, 65, 50, 55, 44, 42, 45, 20,
				103, 103, 10004 };
		int[] i = new int[] { 10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 1003, 1003 };
		Integer[] k = new Integer[]{991,122,333,1,44,588,656};
		insertionSort(a);
		BInsertSort(b);
		shellSort(c);
		selectSort(d);
		heapSort(e);
		quickSort(f);
		mergeSort(g);
		radixSort(h, 5);
		i = countSort(i);
		bucketSort(k);
		System.out.println(Arrays.toString(a));
		System.out.println(Arrays.toString(b));
		System.out.println(Arrays.toString(c));
		System.out.println(Arrays.toString(d));
		System.out.println(Arrays.toString(e));
		System.out.println(Arrays.toString(g));
		System.out.println(Arrays.toString(f));
		System.out.println(Arrays.toString(h));
		System.out.println(Arrays.toString(i));
		System.out.println(Arrays.toString(k));
	}

}
```

闲的没事，测了下各个排序的速度。
随机填充了10W的Integer数组
对其进行各种排序
花费时间如下

- 插入排序：21148
- 二分插入排序：11808
- 希尔排序：76
- 选择排序：18919
- 堆排序：77
- 快速排序：257
- 归并排序：187
- 计数排序：90
- 桶排序：15
可以看到
因为排的是Integer 所以快排并没有归并排的快