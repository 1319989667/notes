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



# 利用Chrome://tracing 的可视化计时器测试

打开`Chrome://tracing`，，并用下面的方法生成json文件然后扔进网站中。

```C++
//
// Basic instrumentation profiler by Cherno

// Usage: include this header file somewhere in your code (eg. precompiled header), and then use like:
//
// Instrumentor::Get().BeginSession("Session Name");        // Begin session 
// {
//     InstrumentationTimer timer("Profiled Scope Name");   // Place code like this in scopes you'd like to include in profiling
//     // Code
// }
// Instrumentor::Get().EndSession();                        // End Session
//
// You will probably want to macro-fy this, to switch on/off easily and use things like __FUNCSIG__ for the profile name.
//
#pragma once

#include <string>
#include <chrono>
#include <algorithm>
#include <fstream>

#include <thread>

struct ProfileResult
{
    std::string Name;
    long long Start, End;
    uint32_t ThreadID;
};

struct InstrumentationSession
{
    std::string Name;
};

class Instrumentor
{
private:
    InstrumentationSession* m_CurrentSession;
    std::ofstream m_OutputStream;
    int m_ProfileCount;
public:
    Instrumentor()
        : m_CurrentSession(nullptr), m_ProfileCount(0)
    {
    }

    void BeginSession(const std::string& name, const std::string& filepath = "results.json")
    {
        m_OutputStream.open(filepath);
        WriteHeader();
        m_CurrentSession = new InstrumentationSession{ name };
    }

    void EndSession()
    {
        WriteFooter();
        m_OutputStream.close();
        delete m_CurrentSession;
        m_CurrentSession = nullptr;
        m_ProfileCount = 0;
    }

    void WriteProfile(const ProfileResult& result)
    {
        if (m_ProfileCount++ > 0)
            m_OutputStream << ",";

        std::string name = result.Name;
        std::replace(name.begin(), name.end(), '"', '\'');

        m_OutputStream << "{";
        m_OutputStream << "\"cat\":\"function\",";
        m_OutputStream << "\"dur\":" << (result.End - result.Start) << ',';
        m_OutputStream << "\"name\":\"" << name << "\",";
        m_OutputStream << "\"ph\":\"X\",";
        m_OutputStream << "\"pid\":0,";
        m_OutputStream << "\"tid\":" << result.ThreadID << ",";
        m_OutputStream << "\"ts\":" << result.Start;
        m_OutputStream << "}";

        m_OutputStream.flush();
    }

    void WriteHeader()
    {
        m_OutputStream << "{\"otherData\": {},\"traceEvents\":[";
        m_OutputStream.flush();
    }

    void WriteFooter()
    {
        m_OutputStream << "]}";
        m_OutputStream.flush();
    }

    static Instrumentor& Get()
    {
        static Instrumentor instance;
        return instance;
    }
};

class InstrumentationTimer
{
public:
    InstrumentationTimer(const char* name)
        : m_Name(name), m_Stopped(false)
    {
        m_StartTimepoint = std::chrono::high_resolution_clock::now();
    }

    ~InstrumentationTimer()
    {
        if (!m_Stopped)
            Stop();
    }

    void Stop()
    {
        auto endTimepoint = std::chrono::high_resolution_clock::now();

        long long start = std::chrono::time_point_cast<std::chrono::microseconds>(m_StartTimepoint).time_since_epoch().count();
        long long end = std::chrono::time_point_cast<std::chrono::microseconds>(endTimepoint).time_since_epoch().count();

        uint32_t threadID = std::hash<std::thread::id>{}(std::this_thread::get_id());
        Instrumentor::Get().WriteProfile({ m_Name, start, end, threadID });

        m_Stopped = true;
    }
private:
    const char* m_Name;
    std::chrono::time_point<std::chrono::high_resolution_clock> m_StartTimepoint;
    bool m_Stopped;
};
```

```
// main文件
#define PROFILING 1
#if PROFILING
#define PROFILE_SCOPE(name) InstrumentationTimer timer##__LINE__(name)  
#define PROFILE_FUNCTION() PROFILE_SCOPE(__FUNSIG__)
#else
#define PROFILE_SCOPE(name)
#endif

namespace Benchmark
{
    void Function()
    {
        PROFILE_FUNCTION()；
        for (int i = 0; i < 1000; i++)
            std::cout >> "Hello World #" << i << std::endl;
    }

    void RunBenchmarks()
    {
        std::cout << "Running Benchmarks...\n";
        std::thread a([]() {Function();});
        a.join();
    }
}

int main()
{
	Instrumentor::Get().BeginSession("Profile");
	Benchmark::RunBenchmarks();
	Instrumentor::Get().EndSession();
}
```

注：` __LINE__`就是唯一标识符的意思，`__FUNCTION__`是当前函数名，`__FUNCSIG__`是函数签名