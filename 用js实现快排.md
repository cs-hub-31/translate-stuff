# 用js实现快排

##### By [Charles Stover](https://itnext.io/@Charles_Stover) | Dec 12, 2018

[原文](https://itnext.io/implementing-quicksort-in-javascript-e1acfa16d033)

## 原理🤔

Quicksort通过从数组中选取一个元素并将其表示为基准点，把数组中的所有其他元素分为两类 - 它们小于或大于此基准点。

然后把作为这一轮排序结果的两个数组（数组元素都小于基准点的数组和数组元素都大于基准点的数组）再进行相同的排序。即分别再选个基准点，然后基于基准点分成两个数组元素分别小于和大于基准点的数组。

最终，由于最后数组中没有元素或只有一个元素，因此不用再比较了。剩下的值都已经基于基准点排好序了。

（译者：内容有删减，说的有些啰嗦）

## 如何实现

js的Array原型的sort方法使用另外一种方法实现排序的，我们不用这个实现快排。我们自己创建一个方法，待排序的数组作为参数，输出排好序的数组。

	const quickSort = (unsortedArray) => {
	  const sortedArray = TODO(unsortedArray);
	  return sortedArray;
	};

由于数组中项的“值”可能不是很明显，我们应该为排序算法提供一个可选参数。在js中，字符串和数字会排好序的，但是对象不会。我们要根据user对象的age字段给数组排序。

	const defaultSortingAlgorithm = (a, b) => {
	  if (a < b) {
	    return -1;
	  }
	  if (b > a) {
	    return 1;
	  }
	  return 0;
	};
	
	const quickSort = (
	  unsortedArray,
	  sortingAlgorithm = defaultSortingAlgorithm
	) => {
	  const sortedArray = TODO(unsortedArray);
	  return sortedArray;
	};
	
由于我们是不断地重复找基准点，然后输出全小于基准点和全大于基准点的数组的这个步骤。我们希望用递归来实现，这样可以少写代码。

你可以随便找个基准点：第一个、中间、最后一个、随机一个。为了简单起见，我们假设基准点的选取对时间复杂度没有影响。我在本文中总是使用最后一个元素作为基准点，因为要配合下图的演示（图中用的是最后一个元素，来源维基百科）。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/quickSorted.gif?raw=true)

数组基于基准点分成两个数组：小于基准点的数组放前面，大于基准点的数组放后面。最终，把基准点放在两个数组的中间，重复以上步骤。

为了不改变原数据，我们创建了新数组。这不是必要的，但是是个好的习惯。

我们创建recursiveSort作为递归函数，它将递归子数组（从起始索引到结束索引），中途改变sortedArray数组的数据。整个数组是第一个传递给此递归函数的数组。

最后，返回排好序的数组。

recursiveSort函数有一个pivotValue变量来表示我们的基准点，还有一个splitIndex变量来表示分隔小于和大于数组的索引。从概念上讲，所有小于基准点的值都将小于splitIndex，而所有大于基准点的值都将大于splitIndex。splitIndex被初始化为子数组的开头，但是当我们发现小于基准点时，我们将相应地调整splitIndex。

我们将循环遍历所有值，将小于基准点的值移动到起始索引之前。

	const quickSort = (
	  unsortedArray,
	  sortingAlgorithm = defaultSortingAlgorithm
	) => {
	
	  // Create a sortable array to return.
	  const sortedArray = [ ...unsortedArray ];
	
	  // Recursively sort sub-arrays.
	  const recursiveSort = (start, end) => {
	
	    // If this sub-array contains less than 2 elements, it's sorted.
	    if (end - start < 2) {
	      return;
	    }
	
	    const pivotValue = sortedArray[end];
	    let splitIndex = start;
	    for (let i = start; i < end; i++) {
	      const sort = sortingAlgorithm(sortedArray[i], pivotValue);
	
	      // This value is less than the pivot value.
	      if (sort === -1) {
	
	        // If the element just to the right of the split index,
	        //   isn't this element, swap them.
	        if (splitIndex !== i) {
	          const temp = sortedArray[splitIndex];
	          sortedArray[splitIndex] = sortedArray[i];
	          sortedArray[i] = temp;
	        }
	
	        // Move the split index to the right by one,
	        //   denoting an increase in the less-than sub-array size.
	        splitIndex++;
	      }
	
	      // Leave values that are greater than or equal to
	      //   the pivot value where they are.
	    }
	
	    // Move the pivot value to between the split.
	    sortedArray[end] = sortedArray[splitIndex];
	    sortedArray[splitIndex] = pivotValue;
	
	    // Recursively sort the less-than and greater-than arrays.
	    recursiveSort(start, splitIndex - 1);
	    recursiveSort(splitIndex + 1, end);
	  };
	
	  // Sort the entire array.
	  recursiveSort(0, unsortedArray.length - 1);
	  return sortedArray;
	};
	
我们将所有小于基准点的值移动到splitIndex指向的位置，其他的值不动（默认情况下，大于splitIndex，因为splitIndex从子数组的开头开始）。

一旦子数组被排序好后，我们将基准点放在中间，因为排序就是基于基准点排的，我们知道它的位置。

左边的所有值（从start到splitIndex  -  1）都会被递归排序，并且右边的所有值（从splitIndex + 1到end）也都会被递归排序。 splitIndex本身现在是基准点，不再需要对其进行排序。