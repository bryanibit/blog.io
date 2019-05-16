---
layout: post
title: Leetcode
date: 2017-7-28
categories: blog
tags: [理论梳理]
description: Leetcode & Codeing skill
---

## Find the shortest path - SPFA

The following pseudocode does not think of minus loop:
```cpp
// 3 vector/array: dist, visited path
dist[] = std::numeric_limits<>::max()
visited[]=0
path[]
queue Q
dist[start_node] = 0
// any operations for Q will influence visited
Q.push(start_node)
//when node is in Q, his visited is 1
visited[start_node] = 1
while(!Q){
	u = Q.front()
	Q.pop()
	// node leaves, his visited becomes 0
	visited[u] = 0
	for(all edges connected node v for node u){
		//fasten
		if(dist[u] + edgesTov < dist[v]){
			path[u] = v
			dist[v] = dist[u] + edgesTov
			if(!visited[v]){
				Q.push(v)
				visited[v] = 1;
			}
		}
	}
}
```
Because of involving std::stack and std::queue, I want to introduce them right here, right now.  
**stack** is *first in, last out*, but **queue** is different, which is *first in, first out*. Both of them have **pop()**/**push()**/**emplace()**, which have diffent location(front or back) for poping and pushing. So **stack** has **top()** func, however, **queue** has **front()** which acquires "oldest" element that will delete from queue by **pop** firstly. And **queue** has **back()** for "newest" element, too.

## DFS(Deep First Search)

在DFS中，当某节点的**左右节点都为空**，或者该节点的**左右树都已经遍历完成**，则会return，也就是跳出DFS function，也就是开始**退栈**。举例：理解下面的函数，就要理解*pop_back*的作用，也就是在退栈的时候会执行，而什么时候会退栈？就是左右节点为空，或者左右树都已经遍历完成。
```c
void pointToVector(TreeNode * p, vector<int>& res)
	{
		res.push_back(p->val);
		if ((p->left == nullptr)&&(p->right == nullptr)) {
			sumAll += cal(res);
			return;
		}
		if (p->left != nullptr) {
			pointToVector(p->left, res);
			res.pop_back();
		}
		if (p->right != nullptr) {
			pointToVector(p->right, res);
			res.pop_back();
		}
	}
```

For a directed or undirected graph, the following code is the best pseudocode to iterate each node for only once.
The code has no loop to entire graph instead of using recursion.
```cpp
DFS(G, u)
    u.visited = true
    for each v ∈ G.Adj[u]
        if v.visited == false
            DFS(G,v)
init() {
    For each u ∈ G
        u.visited = false
     For each u ∈ G
        DFS(G, u)
}
```

DFS is normally implemented recursively, rather than using an explicit stack:
```
DFS(v):
if v is not marked
	mark v
	for each edge v-> w
		DFS(w)
```

The following code describes all paths from one point as start point to target point based on directed graph.
```cpp
// the first step: source is start point
void dfs(vector<vector<int>>& graph, int source, int target, vector<vector<int>>& path)
{
	onePath.push_back(source);
	if (source == target)
	{
		path.push_back(onePath);
	}
	for (auto i : graph[source])
	{
			dfs(graph, i, target, path);//every recursive location, think about the following pop_back
			onePath.pop_back();
	}
}
int main(){
	int target = graph.size() - 1;
	vector<vector<int>> path;
	dfs(graph, 0, target, path);
}
```

## vector operations

1. erase()
```
void removeDuplicate(std::vector<string>& s)
	{
		sort(s.begin(), s.end());
		for (int i = 1; i < s.size(); ++i)
		{
			if (s[i - 1] == s[i])
			{
				s.erase(s.begin()+i-1);
				--i; // point is here
			}
		}
	}
```

## string -- int -- char

```std::string(size_type n, char c);``` Fills the string with n consecutive copies of character c.
```std::string(const char * s, size_type n);``` Copies the first n characters from the array of characters pointed by s.
```string (const char* s);``` Copies the null-terminated character sequence (C-string) pointed by s.

```std::atoi(const char *);``` Transform c-type char * to int
```std::stoi(const string);``` Transform string to int

## 将string类型变量转换为常用的数值类型
//模板函数：（此方法具有普遍适用性）
\#include <sstream>  
template <class Type>  
Type stringToNum(const string& str)  
{  
    istringstream iss(str);  
    Type num;  
    iss >> num;  
    return num;      
}  


## int to string
1.

```
int a = 10;
char *intStr = itoa(a);
string str = string(intStr);
```

2.

```
int a = 10;
stringstream ss;
ss << a;
string str = ss.str();
```

3.

```\#include <string>
std::string s = std::to_string(42);
'''

## long int to string

```
#include <sstream>
std::string number;
std::stringstream strstream;
strstream << 1L;
strstream >> number;
```

## string to int

```
1.
string str="123";
atoi( str.c_str() );

2.
std::stoi(str);

3.
std::stringstream ss(str);
ss >> thevalue;
```

## string to double

```string str;
double value = atof(str.c_str());```

```
string word;  
openfile >> word;
double lol = atof(word.c_str());
```

## double to string

```
string s="235.19123";   **最后输出的number只能显示s中的六位（从高位数）**
std::stringstream ss(s);
double number=0;
ss>>number;
```

## 二叉搜索树

同一层的情况下，右边的元素一定比左边的大

* 插入一个数

```
```

* 删除一个数

```
```

## 最小生成树

生成树是建立在无向图中的，对于有向图，则没有生成树的概念，最小生成树不一定唯一，当所有边权值不同时唯一。但是权值之和时一定的

## 邻接矩阵/逆邻接矩阵

无向图的邻接矩阵时对称的，主队角元素为0，有向图不一定

## 连通图

每个顶点之间都有一个边的为连通图

## 最大子数组

* 求取相邻子数组之和最大值：可以理解为在已知股价折线图上求最大收益的时间段
