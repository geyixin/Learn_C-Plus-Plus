# 容器：vector
- 与array很像，区别在于vector会动态增加空间大小；
- 所谓动态增加大小，并不是在原空间之后续接新空间(因为无法保证原空间之后尚有可配置的空间)，而是开辟一块更大的内存空间，然后将原数据拷贝新空间，并释放原空间。因此，对vector的任何操作，**一旦引起空间的重新配置，指向原vector的所有迭代器就都失效了**(这一点务必注意)；
- vector支持随机存取，因为它提供的是随机访问迭代器；普通指针不支持随机存取；
- 一个vector的capacity()永远大于或等于其size()，一旦capacity()等于size()，便是满载，下次再有新增元素，整个vector容器就重新开辟新的空间；
- 
  ![image](https://note.youdao.com/yws/api/personal/file/252589496A0F4AF380CCE84D0497D4F0?method=download&shareKey=565c7f1270c24bd6f48d903a52ea0391)
- v.begin()和v.end()的位置：
  ![vector的begin和end的位置](https://note.youdao.com/yws/api/personal/file/8B006AA9436E480BA68EE7734EEEABDC?method=download&shareKey=284f8dbcf63b6cf4d4cd54167024c2a9)
- 看看vector的capacity是如何扩的：

```C++
#include <vector>

void test1()
{
	vector<int> v;
	for (int i = 0; i < 10;i++)
	{
		v.push_back(i);
		cout << v.capacity() << " ";
	}
}

// 输出：1 2 3 4 6 6 9 9 9 13   毫无规律
```

### 1.构造函数 、常用赋值 、大小操作、存取、插入、删除、位置预留

```C++
#include <vector>
#include <list>

void printVector(vector<int> & v)
{
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++)
	{
		cout << *it << " ";
	}
	cout << endl;
}

void test1()
{
	// 1. 构造函数 和 初始化
	vector<int> v1;		// 默认构造方法
	int arr[] = { 2,3,4,1,9 };
	vector<int> v2(arr, arr + sizeof(arr) / sizeof(int));
	vector<int> v3(v2.begin(), v2.end());	// v2的元素拷给v3
	printVector(v2);	// 2 3 4 1 9
	printVector(v3);	// 2 3 4 1 9
	vector<int> v4(3, 5);	// 将n个elem拷贝给本身
	printVector(v4);		// 5 5 5

	// 2. 常用赋值
	vector<int> v5;
	v5.assign(v4.begin(),v4.end());	// 5 5 5
	printVector(v5);
	vector<int> v6;
	v6.assign(4, 6);				// 6 6 6 6
	printVector(v6);
	v6.swap(v5);		// 交换
	printVector(v6);				// 5 5 5

	// 3. 大小操作
	cout << v6.size() << endl;		// 3
	cout << v6.empty() << endl;		// 0(非空为0，空为1）
	v6.resize(10);		// 重新指定容器的长度为num，若容器变长，则以默认值填充新位置
	printVector(v6);				// 5 5 5 0 0 0 0 0 0 0
	v6.resize(7);		// 如果容器变短，则末尾超出容器长度的元素被删除
	printVector(v6);				// 5 5 5 0 0 0 0
	v6.resize(8, 2);	// 容器变长，则以自定义的2填充
	printVector(v6);				// 5 5 5 0 0 0 0 2
	cout << v6.capacity()<< endl;	// 10（没错，就是10）

	// 4. 存取操作
	// v6：5 5 5 0 0 0 0 2
	cout << v6.at(7) << endl;		// 2  返回索引idx所指的数据，如果idx越界，抛出out_of_range异常
	cout << v6.front() << endl;		// 5  返回第一个元素
	cout << v6.back() << endl;		// 2  返回最后一个元素

	// 5. 插入 删除
	vector<int> v7;
	v7.push_back(1);				// 尾部插入元素
	v7.push_back(2);
	v7.push_back(3);
	printVector(v7);				// 1 2 3
	v7.insert(v7.begin()+1, 3, 10);	// 从迭代器指向位置处插入3个值为10的元素
	printVector(v7);				// 1 10 10 10 2 3
	v7.insert(v7.begin(), 5);		// 5 1 10 10 10 2 3  只写两个参数，默认开头插入1个值5的元素
	printVector(v7);
	v7.pop_back();					// 尾部删除元素
	printVector(v7);				// 5 1 10 10 10 2
	v7.erase(v7.begin() + 1, v7.end()-1);// 删除指定范围内的元素
	printVector(v7);				// 5 2
	v7.clear();						// 删除所有元素
	printVector(v7);
	
    // 6. 位置预留
	vector<int> v8;
	// v.reserve(int len);	//容器预留len个元素长度，预留位置不初始化，元素不可访问
	v8.reserve(100000);		// 如果预留的空间大于等于下面要放的空间，那只需开辟一次空间
	int *p = NULL;
	int num = 0;
	for (int i = 0; i < 100000; i++)
	{
		v8.push_back(1);
		if (p != &v8[0])
		{
			p = &v8[0];
			num++;
		}
	}
    // 如果上面第72行不加v8.reserve(100000) 输出：30， 加，输出 1
	cout << "100000数据开辟了 " << num << " 次空间。" << endl;
}
```



### 2. swap 收缩内存空间
```C++
// swap 收缩内存空间
void test2()
{
	vector<int> v1;
	for (int i = 0; i < 10000; i++)
	{
		v1.push_back(1);
	}
	cout << "capacity: " << v1.capacity() << endl;		// 12138
	cout << "size: " << v1.size() << endl;				// 10000

	v1.resize(10);		// 现在只需要前10个数字
	
	cout << "capacity: " << v1.capacity() << endl;		// 12138  容量依旧是之前的那么大，显然不行
	cout << "size: " << v1.size() << endl;				// 10
	
	// vector<int>(v1)：利用v1初始化匿名对象，对象的大小/capacity都为v1的size；
	// .swap(v1)，用匿名对象与原v1交换，那现在的v1的capacity变为匿名对象的capacity
	vector<int>(v1).swap(v1);	
	
	cout << "capacity: " << v1.capacity() << endl;		// 10
	cout << "size: " << v1.size() << endl;				// 10
}
```

### 3.逆序访问

```C++
// 逆序访问
void test3()
{
	vector<int> v;
	for (int i = 0;i < 10;i++)
	{
		v.push_back(i);
	}

	// 1. 要用逆序迭代器：reverse_iterator
    // 2. 用rbegin()和rend()，vector图中有rbegin()和rend()位置图。
	for (vector<int>::reverse_iterator it = v.rbegin(); it != v.rend();it++)
	{
		cout << *it << " ";
	}
}
```

### 4. 跳跃式访问

```C++
// 跳跃式访问
void test4()
{
	vector<int> v;
	vector<int>::iterator it = v.begin();
	it++;			// 正常
	it = it + 2;	// 没报错表明此迭代器是随访问迭代器，支持跳跃式访问

	list<int> LL;	// 开头加上 #include <list>
	list<int>::iterator Ll = LL.begin();
	Ll++;			// 正常
	// Ll = Ll + 2;	// 报错。表明此迭代器不是随访问迭代器，不支持跳跃式访问
}
```


























