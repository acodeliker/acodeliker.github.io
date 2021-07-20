# <font size=6>Interview problems since 2021.7</font>

### 7.16

```cpp
// 棱镜空间笔试题
```
#### 1. Finding the elements of an increasing matrix of rows and columns

> problem: 
    
    Given a two-dimensional array, it is known that each row and each column are sorted strictly in ascending order, and it is required to determine whether a number exists in this array in O(n) time

> analyse: 
    
    Finding an increasing matrix of rows and columns:
    
    First, locate the element in the upper right corner of the matrix directly. If the element is greater than the number 6 you are looking for, go to the left, and if it is smaller than the number you are looking for, you will go down until you find the number 6 you are looking for. 
    
    The time complexity of this method is O(m + n).

> solu:

```cpp
#define ROW 4
#define COL 4
class Solution {
public:
	bool YoungMatrix(int array[][COL], int searchKey)
	{
		int i = 0, j = COL - 1;
		int var = array[i][j];
		while (true)
		{
			if (var == searchKey)
			{
				return true;
			}
			else if (var < searchKey && i < ROW - 1)
			{
				var = array[++i][j];
			}
			else if (var > searchKey && j > 0)
			{
				var = array[i][--j];
			}
			else
			{
				return false;
			}
		}
	}
};
```

#### 2. Best Time to Buy and Sell Stock

> problem:

    You are given an array prices where prices[i] is the price of a given stock on the ith day.

    You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock.

    Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

> Example 1:

	Input: prices = [7,1,5,3,6,4]
	Output: 5
	Explanation: Buy on day 2 (price = 1) and sell on day 5 (price = 6), profit = 6-1 = 5.
	Note that buying on day 2 and selling on day 1 is not allowed because you must buy before you sell.

> Constraints:

	1 <= prices.length <= 105
	0 <= prices[i] <= 104

> analyse:
	
Record **[Minimum value bought before today]**

Calculate **[the minimum value of buying before today, the profit of selling today]**, that is, the **[maximum profit of selling today]**

Compare **[the maximum profit per day] and take the maximum value]**

> solu:

```cpp
class Solution {
public: 
	int maxProfit(vector<int> prices) {
        if(prices.size() <= 1)
            return 0;
        int _min = prices[0], _max = 0;
        for(int i = 1; i < prices.size(); i ++) {
            _max = max(_max, prices[i] - _min);
            _min = min(_min, prices[i]);
        }
        return _max;
    }
}
```

#### 3. SingleChain_Sort

> problem: 

    Write a function to sort data in a singly linked list
    Such as 5->7->1->10->NULL, the structure is
    
    struct SingleChain
    {
        int num;

        struct SingleChain *next;
    };

    The function is int sort(struct* head); the head pointer points to the head 5 of the singly linked list.

> analyse:

    directly bubblesort

> solu:

```cpp
struct SingleChain
{
	int num;
	struct SingleChain *next;
};
// 函数为int sort（struct* head）； head指针指向单链表头5。

typedef struct SingleChain SC;
typedef struct SingleChain *SCp;
SC sc;

int sort(SCp & head)
{
	if (head || head->next = nullptr)
	{
		return 0;
	}

	SCp L = head;
	SCp p = nullptr;
	SCp q = nullptr;

	for (p = L; p != nullptr; p = p->next)
	{
		for (q = L; q->next != nullptr; q = q->next)
		{
			if (q->num > q->next->num)
			{
				int temp = q->num;
				q->num = q->next->num;
				q->next->num = temp;
			}
		}
	}
	return 0;
}
```

### 7.7

```cpp
/**
* 云伽智能笔试题
* 
**/
int main() {
	// 1.
	int a=10, b = 20;
	printf("%d..%d\n", a + b, b *= 2);

	/************************************/
	// 2
	unsigned long* p1;
	unsigned char* p2;

	p1 = (unsigned long*)0x81000; 
	// Assign a hexadecimal number to a pointer to unsigned long data, that is, the pointer value is 0x81000, which represents the address where the unsigned long data is stored! address! address!
	p2 = (unsigned char*)0x80100; // similar with p1

	printf("%p\n%p\n", p1+5, p2+5); // p1 moves backward byte-length equal to (0x81028)H ( == 8 bytes per unit address * 5 unit address length == (40)10)
	/*************************************/

}

```
### 7.3


```cpp
// 益玩网络
class A
{
public:
	A() { p = this; } 
 	// Does the constructor have `this`? But why can the function body appear (by the way, although it is not that the function prototype does not imply this, this can be used in the constructor body, which is used to point to the object itself)

	~A()
	{
		if (p != nullptr)
		{
			delete p; //p must point to a dynamically allocated address ah, ah
			//delete this;

			p = nullptr;
		}
	}
	// This is actually the design idea of ​​smart pointers
	A *p; // const A* p;
};

int main()
{
	//A a;
	A *pA = new A; // This can be correctly released by the custom destructor of class A
	if (pA)
	{
		cout << pA->p << endl;
	}
}

```