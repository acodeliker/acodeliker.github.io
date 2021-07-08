# <font size=6>Design Parttern</font>

### Singleton mode (C++ version)

> #### Motivation

> Ensure that there is only one instance of a class and provide a global access point to that instance. 
> ——"Design Patterns" GoF


In software systems, there are often such special classes, and it is necessary to ensure that they only have one instance in the system in order to ensure their logical correctness and good efficiency.

So we have to consider how to bypass the conventional constructor (users are not allowed to create an object) to provide a mechanism to ensure that there is only one instance of a class
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

Make the constructor and copy constructor private, and use a lazy initialization method,

The object will be generated when getInstance() is called for the first time, and the object will not be generated if it is not called, and it does not occupy memory.


However, in the case of multiple threads, this method is unsafe.


Analysis: Under normal circumstances, if thread A calls getInstance(), m_instance is initialized,

Then when thread B calls getInstance() again, it will no longer execute new, and will directly return to the previously constructed object.


However, in this case, the execution of m_instance = new Singleton() in thread A has not been completed yet. At this time, m_instance is still nullptr.

Thread B is also executing m_instance = new Singleton(), which will generate two objects,

Threads A and B may use the same object or two objects, which may cause program errors, and at the same time, memory leaks may occur.