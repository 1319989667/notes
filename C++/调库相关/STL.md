# Vector

其实就是动态数组。跟数学的向量没半毛钱关系。

```
#include <iostream>
#include <string>
#include <vector>

struct Vertex
{
	float  x, y, z;
};

std::ostream& operator<< (std::ostream& stream, const Vertex& vertex)
{
	stream << vertex.x << ", " << vertex.y << ", " << vertex.z;
	return stream;
}

int main()
{
	std::vector<Vertex> vertices; 
	vertices.push_back({1, 2, 3});
	std::cin.get();
}
```

## 基本操作

用Vector直接存储对象而不是指针，迭代的时候在技术上是最优的。【如果经常改变数据的大小，那用对象就不太理想】

```
// 常见操作

// 添加
vertices.push_back({1, 2, 3});
// 清除
vertices.clear();
// 删除元素：参数是一个迭代器！ 如果我们想抹去第二个元素需要这么写
vertices.erase(vertices.begin() + 1);

// 遍历
for(Vectice& v : vertices)
{
	std::cout << v << std::endl;
}
```



## 优化

`Vector`的原理是：在添加时，如果超出目前的内存限制，就将内存中旧位置的所有内容复制到内存中的新位置。

我们不存储指针向量，基本是存储对象的向量。

```
int main()
{
	std::vector<Vertex> vertices; 
	vertices.push_back(Vertex(1, 2, 3));
	vertices.push_back(Vertex(4, 5, 6));
	vertices.push_back(Vertex(7, 8, 9));
	std::cin.get();
} // 调用了六次的拷贝构造函数
```

优化策略：

1. 如果事先已经知道需要插入多少元素（> 2），就先告诉Vector以避免在添加元素时将旧位置的所有内容复制到内存中的新位置。
2. 避免从main栈复制一次之后再复制到Vector里。 

```
// 法1
int main()
{
	std::vector<Vertex> vertices; 
	vertices.reserve(3); // 提前预定3个
	vertices.push_back(Vertex(1, 2, 3));
	vertices.push_back(Vertex(4, 5, 6));
	vertices.push_back(Vertex(7, 8, 9));
	std::cin.get();
} // 现在只调用了3次的拷贝构造函数了

// 在法1基础上进行法2
int main()
{
	std::vector<Vertex> vertices; 
	vertices.reserve(3); // 提前预定3个
	vertices.emplace_back(1, 2, 3); // 传递参数的初始化列表而不是拷贝对象
	vertices.emplace_back(4, 5, 6);
	vertices.emplace_back(7, 8, 9);
	std::cin.get();
}
```



# Array

## 基本操作

```
#include <iostream>
#include <array>

int main()
{
	std:;array<int, 5> data;
	data[0] = 2;
	int count = data.size();
}
```

跟常规数组相比的优点可以使用一些stl算法，已经达到了最大优化。

标准数组和普通数组都存储在栈上。

会做反弹调查

