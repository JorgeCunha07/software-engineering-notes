# Algorithms in Java

## Sort

Sorting algorithms are fundamental to computer science, as they provide a way to organize and manage data in a structured manner. From searching for specific items in a dataset to optimizing data processing, sorting is an essential skill for every programmer.

### Key Concepts in Sorting Algorithms

#### Comparison-based sorting vs non-comparison-based sorting

Comparison-based algorithms compare elements of a dataset to determine their order, while non-comparison-based algorithms use other techniques, like counting or radix sort.

#### Stable vs unstable sorting

A stable sorting algorithm maintains the relative order of equal elements in the sorted output, while an unstable algorithm does not guarantee this.

#### In-place vs out-of-place sorting

In-place sorting algorithms sort the data in the original memory location, requiring minimal extra memory. Out-of-place algorithms use additional memory to perform sorting.

#### Time complexity and space complexity

These measures indicate how an algorithm's performance scales with the size of the input data, helping you evalueate its efficiency.

### Popular Sorting Algorithms in Java

#### Bubble Sort

In Bubble Sort, adjacent elements are compared and swapped if they are in the wrong order. This process is repeated until no swaps are needed, indicating that the list is sorted.

```java
public void bubbleSort(int[] arr) {
  int n = arr.length;
  boolean swapped;

  for(int i = 0; i < n - 1) {
    swapped = false;

    for(int j = 0; j < n - 1 - i; j++) {
      if(arr[j] > arr[j+1]) {
        int temp = arr[j];
        arr[j+1] = arr[j];
        arr[j] = temp;
        swapped = true;
      }
    }

    if(!swapped) break;
  }
}
```

#### Selection Sort implementation

In Selection Sort, the algorithm divides the input into a sorted and an unsorted region.
It repeatedly finds the minimum (or maximum) element in the unsorted region and moves it to the end of the sorted region until the entire list is sorted.

```java
// [1, 2, 3, 4, 10]
public void selectionSort(int[] arr) {
  int n = arr.length;

  for(int i = 0; i < n - 1; i++) {
    int minIndex = i;

    for(int j = i + 1; j < n; j++) {
      if(arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }

    int temp = arr[minIndex];
    arr[minIndex] = arr[i];
    arr[i] = temp;
  }
}
```

#### Insertion Sort implementation

Insertion Sort works by builing the sorted output one item at a time. For each element, the algorithm compares it with the elements in the sorted region, shifting them if they are greater than the current element, and inserts the element in its correct position.

```java
public void insertionSort(int[] arr) {
  int n = arr.length;

  for(int i = 1; i < n; i++) {
    int key = arr[i];
    int j = i - 1;

    while(j >= 0 && arr[j] > key) {
      arr[j + 1] = arr[j];
      j--;
    }

    arr[j + 1] = key;
  }
}
```

#### Quick Sort implementation

Quick sort is a divide-and-conquer algorithm that works by selecting a 'pivot' element from the list and partitioning the other elements into two groups, according to whether they are less than or greater than the pivot. the sub-lists are then sorted recursively. The partition function rearranges the array and returns the pivot index.

```java
public void quickSort(int[] arr, int low, int high) {
  if(low < high) {
    int pivotIndex = partition(arr, low, high);
    quickSort(arr, low, pivotIndex - 1);
    quickSort(arr, pivotIndex + 1, high);
  }
}

public int partition(int[] arr, int low, int high) {
  int pivot = arr[high];
  int i = low - 1;

  for(int j = low; j < high; j++) {
    if(arr[j] < pivot) {
      i++;
      int temp = arr[i];
      arr[i] = arr[j];
      arr[j] = temp;
    }
  }

  int temp = arr[i + 1];
  arr[i + 1] = arr[high];
  arr[high] = temp;
  return i + 1;
}
```

#### Merge Sort implementation

Merge Sort is another divide-and-conquer algorithm that recursively divides the input in two halves, sorts each half, and then merges the sorted halves to produce the final output. The 'merge' function combines two sorted subarrays into a single sorted array.

```java
public void mergeSort(int[], int left, int right) {
  if(left < right) {
    int mid = (left + right) / 2;
    mergeSort(arr, left, mid);
    mergeSort(arr, mid + 1, right);
    merge(arr, left, mid, right);
  }
}

public void merge(int[] arr, int left, int mid, int right) {
  int n1 = mid - left + 1;
  int n2 = right - mid;

  int[] leftArray = new int[n1];
  int[] rightArray = new int[n2];

  for(int i = 0; i < n1; i++) {
    leftArray[i] = arr[left + i];
  }

  for(int i = 0; i < n2; i++) {
    rightArray[i] = arr[mid + 1 + i];
  }

  int i = 0, j = 0, k = left;

  while(i < n1 && j < n2) {
    if(leftArray[i] <= rightArray[j]) {
      arr[k++] = leftArray[i++];
    } else {
      arr[k++] = rightArray[j++];
    }
  }

  while (i < n1) {
    arr[k++] = leftArray[i++];
  }

  while(j < n2) {
    arr[k++] = rightArray[j++];
  }
}
```
