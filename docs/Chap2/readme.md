# <font size=6>Design Parttern</font>

### Singleton mode (C++ version)

> #### Motivation

    Ensure that there is only one instance of a class and provide a global access point to that instance. 
    ——————"Design Patterns" GoF


In software systems, there are often such special classes, and it is necessary to ensure that they only have one instance in the system in order to ensure their logical correctness and good efficiency.

So we have to consider how to bypass the conventional constructor (users are not allowed to create an object) to provide a mechanism to ensure that there is only one instance of a class

> Version 0

```cpp
class Singleton
{
public:
    static Singleton *getInstance()
    {

        // first check if the object exists

        if (m_instance == nullptr)
        {
            m_instance = new Singleton();
        }

        return m_instance;
    }
private:
    Singleton(); //Private constructor, users are not allowed to generate objects themselves

    Singleton(const Singleton &other);

    static Singleton *m_instance; //Static member variable
};

Singleton *Singleton::m_instance = nullptr; //Static members need to be initialized first

```

This is the most classic implementation of the singleton pattern,

    1. Make the constructor and copy constructor private, and use a lazy initialization method,

    2. The object(Instance) will be generated when getInstance() is called for the first time, 

    3. The object will not be generated if it is not called, and it does not occupy memory.


However, in the case of multiple threads, this method is unsafe.

Analysis: 

    1. Under normal circumstances, if thread A calls getInstance(), m_instance is initialized, 
    then when thread B calls getInstance() again, it will no longer execute new, 
    and will directly return to the previously constructed object.

    2. However, in this case, the execution of m_instance = new Singleton() in thread A has not been completed yet. 
    At this time, m_instance is still nullptr. Thread B is also executing m_instance = new Singleton(), which will generate two objects. 
    Threads A and B may use the same object or two objects, which may cause program errors, and at the same time, memory leaks may occur.

Thus:

> Version 1

```cpp
// Guarantee thread safe, but the cost of the lock is vast high
class Singleton {
    // ...
public:
    static Singleton* getInstance() {
        Lock lock; // pseudocode form
        P(lock);

        if (m_instance == nullptr)
        {
            m_instance = new Singleton();
        }

        V(lock);
        return m_instance
    }    
}
```

Analysis: 

This way of writing will not cause the above two threads to execute new.

    When thread A is executing m_instance = new Singleton(), if thread B calls getInstance(), 
    it will definitely be blocked at the lock, and wait for the lock to be released after thread A finishes execution. It is thus thread-safe.

But the performance of this writing method is not high, because each call to getInstance() will lock and release the lock. And this step is only necessary in the first new Singleton(), as long as m_instance is created.

No matter how many threads are accessing at the same time, it is sufficient to use if (m_instance == nullptr) to judge (just read operation, no lock is needed), there is no thread safety problem, but there are performance problems after the lock is added.


In general, when a thread wants to access, lock it first, which will lead to unnecessary lock consumption.

So, can you judge whether the line: if (m_instance == nullptr) is satisfied first? If it is, it means that no lock is needed at all!

This is the so-called double-checked locking (DCL) idea, DCL is double-checked locking.

> Version 2

```cpp
// Double check lock, but it is not safe due to memory read and write reorder
class Singleton {
    // ...
public:
    static Singleton* getInstance() {
        Lock lock; // pseudocode form
        
        if (m_instance = new Singleton()) {
            P(lock);

            if (m_instance == nullptr)
            {
                m_instance = new Singleton();
            }

            V(lock);
        }
        return m_instance
    }    
}
```

Looks great! 
The lock will only be used the first time it is necessary, and then it will be the same as in implementation one.

For a long period of time, It confused a lot of people. It was only in 2000 that the bug was discovered, and it was discovered in every language. 
The reason is the out-of-order execution of memory read and write (compiler problem).

Analysis: 

The sentence m_instance = new Singleton() can be executed in three steps:

    i. The memory required by a Singleton type object is allocated.

    ii. Construct a Singleton type object at the allocated memory.

    iii. Assign the address of the allocated memory to the pointer m_instance.

You might think that these three steps are performed in sequence, but in fact, you can only determine that step 1 is the first to be performed, but steps 2 and 3 are not necessarily. The problem is here.

    1. If a thread A calls and executes m_instance = new Singleton() in the order of 1, 3, 2,

    2. Then just after performing step 3, memory is allocated to the Singleton type (m_instance is not a nullptr at this time) switch to thread B, because m_instance is no longer nullptr,

    3. So thread B will directly execute return m_instance to get an object, and this object is not really constructed ! ! !
    
    A serious bug just happened.

In the c++ 11 version, finally there is such a mechanism to help us realize a cross-platform solution

> Version 3

```cpp
// Cross-platform implementation after C++ 11 version

    // atomic Atomic operations provided in c++11
    std::atomic<Singleton*> Singleton::m_instance;  

    std::mutex Singleton::m_mutex;


/*
* std::atomic_thread_fence(std::memory_order_acquire); 
* std::atomic_thread_fence(std::memory_order_release);
* These two sentences can ensure that the statements between them will not be executed out of order.
*/
Singleton* Singleton::getInstance() {
    Singleton* tmp = m_instance.load(std::memory_order_relaxed);
    std::atomic_thread_fence(std::memory_order_acquire);//获取内存fence
    if (tmp == nullptr) {
        std::lock_guard<std::mutex> lock(m_mutex);
        tmp = m_instance.load(std::memory_order_relaxed);
        if (tmp == nullptr) {
            tmp = new Singleton;
            std::atomic_thread_fence(std::memory_order_release);//释放内存fence
            m_instance.store(tmp, std::memory_order_relaxed);
        }
    }
    return tmp;
}

```

Advantages of singleton mode:

- Since the singleton mode only generates one instance, the system performance overhead is reduced,

        1. When the generation of an object requires more resources, such as reading the configuration, when other dependencies are generated,
        
        2. You can directly generate a singleton object when the application starts,
        
        3. Then solve it by permanently resident in memory.

- Singleton mode can set global access points in the system to optimize access to shared resources in the ring,

        For example, a singleton class can be designed to be responsible for the mapping processing of all data tables

Three elements of singleton mode:

    (1) The class of singleton mode only provides private constructor,
    
    (2) A static private object of the class is included in the class definition,

    (3) This class provides a static public function for creating or obtaining its own static private object.


[Original Address](https://www.jianshu.com/p/edab2673ee6c)
