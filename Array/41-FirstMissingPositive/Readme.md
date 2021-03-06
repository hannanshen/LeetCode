## 41.First Missing Positive

>Given an unsorted integer array, find the smallest missing positive integer.
>
>Example 1:
>
>Input: [1,2,0]
>Output: 3
>Example 2:
>
>Input: [3,4,-1,1]
>Output: 2
>Example 3:
>
>Input: [7,8,9,11,12]
>Output: 1
>
>Note:
>
>Your algorithm should run in O(n) time and uses constant extra space.



先来看题目，题目的要求是，在给定的一个**未排序的**数组中，找到**缺失的最小的正数**。   

此外，题目还要求了我们使用O(1)的空间复杂度。  

这里有两个关键的点，第一是**未排序**。这个条件告诉了我们，不能从数组的最大项开始，不断往前遍历。  

第二个关键的点是，我们需要找**最小的正数**。这代表了，数组中的`0`和`负数`，我们是不需要考虑的。另外，除了一种**特殊情况**外，我们找到的数，一定会位于`0`到`n`之间（这里的n指的是数组的长度）。  

为什么呢，举个例子，假设这个数组是

```
1,2,3,4,5,6,7,8,9,10
```

那么此时的结果就是`n+1`，结果为11。那如果这串数组不是**正自然数**的话，也就是说在这串数组中，有某一项或者某几项是0，是负数，是大于n的数，那么我们要找的结果会落在这个数组中的这些位置中。 

如果你理解了上面的这段话，那么我们就可以说解题方法了。  

**我们先不考虑特殊情况，也不考虑常数的空间复杂度。**  

那么思路是这样的：我们经过分析确定了结果会位于1到n之间，那么我们可以设置一个hash表，hash表的下标就是数组中出现过的数字，只要数组中某一个数字出现了，那么就把hash表对应的项设为1。  

比如，我们的数组是`0，1，2，5，7`

这个数组的长度是5，所以我们定义一个长度为5的数组，初始化为0，作为我们的hash表。  

然后我们开始填数据，根据我们的规则，小于0和大于数组长度n的数字，我们不考虑。然后将符合条件的数组项，作为hash表的下标，将对应的项设置为1。

**注意，因为我们的hash表是从0开始的，所以我们在设置下标的时候，将对应的数组项减一。**

例如：

- 第一项是0，不考虑
- 第二项是1，符合条件，那么把数字减一，给hash[a[1] - 1]的项设置为1，此时hash表为`1，0，0，0，0`
- 第三项是2，符合条件，那么把数字减一，给hash[a[2] - 1]的项设置为1，此时hash表为`1，1，0，0，0`
- 第四项是5，符合条件，那么把数字减一，给hash[a[4] - 1]的项设置为1，此时hash表为`1，1，0，0，4`
- 第五项是7，不考虑  

此时我们已经构造好hash表了，那么从0开始遍历这个hash表，直到遇到第一个为0的项，说明这一项在原数组中是不存在的，那么将下标加1（在保存数据的时候我们把下标减了1，所以要加回去），就是我们的答案了。  

但是，这个解法虽然在时间复杂度上是O(n)，是符合要求的，但是因为我们额外的申请了一个大小为n的hash表，所以在空间复杂度上，并不符合题目的要求，我们下面看看怎么来改进。  

### 更进一步的解法

我们发现，我们的数组长度为n，hash表长度为n，为了实现常数项的时间复杂度，我们可以考虑将原数组作为hash表。  

那么还有一个问题，我们在使用额外空间的hash表的时候，将出现了的数字设为1，然后查找为0的项，以此来找到缺失的第一个正数。但是如果我们使用原数组的话，显然我们是不能将原数组的值设置为1的。然后考虑到，对于小于1或者大于n的数，我们是不考虑的，所以我们可以把目标设置为**找到第一项不符合要求的数字，然后返回下标**。  

那么我们要做的事情就很清晰了，将原数组看成一个hash表，然后当遍历到一个符合条件的数字时，把他放到他应该在的地方。来看几个例子：  

```
1,2,3,4,5
```

在这个数组中，每一项都去到了他应该去的位置，**a[i] == i - 1**。

再看一个例子：

```
1,2,5,4,6
```

在这里，`i = 0`的1和`i= 1`的2是符合要求的，但是当我们遍历到第三项`a[2] = 5`的时候，发现他应该存在的位置是数组的第五项，也就是`i = 4`的位置。

所以，我们将这一项和`i = 4`的项交换，然后我们的数组就变成了：

```
1,2,6,4,5
```

此时，我们遍历的下标`i`还是等于2，但是此时的`a[2] = 6`，在我们的规则中，6大于数组的长度，所以我们不考虑他，继续我们的遍历，`i++`，此时i来到了`i = 3`。

在`a[3] = 4`这一项中，是符合**a[i] == i - 1**这个规则的，所以我们跳过，继续遍历。 

来到了`i= 4`这一项，同样符合要求，我们不做任何的改变。

此时遍历已经结束了，我们从头再遍历一次这个数组，直到找到**小于1或大于n**的一项，返回他的下标，加上1，就是我们的最终答案了。如果全部都符合要求，那么返回`n + 1`。

总结一下：

- 遍历这个数组
- 如果当前的数据项是小于1或者大于n的，不考虑，直接遍历下一项
- 如果当前的数据项位于1和n之间，那么判断这个数据项符不符合**a[i] == i - 1**这一规则，如果不符合，把他**交换**到他应该去的位置，然后继续判断这一数据项，直到这个数据项不符合要求
- 再次遍历数组，找到答案

