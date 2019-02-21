# 容器：deque

![image](https://note.youdao.com/yws/api/personal/file/BB66BE508F9B4D97A0113B950B6FC52B?method=download&shareKey=321179cf47f1b0e4eec356fc06cddb3a)

- deque则是一种双向开口的连续线性空间，vector容器是单向开口的连续内存空间；vector容器也可以在头尾两端插入元素，但是在其头部操作效率奇差，无法被接受；
- 和vector容器最大的差异：

    ```C++
    1. deque允许使用常数项时间对头端进行元素的插入和删除操作；
    2. deque没有capacity的概念，因为它是动态的以分段连续空间组合而成，随时可以增加一段新的空间并链接起来。
    Addition：
    1. 像vector那样，”旧空间不足而重新配置一块更大空间，然后复制元素，再释放旧空间”，这样的事情在deque身上是不会发生的；
    2. deque没有必须要提供所谓的空间保留(reserve)功能。
    ```

- 缺点：
    ```C++
    1. deque提供的迭代器复杂度超级高，vector的迭代器跟它不是一个量级；
    Addition： 
    1. deque进行的排序操作，为了最高效率，可将deque先完整的复制到一个vector中，对vector容器进行排序，再复制回deque.
    ```

- 实现原理

  ```C++
  1. 从逻辑上看，deque容器是连续的空间；
  2. 其实是由一段一段的定量的连续空间构成；一旦有必要在deque前端或者尾端增加新的空间，便配置一段连续定量的空间，串接在deque的头端或者尾端；
  3. deque最大的工作就是维护这些分段连续的内存空间的整体性的假象，并提供随机存取的接口；避开了重新配置空间，复制，释放的轮回；代价就是复杂的迭代器架构；
  4. 有中央控制器去维持缓冲区整体连续的假象；缓冲区是deque的存储空间的主体；
  ```

![image](https://note.youdao.com/yws/api/personal/file/95CD13D084F64EAD80FB6FA32DCECCEB?method=download&shareKey=75b744474a636531c311d22a6039237c)

- 1.构造函数、赋值、大小、双端插入、双端删除、读取、插入、删除

  ```C++
  #include <deque>
  
  void printDeque(deque<int> & dt)
  {
  	// iterator ：普通迭代器；reverse_iterator:倒叙迭代器；const_iterator:只读迭代器
  	for (deque<int>::const_iterator it = dt.begin(); it != dt.end(); it++)
  	{
  		// *it = 10;	// 报错，const_iterator，只能读，，不能修改
  		cout << *it << " ";
  	}
  	cout << endl;
  }
  
  void test1()
  {
  	// 1.构造函数(下面几乎个vector一样）
  	int arr[] = { 1,2,3,4,5 };
  	deque<int> dt1;
  	deque<int> dt2(arr, arr + 2);	// 1 2
  	printDeque(dt2);
  	deque<int> dt3(3, 7);
  	printDeque(dt3);		// 7 7 7
  	
  	// 2. 赋值(下面几乎和vector一样）
  	deque<int> dt4;
  	dt4.assign(dt2.begin(), dt2.end());
  	printDeque(dt4);		// 1 2
  	deque<int> dt5;
  	dt5.assign(5, 2);
  	printDeque(dt5);		// 2 2 2 2 2 
  	dt5.swap(dt4);
  	printDeque(dt5);		// 1 2
  
  	// 3. 大小操作(下面几乎个vector一样）
  	cout << dt5.size() << endl;	// 2
  	cout << dt5.empty() << endl;// 0(非空）
  	dt5.resize(5);
  	printDeque(dt5);		// 1 2 0 0 0
  	dt5.resize(3);
  	printDeque(dt5);		// 1 2 0
  	dt5.resize(6,9);
  	printDeque(dt5);		// 1 2 0 9 9 9
  
  	// 4. 双端插入、删除
  	deque<int> dt6;
  	dt6.push_back(1);	// 在容器尾部添加一个数据
  	dt6.push_back(3);
  	dt6.push_front(2);	// 在容器头部插入一个数据
  	printDeque(dt6);
  	dt6.pop_back();		// 删除容器最后一个数据
  	printDeque(dt6);
  	dt6.pop_front();	// 删除容器第一个数据
  	printDeque(dt6);
  
  	// 5.读取
  	deque<int> dt7;
  	for (int i = 0;i < 7;i++)
  	{
  		dt7.push_back(i+10);
  	}
  	printDeque(dt7);	// 10 11 12 13 14 15 16
  	cout << dt7.at(2) << endl;	// 返回索引idx所指的数据，如果idx越界，抛出out_of_range
  	cout << dt7.front() << " " << dt7.back() << endl;	// front():第一个数据, back():最后一个数据
  
  	// 6.插入
  	printDeque(dt7);				// 10 11 12 13 14 15 16
  	dt7.insert(dt7.begin() + 2, 90);
  	printDeque(dt7);				// 10 11 90 12 13 14 15 16
  	dt7.insert(dt7.begin(), 2, 3);
  	printDeque(dt7);				// 3 3 10 11 90 12 13 14 15 16
  	deque<int> dt8;
  	for (int i = 0;i < 7;i++)
  	{
  		dt8.push_back(i + 100);
  	}
  	printDeque(dt8);		// 100 101 102 103 104 105 106
  	dt7.insert(dt7.begin() + 3, dt8.begin() + 1, dt8.begin() + 5);	// 在pos位置插入[beg,end)区间的数据，前闭后开
  	printDeque(dt7);		// 3 3 10 101 102 103 104 11 90 12 13 14 15 16
  
  	// 7. 删除
  	printDeque(dt7);		// 3 3 10 101 102 103 104 11 90 12 13 14 15 16
  	dt7.erase(dt7.begin() + 2, dt7.begin() + 5);	// 删除[beg,end)区间的数据
  	printDeque(dt7);		// 3 3 103 104 11 90 12 13 14 15 16
  	dt7.erase(dt7.begin() + 2);
  	printDeque(dt7);		// 3 3 104 11 90 12 13 14 15 16
  	dt7.clear();	// 移除所有数据
  }
  ```

- 排序

  ```c++
  bool myCompare(int a, int b)
  {
  	return a > b;	// 如果 a>b 返回 true，表明是由大到小排序
  }
  
  // 排序
  void test2()
  {
  	deque<int> d;
  	d.push_back(2);
  	d.push_back(1);
  	d.push_back(7);
  	d.push_back(5);
  
  	printDeque(d);		// 2 1 7 5
  
  	// sort()默认从小到大排序。使用系统自带函数需要加上 #include <algorithm>
  	sort(d.begin(), d.end());	// 写上需要排序的区间
  	printDeque(d);		// 1 2 5 7
  
  	// 可以自行控制它由大到小排序
  	sort(d.begin(), d.end(), myCompare);
  	printDeque(d);		// 7 5 2 1
  }
  ```


- 其它：[随机数](https://blog.csdn.net/suifenghahahaha/article/details/71480865)

    ```c++
    #include <ctime>
    void test1()
    {
        int a = rand() % 41 + 60;   // 60-100
        cout << a;
    }
    
    int main() 
    {
        srand((unsigned)time(NULL));
        test1();
        return 0;
    }
    ```









