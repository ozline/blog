---
title: STL基本容器
date: 2018-10-17 01:31:43
tags: history
---

# STL基本容器

2018.10.03

### 顺序容器

- “数组”：vector
- “队列”：queue,deque,priority_queue

### 关联容器

- “集合”：set,multiset,unordered_set
- “字典”：map,unordered_map

# Vector 动态数组

头文件：``#include <vector>``

### 使用方法

```c++
vector<int> vec1; //默认初始化,vec1是空的
vector<int> vec2(vec1);//使用vec1初始化vec2
vector<int> vec3(vec1.begin(),vec1.end());//使用vec1初始化vec3
vector<int> vec4(10); //10个值是0的元素
vector<int> vec5(10,4);//10个值是4的元素

vec1.push_back(100);//尾部添加元素
int size = vec1.size();//元素个数
bool isEmpty = vec1.empty();//判断是否为空
cout<<vec[10]<<endl; //取得第一个元素
vec1.insert(vec1.end(),5,3);//从vec1.back位置插入5个值为3的元素
vec1.pop_back();//删除末尾元素
vec1.erase(vec1.begin(),vec1.begin()+2);//删除vec1[0]-vec[2]之间的元素，不包括vec1[2]其他元素的前移
cout<<(vec1==vec2)?true:false;//判断是否相等==、!=、>=、<=...
vector<int>::iterator iter = vec1.begin();//获取迭代器首地址
vector<int>::const iterator c iter=vec1.begin();//获取const类型迭代器
vec1.clear();//清空元素
```

## 遍历

下标法

```c++
int length = vec1.size();
for(int i=0;i<lengthl;i++){
    cout<<vec1[i];
}
cout<<endl<<endl;
```

迭代器法

```c++
vector<int>::iterator iter=vec1.begin();
for(;iter!=vec1.end();iter++){
    cout<<*iter;
}
```

# Queue 队列

可以看作是数组实现的队列

提一下队列特点：先进先出

头文件：``#include <queue>``

### 使用方法

```c++
queue<int> q;
q.opo();//弹出队列第一个元素
q.push();//在队尾插入一个元素
q.empty();//判断队列是否为空
q.size();//获取队列长度
q.front();//返回队列第一个元素(最早，最先进入的元素)
q.back();//返回队列最后一个元素(最晚，最后进入的元素)
```



# Deque 双端队列

可以看作是数组实现的双端队列

头文件：``#include <deque>``

### 使用方法

```c++
deque<int> dq;
dq.pop_front();//在队首弹出一个元素
dq.pop_back();//在队尾弹出一个元素
dq.push_front();//在队首加入一个元素
dq.push_back();//在队尾加入一个元素
dq.size();//获取队列长度

//输出
for (pos = dq.begin(); pos != dq.end(); pos++)  
    printf("%d ", *pos);
```

# Priority_queue 优先队列

可以看作是最大堆/优先队列

这里稍微重点一下(因为老师花的时间比较多)

头文件：`` #include <queue>``

定义：``priority_queue<Type, Container, Functional>``

```c++
// 下面两种优先队列的定义是等价的
priority_queue<int> q;
priority_queue<int,vector<int>,less<int> >q;

//如果想要把最小的放在第一位，则只需要
priority_queue<int,vector<int>,greater<int> >q;
```

> less表示数字大的优先级高(队头),greater表示数字小的优先级高

队头元素等价于优先级最高的元素

### 使用方法

```c++
priority_queue<int> a;
a.top();//访问队头元素
a.empty();//判断是否为空
a.size();//返回堆的大小
a.push();//插入元素
a.pop();//删除队头元素
a.swap(b);//交换队列
```

### 结构体的优先级设置

来源于CSDN作者琦小虾的文章:[链接](https://blog.csdn.net/ajianyingxiaoqinghan/article/details/79734532)

### 基本类型例子

上课记得笔记找不到了..这边引用了CSDN作者吕白_的文章:[链接](https://blog.csdn.net/weixin_36888577/article/details/79937886)  O～O

```c++
#include<iostream>
#include <queue>
using namespace std;
int main() 
{
    //对于基础类型 默认是大顶堆
    priority_queue<int> a; 
    //等同于 priority_queue<int, vector<int>, less<int> > a
    //                这里一定要有空格，不然成了右移运算符↓
    priority_queue<int, vector<int>, greater<int> > c;  //这样就是小顶堆
    priority_queue<string> b;
    for (int i = 0; i < 5; i++){
        a.push(i);
        c.push(i);
    }
    while (!a.empty()) {
        cout << a.top() << ' ';
        a.pop();
    } 
    cout << endl;
    while (!c.empty()) {
        cout << c.top() << ' ';
        c.pop();
    }
    cout << endl;
    b.push("abc");
    b.push("abcd");
    b.push("cbd");
    while (!b.empty()) 
    {
        cout << b.top() << ' ';
        b.pop();
    } 
    cout << endl;
    return 0;
}

//输出:
//4 3 2 1 0
//0 1 2 3 4
//cbd abcd abc
```

### pair的比较

先比较第一个，再比较第二个

```c++
#include <iostream>
#include <queue>
#include <vector>
using namespace std;
int main() 
{
    priority_queue<pair<int, int> > a;
    pair<int, int> b(1, 2);
    pair<int, int> c(1, 3);
    pair<int, int> d(2, 5);
    a.push(d);
    a.push(c);
    a.push(b);
    while (!a.empty()) 
    {
        cout << a.top().first << ' ' << a.top().second << '\n';
        a.pop();
    }
}

//输出：
//2 5
//1 3
//1 2
```

### 自定义类型例子

```c++
#include <iostream>
#include <queue>
using namespace std;

//方法1
struct tmp1 //运算符重载<
{
    int x;
    tmp1(int a) {x = a;}
    bool operator<(const tmp1& a) const
    {
        return x < a.x; //大顶堆
    }
};

//方法2
struct tmp2 //重写仿函数
{
    bool operator() (tmp1 a, tmp1 b) 
    {
        return a.x < b.x; //大顶堆
    }
};

int main(){
    tmp1 a(1);
    tmp1 b(2);
    tmp1 c(3);
    priority_queue<tmp1> d;
    d.push(b);
    d.push(c);
    d.push(a);
    while (!d.empty()){
        cout << d.top().x << '\n';
        d.pop();
    }
    cout << endl;
    priority_queue<tmp1, vector<tmp1>, tmp2> f;
    f.push(c);
    f.push(b);
    f.push(a);
    while (!f.empty()) 
    {
        cout << f.top().x << '\n';
        f.pop();
    }
}
//输出:
//3
//2
//1
//
//3
//2
//1
```

### 打印堆中元素

```c++
while(!q.empty()){
    cout<<q.top()<<endl;
    q.pop();
}
```

### 其他

```c++
int main(){
    std::priority_queue<int> q;
    for(int n :{1,8,5,6,3,4,0,9,7,2}) q.push(n);
    print_queue(q);
    std::priority_queue<int,std::vector<int>,std::greater<int>> q2;
    for(int n : {1,8,5,6,3,4,0,9,7,2}) q2.push(n);
    print_queue(q2);
    //用lambda比较元素
    auto cmp=[](int left,int right){return (left^1)<(right^1);};
    for(int n:{1,8,5,6,3,4,0,9,7,2}) q3.push(n);
    print_queue(q3);
}
```

# Set 堆

可以看作是维护的有序数组，可以用作堆

头文件：``#include <set>``

一个元素只能出现一次，如果要出现多次需要用到multiset

定义：``set<int> s;``

### 常用操作

```c++
s.begin(); //返回指向第一个元素的迭代器
s.end(); //返回指向最后一个元素的迭代器
s.insert(); //插入一个元素
s.erase(a); //删除集合中键值为a的元素
s.size(); //尺寸
s.count();//返回某个值元素的个数
s.empty();//是否为空
s.swap();//交换2个集合变量
s.upper_bound();//返回大于某个值元素的迭代器
s.lower_bound();//返回小于某个值元素的迭代器

//迭代器使用方法同上面

set<int>::iterator iter=s.begin();
for(;iter!=s.end();iter++){
    cout<<*iter;
}
```

# Bitset

方便的进行位运算,常用来处理二进制位的有序集

头文件：``#include <bitset>``

每个位可能包含0或者1，也可以当作**false**和**true**

定义：``bitset<40> b;``
