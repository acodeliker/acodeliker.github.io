# <font size=6>Interview problems since 2021.7</font>

### 7.16

#### 1. 

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

#### 2. SingleChain_Sort

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