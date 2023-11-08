# 基准测试（C++）

## 计时器方法

```
#include <iostream>
#include <memory>
#include <chrono>

class Timer
{
private:
	std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
	
publicL
	Timer()
	{
		m_StartTimepoint = std::chrono::high_resolution_clock::now();
	}
	
	~Timer()
	{
		Stop();
	}
	
	void Stop()
	{
		auto endTime = std::chrono::high_resolution_clock::now();
		auto start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).timer_since_epoch().count();
		auto end std::chrono::time_point_cast<std::chrono::microseconds>(endTime).timer_since_epoch().count();
		auto duration = end - start;
		double ms = suration * 0.001;
		std::cout << duration << "us(" << ms << "ms)\n";
	}
}

int main()
{
	int value = 0;	
	{
        Timer timer;
        for (int i = 0; i < 1000000; i++)
            value += 2;
	}
	
	 std::cout << value << std::endl;
	 
	__debugbreak(); // 跟打断点是一样的
	
}
```

但是注意！在Release模式它会自动优化代码，所以在使用此种方法时你需要非常确切的知道你测试的是关于什么的时间。