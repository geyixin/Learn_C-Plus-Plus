# 容器：stack

- stack是一种先进后出(First In Last Out,FILO)的数据结构，它只有一个出口；
- stack容器允许新增元素，移除元素，取得栈顶元素，但是除了最顶端外，没有任何其他方法可以存取stack的其他元素；
- stack不允许有遍历行为；
- 有元素推入栈的操作称为:push；将元素推出stack的操作称为pop；
- stack没有迭代器；

![image](https://note.youdao.com/yws/api/personal/file/86FCC5F37835423598BB021093B063AF?method=download&shareKey=ae2dc9ef89e376998a1eb76aee209984)

- push()、swap()、size()、pop()、top()、empty()：
```C++
#include <stack>

void test()
{
	stack<int> s; 
	s.push(1);
	s.push(3);
	s.push(2);
	stack<int> s2;
	s2.push(10);
	s2.push(30);
	s2.push(20);
	s2.push(50);

	s.swap(s2);

	while (s.size() != 0)
	{
		cout << s.top() << " ";		//50 20 30 10
		s.pop();
	}
	cout << endl;
	if (s.empty())
		cout << "空" << endl;       // 空
}

int main() 
{
	test();
	return 0;
}
```

