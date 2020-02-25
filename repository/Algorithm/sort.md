**常见的排序算法总结**

1，冒泡排序

	冒泡排序算法的运作如下：（从后往前）
	比较相邻的元素。如果第一个比第二个大，就交换他们两个。
	对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。
	针对所有的元素重复以上的步骤，除了最后一个。
	持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。
	
	冒泡排序最好的时间复杂度为o(n)。
	
java代码示例(正序)：

	public static void bubbleSort(int[] arr) {
        final int len = arr == null ? 0 : arr.length;
        for (int i = 0; i < len - 1; i++) {
            for (int j = 0; j < len; j++) {
                int temp;
                if(arr[i] < arr[j]) {
                    temp = arr[i];
                    arr[i] = arr[j];
                    arr[j] = temp;
                } else {
                    continue;
                }
            }
        }
    }
    
2,快速排序

	快速排序算法是对集合中元素进行排序最通用的算法，俗称快排，其算法的时间复杂度为O(nlgn)，空间复杂度为O(1)。

我们举例来对其算法思路进行理解，譬如数组 A = { 4, 8, 1, 2, 9, 7, 3, 0, 5, 6 };

	第一步，以最后一个数6为基准，把小于等于6的数挪到数组左边，把大于6的数挪到数组右边。
	
	那么结果为 { 4, 1, 2, 3, 0, 5, 8, 9, 7, 6 }，这个时候再做一步，把8和6进行交换，得到{ 4, 1, 2, 3, 0, 5, 6, 9, 7, 8 }把6的最新位置返回。这个时候其实数组被切割成两部分，准确地说，是三部分{ 4, 1, 2, 3, 0, 5 }, { 6 }和{ 9, 7, 8 }.
	
	第二步，对 { 4, 1, 2, 3, 0, 5 }和 { 9, 7, 8 }重复第一步的过程，我们得到
	
	{ 4, 1, 2, 3, 0 }, { 5 }, { 7 }, { 8 }, { 9 }
	
	第三步，再对{ 4, 1, 2, 3, 0 }进行分割，得到 { 0 }, { 1, 2, 3, 4 }
	
	第四步，再对{ 1, 2, 3, 4 }进行分割，得到{ 1, 2, 3 }和{ 4 }
	
	第五步，对{ 1, 2, 3 }进行分割，得到{ 1, 2 }和{ 3 }
	
	第六步，对{ 1, 2 }进行分割，得到{ 1 }和{ 2 }
	
java代码示例(正序)：

	public static void main(String[] args) {
		// write your code here
		int[] arr = {4, 2, 0, 5, 8, 9, 3, 5, 4};
		final int len = arr.length;
		quickSort(arr, 0, len - 1);
		for (int i = 0; i < len - 1; i++) {
		System.out.println(arr[i]);
		}
	}

	// Sort a list of numbers in an array within [start, end]
    public static void quickSort(int[] arr, int low, int high) {
        if(low < high) {
            int povit = partition(arr, low, high);
            quickSort(arr, low, povit - 1);
            quickSort(arr, povit + 1, high);
        }
    }

    // Select A[end] as the pivot, separate the array into two parts.
    private static int partition(int[] A, int start, int end) {
        int pivot = A[end];
        int i = start;
        for (int j = start; j < end; ++j) {
            if (A[j] <= pivot) {
                int tmp = A[i];
                A[i] = A[j];
                A[j] = tmp;
                ++i;
            }
        }

        A[end] = A[i];
        A[i] = pivot;
        return i;
    }
		
3,简单选择排序：

在要排序的一组数中，选出最小（或者最大）的一个数与第1个位置的数交换；然后在剩下的数当中再找最小（或者最大）的与第2个位置的数交换，依次类推，直到第n-1个元素（倒数第二个数）和第n个元素（最后一个数）比较为止。

操作方法：

第一趟，从n 个记录中找出关键码最小的记录与第一个记录交换；

第二趟，从第二个记录开始的n-1 个记录中再选出关键码最小的记录与第二个记录交换；

以此类推.....

第i 趟，则从第i 个记录开始的n-i+1 个记录中选出关键码最小的记录与第i 个记录交换，

直到整个序列按关键码有序。

java代码示例(正序)：

	public static void simpleSort(int[] arr) {
        final int len = arr == null ? 0 : arr.length;
        for (int i = 0; i < len; ++i) {
            int max = getMax(arr, i);//获取最大的元素
            if(max != i) {
                int temp = arr[i];
                arr[i] = arr[max];
                arr[max] = temp;
            }
        }
    }

    public static int getMax(int[] arr, int start) {
        final int len = arr == null ? 0 : arr.length;
        int k = start;
        for (int i = start; i < len; i++) {
            if(arr[k] < arr[i]) {
                k = i;
            }
        }
        return k;
    }

	
