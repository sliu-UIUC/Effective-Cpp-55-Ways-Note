# Item 5： Know what functions C++ silently writes and calls. 
Compilers will declare their own versions of a copy constructor, a copy assignment operator and a destructor, and even the contructor if you don't declare them 
yourself. All of them will be both puiblic and inline. Thus, writing ```C++ class Empty{}; ``` is equivalent to: 

```C++
class Empty {
public: 
  Empty(){...}                             // default constructor
  Empty(const Empty& rhs) {...}            // copy constructor
  ~Empty(){...}                            // destructor
  
  Empty& operator=(const Empty& rhs) {...} //copy assignment operator
};

```


```diff
- Things to Remember
```
* 
