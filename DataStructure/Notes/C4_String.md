# 串

## 定义

串就是字符串,是由零个或多个字符组成的有限序列.

子串: 串中任意个连续的字符组成的序列

主串: 包含子串的串

字符在主串中的位置: 字符在串中的序号, 从1开始

子串在主串中的位置: 子串的第一个字符在主串中的位置

## 存储结构

### 顺序存储表示

```c++
#define MAXLEN 255
typedef struct{
    char ch[MAXLEN];
    int length;
}SString;
```

### 堆分配存储

```c++
typedef struct
{
	char* ch;
	int length;
}HString;
HString S;
S.ch = (char*)malloc(MAXLEN*sizeof(char));
S.length = 0;
```

### 链式存储

```c++
//每个结点中，一个字符占一个字节，一个指针类型占四个字节，存储密度低
typedef struct StringNode
{
	char ch;
	struct StringNode* next;
}StringNode, *String;

//解决方案：让每个结点多存几个字符
```

## 基本操作

注：使用顺序存储，数组的第一个字符抛弃不使用，达到位序与下标相同的目的

### 求子串

```c++
bool SubString(SString& Sub, SString S, int pos, int len)
{
	if (pos + len - 1 > S.length)
		return false;
	for (int i = pos; i < pos + len; i++)
		Sub.ch[i - pos + 1] = S.ch[i];
	Sub.length = len;
	return true;
}
```

### 比较

```c++
int StrCompare(SString S, SString T)
{
	for (int i = 1; i <= S.length && i <= T.length; i++)
		if (S.ch[i] != T.ch[i])
			return S.ch[i] - T.ch[i];
	return S.length - T.length;
}
```

### 定位

```c++
int Index(SString S, SString T)
{
	int i = 1, n = StrLength(S), m = StrLength(T);
	SString sub;	//用于暂存子串
	while (i <= n - m + 1)
	{
		SubString(sub, S, i, m);
		if (StrCompare(sub, T) != 0) i++;
		else return i;
	}
	return 0;	//没有匹配的子串
}
```

## 模式匹配算法

### 暴力解法

思路同`定位`操作

```c++
//时间复杂度，最坏O(nm) 最好O(n)
int BF(SString S, SString T)
{
	int i = 1, j = 1;
	while (i <= S.length && j <= T.length)
	{
		if (S.ch[i] == T.ch[j])
		{
			i++;
			j++;
		}
		else
		{
			i = i - j + 2;
			j = 1;
		}
	}
	if (j > T.length)
		return i - T.length;
	else return 0;
}
```



### KMP

对于任何一个next数组，next[1] = 0 next[2] = 1，这是固定的(应试技巧)

```c++
///最坏时间复杂度为 O(m+n)
///其中，求next数组时间复杂度为O(m)
///模式匹配过程最坏时间复杂度为O(n)
int KMP(SString S, SString T, int next[])
{
	int i = 1, j = 1;
	while (i <= S.length && j <= T.length)
	{
		//这里的前提是字符串数组0下标的位置是没有元素的, 元素下标与位序相同
		//这里的if代表两种情况，
		// 1 是模式串第一个字符就失配，因为第一个字符的前缀和后缀都是0，所以让模式串和主串都往前挪一下就好了
		// 2 是当前字符匹配成功，i和j都往后移
		if (j == 0 || S.ch[i] == T.ch[j])	
		{
			i++;
			j++;
		}
		else
		{
			j = next[j];
		}
	}

	if (j > T.length)
		return i - T.length;
	else
		return 0;	//匹配失败
}
```

