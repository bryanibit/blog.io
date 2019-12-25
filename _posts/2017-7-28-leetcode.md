---
layout: post
title: Leetcode
date: 2017-7-28
categories: blog
tags: [理论梳理]
description: Leetcode & Codeing skill
---

## BackTracking

**Do not try in-place like```s = s.subtr(0, i)`` in backtracking(recursive operation)**

## STL basic function arguments meaning:

* substr()
* vector<>()
* assign()
* std::find()
* erase()

## longest substring without repeating characters

* C++ set
```
template < class T,                        // set::key_type/value_type
           class Compare = less<T>,        // set::key_compare/value_compare
           class Alloc = allocator<T>      // set::allocator_type
           > class set;
```
The value in *set* container can not be modified (their elements are *const*), but they can be **removed** and **inserted**.  
The value in *set* is ordered in according to specific weak ordering criterion. *set* containers are generally slower than *unordered_set* containers to access individual elements by their key, but they allow the **direct iteration** on subsets based on their order.

**Basic Operation**:  
```
empty() size()  
insert() emplace() erase() //insert and remove
find()  
if (m.count(val)) // val exists
operation=// set s; set a; s = a;
```

* C++ map
```
template < class Key,                                     // map::key_type
           class T,                                       // map::mapped_type
           class Compare = less<Key>,                     // map::key_compare
           class Alloc = allocator<pair<const Key,T> >    // map::allocator_type
           > class map;
```
The value in *map* is ordered by its key in according to specific weak ordering criterion. *Map* containers are generally slower than *unordered_map* containers to access individual elements by their key, but they allow the direct iteration on subsets based on their order.

**Basic Operation**:  
```
empty() size()  
insert() emplace() erase() //insert and remove
find() 
if (m.count(key)) // key exists 
```

## DP(Dynamic Programming)

*Those who cannot remember the past are condemned to repeat it.* -- **Dynamic Programming**. In a nutshell, dynamic programming is recursion without repetition.

In leetcode, the DP problems can be split into two categaries: continue and discrete.  
**review all problems**:   
* Continue:  
if f(i) is not only connected to f(i-1), also to f(i-2), then you do not only need one variable, such as max_val for recording the largest value, but also need another value to iterate, like a, b = a, a+b(Fibonacci numbers).

* Discrete:  
For each element, you can choose do nothing or do operations(like add it or minus it).

Right now, I wanna introduce one kind of DP with conditions. The problems are both based on a 2d matrix including only "0" and "1".

* First one -- leetcode 221. [Maximal Square(medium)](https://leetcode.com/problems/maximal-square/)
Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.  
**Iteration function**(*Initial operation is omitting*):  
```cpp
vector<vector<int>> dp(matrix.size(), vector<int>(matrix.at(0).size(), 0))
if(matrix[i][j] == 1)
	dp[i][j] = min(dp[i-1][j-1], dp[i-1][j], dp[i][j-1])
else // do nothing
	dp[i][j] = matrix[i][j]
```

* Second one -- leetcode 63. [Unique Paths II(medium)](https://leetcode.com/problems/unique-paths-ii/)  
A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).  
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).  
Now consider if some obstacles(use 1 to represent) are added to the grids. How many unique paths would there be?  
**Iteration function**(I achieve it in place here):(*Initial operation is omitting*)
```cpp
if(grid[i][j] == 1)
	grid[i][j] = 0
else// grid[i][j] = 0
	grid[i][j] = grid[i-1][j] + grid[i][j-1]]
```

## Leveshtein Distance



## Find the shortest path - SPFA

The following pseudocode does not think of minus loop:

```cpp
// 3 vector/array: dist, visited, path  
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
**stack** is *first in, last out*, but **queue** is different, which is *first in, first out*. Both of them have **pop()**/**push()**/**emplace()**, but both have diffent location(front or back) for poping and pushing. So **stack** has **top()** func, however, **queue** has **front()** which just acquires "oldest" element that **pop** will delete from queue. And **queue** has **back()** for "newest" element, too.

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

When using variables reference and modify it in the recursive process, you should be careful. There is an example for rectify the modification after *each time of return false*. There is link of [leetcode: word search](https://leetcode.com/problems/word-search/). PS: **==** has priority to **||** or **&&**. 

## vector operations

1. erase()
```cpp
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

## set Operation(union and intersection)

There, I will introduct two STL function called ```set_union``` and ```set_intersection```.

eg. Find the common part of each set in ```vector<set<int>> v```
```cpp
#include <iterator> //for inserter
#include <algorithm> // for swap
set<int> __intersection;
set<int> temp = v.at(0);
for (int i = 0; i < v.size(); ++i){
	set_intersection(temp.begin(), temp.end(), v.at(i).begin(), v.at(i).end(),
	std::inserter(__intersection, __intersecion.begin())); 
	// __intersection has function insert(), so std::inserter is OK
	// if changed to back_inserter from inserter, error for not
	// have push_back for __intersection, because __intersection is
	// a set not a vector
	std::swap(__intersecion, temp);
	__intersection.clear(); // clean set of containing each time's result
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

```cpp
#include <sstream>  
template <class Type>  
Type stringToNum(const string& str)  
{  
    istringstream iss(str);  
    Type num;  
    iss >> num;  
    return num;      
}  
```

## int to string

1.
```cpp
int a = 10;
char *intStr = itoa(a);
string str = string(intStr);
```

2.
```cpp
int a = 10;
stringstream ss;
ss << a;
string str = ss.str();
```

3.
```cpp
#include <string>
std::string s = std::to_string(42);
'''

## long int to string

```cpp
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

```cpp
string str;
double value = atof(str.c_str());
```

```cpp
string word;  
openfile >> word;
double lol = atof(word.c_str());
```

## double to string

```cpp
string s="235.19123";   **最后输出的number只能显示s中的六位（从高位数）**
std::stringstream ss(s);
double number=0;
ss>>number;
```

## 二叉搜索树

同一层的情况下，右边的元素一定比左边的大

* 插入一个数

```
待补充
```

* 删除一个数

```
待补充
```

## 最小生成树

生成树是建立在无向图中的，对于有向图，则没有生成树的概念，最小生成树不一定唯一，当所有边权值不同时唯一。但是权值之和时一定的

## 邻接矩阵/逆邻接矩阵

无向图的邻接矩阵时对称的，主队角元素为0，有向图不一定

## 连通图

每个顶点之间都有一个边的为连通图

## 最大子数组

* 求取相邻子数组之和最大值：可以理解为在已知股价折线图上求最大收益的时间段

## Topological Sorting

Topological sorting is not possible if a graph is not a *DAG*(Directed **Acyclic** Graph). Topological Sorting is mainly used for **scheduling jobs** from the given dependencies among jobs.  
Topological sorting can be used to find circle in a directed graph.  
e.g. we can find collapse dependence for course schedule in [leetcode-example](https://leetcode.com/problems/course-schedule/).  
Detailed description shown below as presudocode of topological sorting:
```python
for node in all_node:
    if node is not visited or not visiting:
	    dfs(node)
def dfs(node):
	# for list visited, 2:visiting, 1:visited
	# if visited[node] = 1, never consider it again.
	visited[node] = 2
	# if neighbor is 2, has circle, if 1, skip it over, if 0, dfs it.
	for node_neighbor in node.neighbor:
		if visited[node_neighbor] == 2:
			mark as circle
			break # if not break, dead loop
		if visited[node_neighbor] != 1:
			dfs(node_neighbor)
	stack.append(node) # no neighbor or visited[neighbor] == 1
	visited[node] = 1
```

## memory limited  

When occuring into memory limited situation, check whether *vector* or other data structure costs much resources **copying**.

## delete a node in a tree

```c
void dfs(TreeNode* root, const vector<int>& to_delete){
        if(root->left != nullptr)
            dfs(root->left, to_delete);
        if(root->right != nullptr)
            dfs(root->right, to_delete);
        if(std::find(to_delete.begin(), to_delete.end(), root->val) != to_delete.end()){
            if(root->left != nullptr)
                forest.push_back(root->left);
            if(root->right != nullptr)
                forest.push_back(root->right);
			// the flowing code can not delete node *root*
            root->val = NULL;
            root->left = nullptr;
            root->right = nullptr;
            root = nullptr; 
		}
}
```	
If you wanna delete a node whose value *to_delete* contains from a tree and you can use dfs like this:
```c
TreeNode* dfs(TreeNode* root, const vector<int>& to_delete){
        if(root->left != nullptr)
            root->left = dfs(root->left, to_delete);
        if(root->right != nullptr)
            root->right = dfs(root->right, to_delete);
        if(std::find(to_delete.begin(), to_delete.end(), root->val) != to_delete.end()){
            if(root->left != nullptr)
                forest.push_back(root->left);
            if(root->right != nullptr)
                forest.push_back(root->right);
			return nullptr;
		}
		return root;
}
```

## Someone needs to recall what he has done before  

### Best time to buy and sell

This kind of problems involves some basic conditions or limits. For example, 
1. You may **not** engage in multiple transactions at the same time (i.e., you must sell the stock before you buy again).  
2. You may complete as many transactions as you like (i.e., *buy one and sell one share of the stock multiple times*). The limit may be suitable for all *buy and sell* problem, because you have no need to buy and sell twice if the pirces of consistent two day have rised. You just buy in the first day and sell int the tird day. Not to mention that there may be a transaction fee.    

The following problems can be derived from the above:  
1. You may complete as many transactions as you like (i.e., buy one and sell one share of the stock multiple times). Ans: plus all positive diff_prices  
2. You may complete at most two or *K* transactions. Ans: for each price, you can hold buy it or sell it.  
3. buy and sell combines one transaction with a positive fee. Ans: if next price - last pirce - fee > 0 prove that can be added to final profits.  
4. buy and sell with a cooldown day. Ans: use DP algorithm. dp[i] denotes the max profit of the first i days, and then dp[i] = max(dp[i] , dp[i - 1], prices[i] - prices[j] + dp[j - 2]), here, j means buy time and i is sell time.  