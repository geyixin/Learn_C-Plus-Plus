# 容器：queue

- queue是一种先进先出(First In First Out,FIFO)的数据结构，它有两个出口，queue容器允许从一端新增元素，从另一端移除元素；
- 只有queue的顶端元素，才有机会被外界取用；
- queue不提供遍历功能，也不提供迭代器；

![image](https://note.youdao.com/yws/api/personal/file/6851F15B50B34024952EDBC37CCAA296?method=download&shareKey=3cc1f5ddedd73ac7acf04ff1201406e1)

- push()、swap()、size()、front()、back()、empty:
```C++
#include <queue>

void test()
{
	queue<int> q; 
	q.push(1);
	q.push(3);
	q.push(2);
	queue<int> q2;
	q2.push(10);
	q2.push(30);
	q2.push(20);
	q2.push(50);

	q.swap(q2);

	while (q.size() != 0)
	{
		cout << "队头： " << q.front() << endl;		//10	30	20	50
		cout << "队尾： " << q.back() << endl;		//50	50	50	50
		q.pop();	// 弹出队头		依次弹出：10	30	20	50
	}
	cout << endl;
	if (q.empty())
		cout << "空" << endl;
}

int main() 
{
	test();
	return 0;
}
```