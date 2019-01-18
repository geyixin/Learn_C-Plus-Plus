# 容器：string

- 赋值/初始化、存取、拼接、查找、替换、比较、查找子串、删除、插入、string与 const char * 的相互转化、内存地址占用、大小写互转

### 1. 赋值/初始化

```C++
// string初始化/赋值
void test1()
{
	string str1;				// 默认构造
	string str2(str1);			//拷贝构造
	string str3 = str1;			// 拷贝构造
	string str4 = "asdg";
	string str5(10, 'a');		// 使用10个a初始化
	cout << str5 << endl;		// aaaaaaaaaa

	str1= "hello";
	
	str2.assign("asdfg", 3);	// 前3个字符赋给str2
	cout << str2 << endl;		// asd

	string str6;
	str6.assign("asdfghj", 1, 5);	// 1和5，都闭；从0索引。
	cout << str6 << endl;			// sdfgh
}
```

### 2. 读取

```C++
// string 存取
void test2()
{
	string str1 = "hello world";
	for (int i = 0; i < str1.size(); i++)
		// cout << str1[i] << endl;		// 越界时候会直接挂掉
		cout << str1.at(i) << endl;		// 越界时候会捕获异常
	}

	try
	{
		// cout << str1[100] << endl;	// 直接崩掉
		cout << str1.at(100) << endl;	// 捕获异常
	}
	catch (out_of_range & e)
	{
		cout << e.what() << endl;		// invalid string position
	}
}
```

### 3. 拼接

```C++
// string 拼接
void test3()
{
	string s1 = "我";
	string s2 = "爱你";
	s1 += s2;
	cout << s1 << endl;		// 我爱你
	s1.append("，中国");
	cout << s1 << endl;		// 我爱你，中国
}
```

### 4. 查找  AND  替换

```C++
// string 查找 替换
void test4()
{
	string s = "asdfgaqwe";

	int pos3 = s.find('M');
	cout << pos3 << endl;	// -1 元素没找到

	int pos = s.find('a');
	cout << pos << endl;	// 0 返回a出现的第一个位置
	int p = s.rfind('a');	// rfind 从右边开始查找，find从左边开始查找
	cout << p << endl;		// 5 从右边查找a出现的第一个位置(注意：左边第一个元素位置index仍为0）

	int pos2 = s.find("df");
	cout << pos2 << endl;	// 2 find，返回第一个元素d的位置
	int p2 = s.rfind("df");
	cout << p2 << endl;		// 2 rfind，返回最后一个元素f的位置

	string s1 = "zxcvbnqwer";
	string s3 = "zxcvbnqwer";
	string s2 = "JKLMNB";
    // s1.replace(pos_a, num_b, s2) ,从位置pos_a(包括pos_a)开始，连续将原num_b个字符替换成字符串s2。	
    /*
    replace似乎分3步：
    1. 删除s1的连续num_b个字符(从位置pos_a开始);
    2. 将删除的元素位置留出s2.size()个位置;
    3. 把s2元素放上去.
    */
	s1.replace(2,4,s2);
	s3.replace(2, 4, "L");
	cout << s1 << endl;		// zxJKLMNBqwer
	cout << s3 << endl;		// zxLqwer
}
```

### 5. 比较

```C++
// string比较
void test5()
{
	string s1 = "abd";
	string s2 = "abc";
	int c = s1.compare(s2);		// c>0
	if (c == 0)
	{
		cout << "s1相等s2" << endl;
	}
	else if (c < 0)
	{
		cout << "s1小于s2" << endl;
	}
	else
	{
		cout << "s1大于s2" << endl;
	}
}
```

### 6. 查找子串

```C++
// string 查找子串
// s1.substr(pos, n)返回由pos开始的n个字符组成的字符串，[pos,pos+n-1]
void test6()
{
	// 固定查找
	string s1 = "abcdefgh";
	cout << s1.substr(2, 5) << endl;	// cdefg

	// 动态查找个人邮箱名字（@前面字符）
	string my_email = "asdfghj@qq.com";
	int p = my_email.find('@');
	string res = my_email.substr(0, p);
	cout << p << " " << res << endl;	// 7 asdfghj
}
```

### 7. 删除 AND 插入

```C++
// string 删除 插入
void test7()
{
	// 插入
	string s1 = "abcdef";
	s1.insert(2, "345");
	cout << s1 << endl;		// ab345cdef

	// 删除：s1.erase(int pos, int n = npos);//删除从Pos开始的n个字符
	s1.erase(2, 3);
	cout << s1 << endl;		// abcdef
}
```

### 8. string 与 const  char  *  的相互转化探究

```C++
// string与 const char * 的相互转化
/*
1. string想转化成 const char * 类型，只能通过s.c_str()，无法进行隐士转换；
2. const char * 可以隐士转换成 string，如：string s(ch)；
*/
void func1(string s)
{ cout << s << endl; }

void func1(char * ch)
{ cout << ch << endl; }

void test8()
{
	string s = "abc";
	const char * ch = s.c_str();	// string 转 const char * 
	cout << ch << endl;				// abc
	func1(ch);						// abc		const char * 可以隐士转换为 string
	string s2(ch);					// const char *  转 string
	cout << s2 << endl;				// abc			
	// func2(s2);					// 报错！	string 无法隐士转换为 const char *
}
```

### 9. 内存地址引用

```C++
// string 内存地址引用
// &出现在变量声明语句中，位于变量左边时，表示引用;
// &在给变量赋初值时，出现在等号右边或在执行语句中作为一元运算符出现时，都表示取对象的地址；
void test9()
{
	string s = "abcd";
	char & ch1 = s[1];	// 引用
	char & ch2 = s[2];

	ch1 = 'm';
	ch2 = 'n';

	cout << s << endl;		// amnd	，可见原来s[1]，s[2]位置的元素被修改了
	cout << (int*)s.c_str() << endl;	// 首字符地址：000000879B94F840
    cout << &s << endl;		// 000000879B94F838	

	s = "qqqqqqqqqqqqqqqqqqqqqqqqqqqqqq";
	cout << (int*)s.c_str() << endl;	// 首字符地址：00000206A5BBD0D0，变了
    cout << &s << endl;		// 000000879B94F838， 没变

	// 下面这个操作将无法进行。
	// ch1 = 'J';	// 出错！会停止运行。
	// 因为原来比较短的s的空间已经容不下新的比较长的s了，因而系统自动开辟了一个新的空间，
	//		那ch1原来指向的地址就没有了，没有地址自然也就没法赋值，如果赋值，肯定出错。
}
```

### 10. 字符大小写互转

```C++
// string 字符大小写互转
void test10()
{
	string s = "abcEFk";
	for (int i = 0; i < s.size(); i++)
	{
		s[i] = toupper(s[i]);		// 变大写	ABCEFK
		// s[i] = tolower(s[i]);	// 变小写
	}
	cout << s << endl;
}
```
