# Stack&Queue

## Stack

### 题型：给定进栈顺序，问合法出栈顺序
n个不同的元素进栈，出栈元素不同排列的个数为 1/(n+1)C 2n n

### 顺序栈

#### 结点定义
```c++
#define MaxSize 10
typedef struct{
    ElemType data[MaxSize];
    int top;
}SqStack;
```

#### 初始化
```c++
void InitStack(SqStack &S)
{
    S.top = -1;
}
```

#### 判断栈空
```c++
bool StackEmpty(SqStack S)
{
    return S.top == -1;
}
```

#### 进栈
```c++
bool Push(SqStack &S, ElemType x)
{
    if(S.top == MaxSize-1)
        return false;
    S.data[++S.top] = x;
    return true;
}
```

#### 出栈
```c++
bool Pop(SqStack &S, ElemType &x)
{
    if(S.top == -1)
        return false;
    x = S.data[S.top--];
    return true;
}
```

#### 读栈顶元素
```c++
bool GetTop(SqStack S, ElemType x)
{
    if(S.top == -1)
        return false;
    x = S.data[S.top];
    return true;
}
```

#### 共享栈
```c++
//设置两个top指针，一个往下减一个往上增
#define MaxSize 10
typedef struct{
    ElemType data[MaxSize];
    int top0;   
    int top1;
}ShStack;

void InitStack(ShStack &S)
{
    S.top0 = -1;
    S.top1 = MaxSize;
}

//栈满的条件：top0 + 1 == top1
```

### 链栈

#### 结点定义
```c++
typedef struct LinkNode{
    ElemType data;
    struct LinkNode *next;
} *LiStack;
```
添加删除相当于仅限制在单链表头部位置的插入和删除

## Queue

### 顺序结构

#### 结点定义
```c++
#define MaxSize 10
typedef struct{
    ElemType data;
    int front, rear;//front指向队头元素,rear指向队尾元素的后一个位置
}SqQueue;
```

#### 初始化

```c++
void InitQueue(SqQueue& Q)
{
	Q.front = Q.rear = 0;
}
```

#### 判断是否为空

```c++
bool QueueEmpty(SqQueue Q)
{
    return Q.rear == Q.front;
}
```

#### 入队

```c++
bool Enqueue(SqQueue& Q, ElemType x)
{
  //如果不浪费这个存储空间，则判断队列满的条件就与判断队空的条件冲突了
	if ((Q.rear + 1) % MaxSize == Q.front) return false;
	Q.data[Q.rear] = x;
	Q.rear = (Q.rear + 1) % MaxSize;
	return true;
}
```

#### 出队

```c++
bool Dequeue(SqQueue& Q, ElemType& x)
{
	if (Q.rear == Q.front)
		return false;
	x = Q.data[Q.front];
	Q.front = (Q.front + 1) % MaxSize;
	return true;
}
```

#### 获取队头元素

```c++
bool GetHead(SqQueue Q, ElemType& x)
{
	if (Q.rear == Q.front) return false;
	x = Q.data[Q.front];
	return true;
}
```

#### 获取元素个数

```c++
int Count(SqQueue& Q)
{
	return (Q.rear - Q.front + MaxSize) % MaxSize;
}
```

#### 避免浪费无法使用的那个空间的结点定义

```c++
#define MaxSize 10
typedef struct{
    ElemType data;
    int front, rear;//front指向队头元素,rear指向队尾元素的后一个位置
    int size;	//队列当前长度
}SqQueue;

typedef struct{
    ElemType data;
    int front, rear;//front指向队头元素,rear指向队尾元素的后一个位置
    int tag; //描述最近进行的是删除还是插入操作，从而判断队空还是队满	
}SqQueue;
```

### 链式

#### 结点定义

```c++
typedef int ElemType;

typedef struct LinkNode {
	ElemType data;
	struct LinkNode* next;
}LinkNode;

typedef struct {
	LinkNode* front, * rear;
}LinkQueue;
//带头结点版本，front指向头节点；不带头结点的版本，front指向第一个元素
```

#### 初始化

```c++
void InitQueue(LinkQueue& Q)
{
	//初始化时,front,rear都指向头节点
	Q.front = Q.rear = new LinkNode;
	Q.front->next = NULL;
}

//不带头结点
void InitQueue(LinkQueue& Q)
{
	Q.front = NULL;
	Q.rear = NULL;
}
```

#### 判空

```c++
bool IsEmpty(LinkQueue Q)
{
	return Q.front == Q.rear;
	//return Q.front->next == NULL;
}

//不带头结点
bool IsEmpty(LinkQueue Q)
{
	return Q.front == NULL;
}
```

#### 入队

```c++
void Enqueue(LinkQueue& Q, ElemType x)
{
	LinkNode* s = new LinkNode;
	s->data = x;
	s->next = NULL;
	Q.rear->next = s;
	Q.rear = s;
}

//不带头结点
void Enqueue(LinkQueue& Q, ElemType x)
{
	LinkNode* s = new LinkNode;
	s->data = x;
	s->next = NULL;
	if (Q.front == NULL)	//在空队列中插入第一个元素，不带头结点需要特殊处理
	{
		Q.front = s;
		Q.rear = s;
	}
	else
	{
		Q.rear->next = s;
		Q.rear = s;
	}
}
```

#### 出队

```c++
//带头结点
bool DeQueue(LinkQueue& Q, ElemType& x)
{
	if (Q.front == Q.rear) return false;	//空队
	LinkNode* p = Q.front->next;
	x = p->data;
	Q.front->next = p->next;
	if (Q.rear == p) Q.rear = Q.front;	//删除的结点时最后一个结点需要特殊处理
	delete p;
	return true;
}

//带头结点
bool DeQueue(LinkQueue& Q, ElemType& x)
{
	if (Q.front == NULL) return false;	//空队
	LinkNode* p = Q.front;
	x = p->data;
	Q.front = p->next;
	if (Q.rear == p)	//删除的是最后一个结点，需要特殊处理
	{
		Q.front = NULL;	//这句话可以不要，因为如果删除的是最后一个结点，他本身的next就指向NULL
		Q.rear = NULL;
	}
	delete p;
	return true;
}
```

