# 栈与队列

## 0. 总结

## 1. 选择题

### 1.1 

> 设链表不带头结点且所有操作均在表头进行，则下列最不适合作为链栈的是( C )
>
> A. 只有表头结点指针，没有表尾指针的双向循环链表
>
> B. 只有表尾结点指针，没有表头指针的双向循环链表
>
> C. 只有表头结点指针，没有表尾指针的单向循环链表
>
> D. 只有表尾结点指针，没有表头指针的单向循环链表
>
> > 解析：对于双向循环链表，不管是表头指针还是表尾指针，都可以方便地找到表头结点，方便在头部做插入删除操作。而单循环链表通过尾指针可以方便的找到表头，而且很方便删除，但是通过表头指针找到尾指针从而删除头指针是比较烦琐的，需要O(n)，错选了D，没仔细看题目。

### 1.2

> 一个栈的入栈序列为 1, 2, 3, ..., n，出栈序列是 P1, P2, P3, ..., Pn. 若P2 = 3，则P3可能取值的个数为( C )
>
> A. n - 3     B. n - 2     C. n - 1     D. 无法确定
>
> > 解析：显然，3之后的4到n都是可以取的数。接下来分析1和2，P1可以是3之前入栈的数( 1和2 )，也可以是4，当P1为1时，P3可以取2，当P1为2时，P3可以取1，当P1为4时，P3可以取除了1, 3, 4 之外的所有数。故P3可能的取值个数为 n-1

## 2. 判断序列是否中心对称

假设单链表的表头指针为L，结点结构为 [data, next]，其中data域为字符型，设计算法判断该链表的全部n个字符是否中心对称。例如xyx，xyyx都是中心对称。

> [分析] 拿到这道题的疑惑点：表头指针是否代表有头结点。如果带头结点的话我的思路就可以用。看了答案之后是带头结点的。
>
> 我的思路：快慢指针，找到中间节点`(见C2线性表笔记中总结的使用快慢指针查找中间节点有何不同)`，然后将后半部分逆置，slow最终指向Round(n/2)，然后再从第一个元素开始与后半部分进行比较。时间复杂度，找到中间节点 O(n/2) 逆置O(n/2)，比较O(n/2)
>
> 答案思路：题目给出了元素的个数n，因此用一个数组和一个下标变量模拟栈，将前n/2个字符放入模拟的栈中，然后继续遍历链表，与每次弹栈的元素进行比较。当元素个数为奇数的时候需要先将指针后移一位，再与弹栈的元素相比。

```c++
bool SymmetryString(LinkList L, int n)
{
	int i;
  //这里使用new是因为 char s[n/2]的语法在vs中过不去，但是答案上是这样写的，感觉用vector也可以。
  //考试的话自己应该会用vector
	char* s = new char[n / 2];		
	Node* p = L->next;
	for (i = 0; i < n / 2; i++)
	{
		s[i] = p->data;
		p = p->next;
	}
	i--;								//让i指向栈顶元素
	if (n % 2 == 1)		//元素个数为奇数
		p = p->next;
	while (p != NULL && s[i] == p->data)
	{
		i--;
		p = p->next;
	}
	return i == -1;
}
```

## 3. 利用一个栈实现以下递归函数的非递归计算

$$
\left\{\begin{array}{lr}
1 ,  &  n=0\\
2x,& n=1\\
2xP_{n-1}(x)-2(n-1)P_{n-2}(x), &n>1
\end{array}
\right.
$$

> 【分析】其实不用栈，只需要先算出第0项和第1项之后用一个循环从n开始循环计算，维护两个数字就好了，但是题目要求用栈，就把需要使用的信息：n，当前层数的运算结果放入栈中，然后依次弹栈就可以了。

```c++
typedef struct
{
	int n, val;
}Node;
int CalculateByStack(int n, int x)
{
	stack<Node> st;				//这里使用了STL中的stack，需#include<stack>
	int first = 1, second = 2 * x;	//first 代表fn-2(x) second　代表 fn-1(x) 
	for (int i = n; i >= 2; i--)
	{
		Node t;
		t.n = i;				//将每一层的n放入栈中
		st.push(t);
	}
	while (!st.empty())
	{
		st.top().val = 2 * x * second - 2 * (st.top().n - 1) * first;
		first = second;
		second = st.top().val;
		st.pop();
	}
	return second;
}
```

## 4. 队列

某汽车轮渡口，过江渡船能载10辆车过江。过江车辆分为客车类和货车类，上渡船有如下规定：同类船先到先上船；客车先于货车上船，且每上４辆客车，才允许上一辆货车；若等待的客车不足４辆，则以货车代替；若无货车等待，允许客车都上船。试设计一个算法模拟渡口管理。

> 【分析】放上来的原因不是因为算法难度很大，而是刚看的时候有一会没理解懂这个题目在表达什么。。
>
> 翻译：两个队列，一个货车队列，一个客车队列，客车队列上４个货车才能上，如果有一个队列是空的，另一个就一直上。直到上到10个为止。参考答案给的代码感觉没有自己写的简洁，所以放自己的代码。

```c++
void LoadManagement(queue<char>& buses, queue<char>& trucks, vector<char>& load)
{
  //使用了STL中的　queue vector 需#include<queue> #include<vector>
	int busCnt = 0;		//当前上了几辆客车了
	int cnt = 0;		//船上有几辆车了
	while (cnt < 10)
	{
		while (!buses.empty())
		{
			load.push_back(buses.front());
			buses.pop();
			busCnt++;
			cnt++;
			if (busCnt == 4)
			{
				busCnt = 0;
				break;
			}
		}
		if (!trucks.empty())
		{
			load.push_back(trucks.front());
			cnt++;
			trucks.pop();
		}
		if (buses.empty() && trucks.empty()) break;	//队列都空了，但是还没上够10辆车
	}
}
```

